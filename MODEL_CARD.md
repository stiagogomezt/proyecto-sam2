---
library_name: pytorch
license: apache-2.0
pipeline_tag: mask-generation
tags:
  - computer-vision
  - image-segmentation
  - video-segmentation
  - sam2
  - meta
  - model-card
---

# Ficha del Modelo (Model Card): SAM 2 (Segment Anything in Images and Videos)

Esta ficha técnica documenta el modelo **SAM 2** y su conjunto de datos asociado (**SA-V**), siguiendo la estructura propuesta por Mitchell et al. (2019) en *"Model Cards for Model Reporting"* y los estándares de documentación de *Hugging Face Hub*.

* **Autor de la ficha:** Kevin Santiago Gómez Tibaquira (Universidad Distrital Francisco José de Caldas)
* **Desarrolladores originales del modelo:** Meta AI (Fundamental AI Research - FAIR)

---

## 1. Detalles del Modelo (Model Details)

* **Nombre del modelo:** Segment Anything Model 2 (SAM 2)
* **Organización desarrolladora:** Meta AI (FAIR)
* **Fecha de lanzamiento original:** Julio de 2024 (Documentación actualizada a Octubre de 2026)
* **Versión y variantes del modelo:** `sam2_hiera_tiny` (38.9M params), `sam2_hiera_small` (46M params), `sam2_hiera_base_plus` (80.8M params) y `sam2_hiera_large` (224.4M params).
* **Tipo de modelo:** Modelo fundacional de visión por computadora para segmentación visual interactiva (*Promptable Visual Segmentation - PVS*) en imágenes estáticas y secuencias de video.
* **Arquitectura:**
  1. **Codificador de imágenes (*Image Encoder*):** Basado en un Vision Transformer jerárquico preentrenado con MAE (**Hiera**), que extrae características multiescala.
  2. **Codificador de indicaciones (*Prompt Encoder*):** Procesa entradas interactivas del usuario (puntos, cajas delimitadoras o máscaras).
  3. **Mecanismo de atención de memoria (*Memory Attention & Memory Bank*):** Almacena representaciones espaciales y semánticas de fotogramas previos para mantener la consistencia temporal y manejar oclusiones.
  4. **Decodificador de máscaras (*Mask Decoder*):** Genera las máscaras de segmentación finales y un puntaje de confianza de intersección sobre unión (IoU), además de predecir si el objeto está ocluido en el fotograma actual.
* **Licencia:** Apache 2.0 (Código y pesos abiertos para uso académico y comercial).
* **Artículo de referencia:** Ravi, N., Gabeur, V., Hu, Y. T., Hu, R., Ryali, C., Ma, T., ... & Feichtenhofer, C. (2024). *SAM 2: Segment Anything in Images and Videos*. arXiv preprint arXiv:2408.00714.

---

## 2. Uso Previsto (Intended Use)

* **Usos directos principales:**
  * Segmentación interactiva *zero-shot* de objetos arbitrarios en imágenes estáticas y videos.
  * Extracción automatizada de elementos en imágenes de sensores remotos, ortofotomosaicos, cartografía digital y monitoreo territorial.
  * Aceleración del etiquetado de conjuntos de datos visuales y generación de máscaras para visión por computadora.
* **Usuarios previstos:** Investigadores, científicos de datos, ingenieros en geomática/percepción remota y desarrolladores de software.
* **Usos fuera de alcance (Out-of-Scope):**
  * Vigilancia masiva, seguimiento biométrico no autorizado o aplicaciones que vulneren la privacidad de las personas.
  * Diagnóstico médico clínico autónomo o sistemas críticos de seguridad (ej. conducción autónoma sin supervisión) sin un proceso previo de *fine-tuning* y validación rigurosa de dominio.

---

## 3. Factores (Factors)

De acuerdo con Mitchell et al. (2019), el desempeño de SAM 2 puede verse afectado por los siguientes factores:
* **Factores ambientales y de captura:** Condiciones extremas de iluminación, desenfoque por movimiento rápido (*motion blur*), baja resolución espacial y compresión de video severa.
* **Complejidad de la escena:** Oclusiones prolongadas, presencia de múltiples objetos visualmente idénticos muy próximos entre sí (*crowded scenes*) y estructuras extremadamente finas o semitransparentes.
* **Cambio de dominio (*Domain Shift*):** Aunque posee gran capacidad *zero-shot*, la precisión en imágenes multiespectrales, radar (SAR) o sensores aéreos especializados puede variar respecto a fotografías RGB convencionales si no se proporcionan suficientes puntos de control (*prompts*).

---

## 4. Métricas de Evaluación (Metrics)

Para evaluar rigurosamente el desempeño tanto en imagen como en video, se emplean las siguientes métricas estándar:
* **$\mathcal{J}$ (Jaccard Index / IoU de región):** Mide el grado de superposición espacial entre la máscara predicha y la máscara real (*ground truth*).
* **$\mathcal{F}$ (F-Measure de contorno):** Evalúa la precisión espacial en la delimitación de los bordes del objeto.
* **$\mathcal{J}\&\mathcal{F}$ (Promedio de región y contorno):** Métrica principal compuesta ($\frac{\mathcal{J} + \mathcal{F}}{2}$) utilizada en benchmarks de segmentación de objetos en video (VOS).
* **mIoU (Mean Intersection over Union):** Utilizada para evaluar la calidad de segmentación en imágenes estáticas (con 1 a 5 clics interactivos).

---

## 5. Conjunto de Datos de Entrenamiento y Evaluación (Dataset Card)

### 5.1. Datos de Entrenamiento: SA-V (Segment Anything Video Dataset) y SA-1B
* **Descripción del dataset SA-V:** Es el conjunto de datos de segmentación de video más grande construido hasta la fecha, recolectado a través de un motor de datos iterativo (*Data Engine*) en tres fases con intervención humana (*human-in-the-loop*).
* **Volumen y composición:**
  * **Videos:** ~50,900 videos diversos capturados en 47 países.
  * **Anotaciones (Masklets):** ~642,600 *masklets* (trayectorias de máscaras espacio-temporales) que equivalen a **35.5 millones de máscaras individuales**.
  * **Complemento de imagen estática:** Entrenado conjuntamente con el dataset **SA-1B** (11 millones de imágenes y 1,100 millones de máscaras).
* **Preprocesamiento y privacidad:** Difuminado automático de rostros y placas vehiculares en todos los videos para proteger la identidad y privacidad de terceros.

### 5.2. Datos de Evaluación
* **Partición de validación/prueba SA-V:** Subconjunto curado con videos de alta complejidad, oclusiones y objetos dinámicos, estrictamente separado del conjunto de entrenamiento.
* **Benchmarks públicos externos (Zero-Shot):** Evaluación sin reentrenamiento sobre conjuntos estándar como **DAVIS 2017**, **MOSE**, **YouTube-VOS 2019** y **LVOS**, además de 37 conjuntos de datos de imágenes estáticas (SA-23).

---

## 6. Análisis Cuantitativo (Quantitative Analyses)

Rendimiento reportado de **SAM 2** frente a arquitecturas previas en evaluación *zero-shot* de video e imagen (métrica $\mathcal{J}\&\mathcal{F}$ para video y mIoU con 1 clic para imagen):

| Variante del Modelo | Parámetros | Velocidad (FPS) | DAVIS 2017 ($\mathcal{J}\&\mathcal{F}$) | MOSE ($\mathcal{J}\&\mathcal{F}$) | YouTube-VOS ($\mathcal{J}\&\mathcal{F}$) | SA-23 Imagen (1-clic mIoU) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **SAM 2 (Hiera-Tiny)** | 38.9 M | ~47.2 FPS | 88.9% | 75.6% | 87.5% | 61.3% |
| **SAM 2 (Hiera-Small)** | 46.0 M | ~43.3 FPS | 89.6% | 76.2% | 88.0% | 61.5% |
| **SAM 2 (Hiera-Base+)** | 80.8 M | ~34.8 FPS | 90.2% | 76.8% | 88.6% | 61.9% |
| **SAM 2 (Hiera-Large)** | 224.4 M | ~24.2 FPS | **90.7%** | **77.9%** | **89.3%** | **63.6%** |

*Nota: SAM 2 requiere hasta **3 veces menos interacciones humanas** que enfoques previos para alcanzar la misma calidad de segmentación en video y es **6 veces más rápido** que el SAM original en tareas de imagen estática.*

---

## 7. Consideraciones Éticas, Sesgos y Limitaciones (Ethical Considerations & Caveats)

* **Equidad geográfica y demográfica:** Aunque el dataset SA-V incluye videos de 47 países, existe una mayor representación de entornos urbanos y dispositivos de captura modernos. Meta realizó evaluaciones de equidad demográfica (edad, género percibido y tono de piel en la escala Monk) confirmando variaciones mínimas en la métrica $\mathcal{J}\&\mathcal{F}$, aunque se recomienda precaución al aplicar el modelo en contextos subrepresentados.
* **Limitaciones técnicas conocidas:**
  1. El modelo puede confundir instancias cuando varios objetos idénticos se cruzan rápidamente.
  2. No incorpora seguimiento inter-cámara ni razonamiento semántico de lenguaje natural por sí solo (no clasifica qué es el objeto, solo delimita su geometría).
  3. En videos extremadamente largos, la deriva temporal puede requerir un clic correctivo adicional por parte del usuario.

---

## 8. Referencias

1. **Mitchell, M., Wu, S., Zaldivar, A., Barnes, P., Vasserman, L., Hutchinson, B., Spitzer, E., Raji, I. D., & Gebru, T.** (2019). *Model cards for model reporting*. In Proceedings of the Conference on Fairness, Accountability, and Transparency (FAT* '19), pp. 220–229. https://arxiv.org/abs/1810.03993
2. **Ravi, N., Gabeur, V., Hu, Y. T., Hu, R., Ryali, C., Ma, T., Khedr, H., Rädle, R., Rolland, C., Gustafson, L., Mintun, E., Pan, J., Alwala, K. V., Carion, N., Wu, C. Y., Girshick, R., Dollár, P., & Feichtenhofer, C.** (2024). *SAM 2: Segment Anything in Images and Videos*. Meta AI (FAIR). https://arxiv.org/abs/2408.00714
3. **Hugging Face Hub Templates:** *Model Card Annotated Template*. https://github.com/huggingface/huggingface_hub/tree/main/src/huggingface_hub/templates
