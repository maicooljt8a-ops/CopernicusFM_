---
# Para referencia sobre los metadatos de las fichas de modelo, ver la especificación: https://github.com/huggingface/hub-docs/blob/main/modelcard.md?plain=1
# Documentación / guía: https://huggingface.co/docs/hub/model-cards
---

# Ficha del modelo Copernicus-FM

Copernicus-FM (Copernicus Foundation Model) es un modelo fundacional unificado para observación de la Tierra, preentrenado sobre el dataset Copernicus-Pretrain (18.7 millones de imágenes alineadas de misiones Sentinel). Puede procesar cualquier modalidad de sensor espectral o no espectral mediante hiperredes dinámicas extendidas y codificación flexible de metadatos, y fue evaluado en Copernicus-Bench, un conjunto de 15 tareas descendentes que cubren Sentinel-1, Sentinel-2, Sentinel-3 y Sentinel-5P.

## Detalles del modelo

### Descripción del modelo

Copernicus-FM es una extensión del modelo fundacional DOFA, capaz de procesar cualquier modalidad de sensor espectral o no espectral mediante hiperredes dinámicas extendidas y codificación flexible de metadatos. El modelo se preentrena sobre el dataset Copernicus-Pretrain con modelado de imágenes enmascarado (MIM) y destilación continua.

- **Desarrollado por:** Yi Wang, Zhitong Xiong, Chenying Liu, Adam J. Stewart, Thomas Dujardin, Nikolaos Ioannis Bountos, Angelos Zavras, Franziska Gerken, Ioannis Papoutsis, Laura Leal-Taixé y Xiao Xiang Zhu — Universidad Técnica de Múnich (TUM), Centro de Aprendizaje Automático de Múnich (MCML), Universidad Técnica Nacional de Atenas (NTUA), Observatorio Nacional de Atenas (NOA), Universidad Harokopio de Atenas y NVIDIA.
- **Financiado por [opcional]:** No reportado por los autores.
- **Compartido por [opcional]:** Los pesos del modelo, el dataset Copernicus-Pretrain, los datasets de Copernicus-Bench y el dataset de embeddings Copernicus-Embed-025deg están disponibles públicamente en Hugging Face (`wangyi111/Copernicus-FM`, `wangyi111/Copernicus-Pretrain`, `wangyi111/Copernicus-Bench`, `wangyi111/Copernicus-Embed-025deg`).
- **Tipo de modelo:** Modelo fundacional multimodal unificado de observación de la Tierra: un codificador Vision Transformer (ViT) cuyos núcleos de convolución de parcheo se generan dinámicamente mediante hiperredes conscientes del sensor (hiperred espectral para sensores con respuesta espectral; hiperred de variables para variables no espectrales), con metadatos (geolocalización, área de parche, tiempo) integrados como codificaciones Fourier añadidas a los tokens de parche.
- **Idioma(s) (NLP):** No aplica — el modelo procesa imágenes satelitales, no lenguaje natural. Los nombres de variables de entradas no espectrales se codifican offline en vectores de 128 dimensiones con un codificador Llama 3.2 congelado (embeddings guiados por lenguaje) antes de pasarlos a la hiperred de variables.
- **Licencia:** Pesos del modelo: CC-BY-4.0. Código: Licencia Apache 2.0, porciones de código de terceros bajo MIT y CC-BY-NC-4.0 (ver LICENSE y NOTICE en el repositorio oficial). Datasets (Copernicus-Pretrain y los datasets nuevos de Copernicus-Bench): CC-BY-4.0. Los datasets de benchmark obtenidos de otras fuentes conservan sus propias licencias (CC0-1.0, MIT, CDLA-Permissive-1.0, CC-BY-4.0).
- **Modelo base [opcional]:** Preentrenado desde cero sobre Copernicus-Pretrain con modelado de imágenes enmascarado estilo MAE más destilación continua de profesores congelados (DINOv2 para RGB derivado de Sentinel-2, SoftCon para Sentinel-1/2). La arquitectura extiende el diseño de hiperredes dinámicas de DOFA y la estructura codificador-decodificador de MAE.

### Fuentes del modelo [opcional]

- **Repositorio:** https://github.com/zhu-xlab/Copernicus-FM
- **Artículo [opcional]:** Wang et al., "Towards a Unified Copernicus Foundation Model for Earth Vision", ICCV 2025 (oral), arXiv:2503.11849 — v1 14 mar 2025, v2 29 mar 2025, v3 31 jul 2025 (última).
- **Demo [opcional]:** Notebook de demostración oficial `Copernicus-FM/demo.ipynb` (ejemplos de uso para entradas espectrales y no espectrales): https://github.com/zhu-xlab/Copernicus-FM/blob/main/Copernicus-FM/demo.ipynb

## Usos

### Uso directo

El modelo está diseñado para extracción de características de imágenes a partir de observaciones satelitales: dado un parche de imagen de cualquier modalidad de sensor (con su respuesta espectral o nombre de variable, y opcionalmente metadatos), produce mapas de características densos adecuados como base congelada o ajustada para tareas descendentes como clasificación y segmentación de uso y cobertura del suelo, detección de nubes, detección de cambios por inundación, clasificación de zonas climáticas locales, regresión de biomasa y regresión de calidad del aire (NO2, O3). El artículo demuestra el uso directo mediante clasificación k-NN y sondas lineales sobre codificadores congelados, sin ajuste específico de la tarea.

### Uso descendente [opcional]

Los codificadores de Copernicus-FM pueden ajustarse o usarse con cabezales de tarea para las 15 tareas jerárquicas de Copernicus-Bench: (L1, preprocesamiento) detección de nubes en Sentinel-2 y Sentinel-3 OLCI; (L2, aplicaciones base) clasificación de uso y cobertura del suelo (EuroSAT-S1/S2, BigEarthNet-S1/S2, LC100Cls-S3, LCZ-S2) y segmentación (DFC2020-S1/S2, LC100Seg-S3); (L3, aplicaciones especializadas) detección de cambios por inundación (Flood-S1, Kuro Siwo), regresión de biomasa (Biomass-S3) y regresión de calidad del aire (AQ-NO2-S5P, AQ-O3-S5P). El modelo también está pensado como proveedor de embeddings de celda para investigación meteorológica/climática (p. ej., el mapa global de embeddings Copernicus-Embed-025deg alineado con ERA5). Los autores también planean contribuir el modelo a bibliotecas de código abierto como TorchGeo.

### Usos fuera del alcance

- Misiones satelitales distintas a Sentinel: el modelo y sus datos de preentrenamiento están restringidos a la serie Sentinel (S1, S2, S3, S5P) más Copernicus DEM; la generalización a otras constelaciones está fuera del alcance de esta versión (limitación declarada).
- Rango temporal de aproximadamente un año: los datos de preentrenamiento se anclan alrededor del año 2021; el comportamiento fuera de ese período no está validado por los autores.
- Aplicaciones que requieren fusión multimodal nativa o procesamiento de series temporales dentro del modelo: los autores los identifican como trabajo futuro.
- Toma de decisiones operativas (p. ej., respuesta a inundaciones, alertas de calidad del aire) sin validación de dominio y datos de verdad terreno complementarios.

## Sesgos, riesgos y limitaciones

El artículo no reporta un análisis dedicado de equidad/sociotécnico; los puntos a continuación se derivan de lo que el artículo y el repositorio oficial sí reportan sobre cobertura de datos, resolución de sensores, alcance de evaluación y limitaciones declaradas.

**Sesgos.**
- Sesgo de muestreo geográfico: los parches de preentrenamiento de Sentinel-1/2 se muestrean con una estrategia Gaussiana (desviación estándar de 50 km) alrededor de las top-10K ciudades más pobladas, más 40K ubicaciones polares; las regiones densamente pobladas están sobre-representadas respecto a tierras deshabitadas y océanos (cobertura solo terrestre con buffer costero de 0.5°).
- Sesgo de cobertura de sensores: el modelo se preentrena y evalúa solo en sensores de la serie Sentinel (S1 SAR, S2/S3 ópticos, S5P atmosféricos) y Copernicus DEM; no está diseñado para otros sensores.
- Sesgo temporal: todos los datos de preentrenamiento se anclan alrededor de 2021 (~1 año de adquisición), por lo que la dinámica de largo plazo, tendencias y eventos raros fuera de esa ventana están sub-representados.
- Sesgo de tareas/etiquetas: la procedencia de etiquetas varía — 9 datasets provienen de fuentes existentes (CloudSEN12+, EuroSAT, EuroSAT-SAR, BigEarthNet v2.0, DFC2020, Kuro Siwo, So2Sat LCZ42) y 6 son nuevos, curados por los autores (Cloud-S3, LC100Cls-S3, LC100Seg-S3, Biomass-S3, AQ-NO2-S5P, AQ-O3-S5P); la calidad de etiquetas y definiciones de clase difieren entre tareas.
- Sesgo de disponibilidad de metadatos: varios datasets de benchmark carecen de metadatos de geolocalización y/o tiempo (DFC2020-S1/S2, LCZ-S2), lo que limita la ventaja del modelo basada en metadatos en esas tareas.

**Riesgos.**
- Los usuarios descendentes pueden sobreestimar el rendimiento del modelo en regiones, estaciones, tipos de cobertura del suelo o sensores sub-representados en Copernicus-Pretrain y Copernicus-Bench.
- Las salidas del modelo para tareas de regresión (biomasa, calidad del aire) son estimaciones que deben validarse contra mediciones in situ antes de usarse en monitoreo ambiental o contextos de política.
- Los autores señalan que el beneficio del componente de destilación S1/2 disminuye con tiempos de entrenamiento más largos y modelos más grandes, por lo que el checkpoint preentrenado puede no ser óptimo para todas las recetas descendentes.
- Los metadatos de área de parche son aproximados por construcción: debido a la proyección geográfica no reflejan estrictamente el área superficial (los autores indican que son suficientemente precisos para el preentrenamiento).
- Entrenar el modelo conlleva un costo computacional sustancial (una corrida de preentrenamiento = 512 horas GPU de NVIDIA A100).

**Limitaciones (declaradas por los autores en la última versión del artículo, arXiv:2503.11849v3).**
- "As a limitation, the scope of this work is restricted to the Sentinel series and a temporal range of about 1 year. Future work will thus also explore efficient scaling for satellite expansion, as well as native model support for multimodal fusion and time series processing."

### Recomendaciones

- Los usuarios (tanto directos como descendentes) deben conocer los riesgos, sesgos y limitaciones listados anteriores, en particular el alcance solo-Sentinel y de un único año ancla de los datos de preentrenamiento.
- Antes del despliegue, validar el rendimiento del modelo sobre datos de prueba específicos de región, estación y cobertura del suelo relevantes al dominio de aplicación, en lugar de depender únicamente de las puntuaciones agregadas de Copernicus-Bench.
- Para productos de regresión (biomasa, NO2, O3), complementar las predicciones con mediciones terrestres e reportar incertidumbre.
- Al usar cabezales de clasificación/segmentación descendente, seguir el protocolo de benchmark (sonda lineal con SGD para clasificación; decodificador UPerNet con AdamW para segmentación/regresión; 50 épocas) para resultados comparables.
- Cumplir las licencias de los pesos del modelo y datasets (CC-BY-4.0), y de cada dataset de benchmark obtenido de otra fuente (CloudSEN12+ CC0-1.0, EuroSAT MIT, EuroSAT-SAR CC-BY-4.0, BigEarthNet v2.0 CDLA-Permissive-1.0, Kuro Siwo MIT, So2Sat LCZ42 CC-BY-4.0, DFC2020 CC-BY-4.0).

## Cómo empezar con el modelo

Use el código siguiente para empezar con el modelo (adaptado del repositorio oficial):

```python
import torch
from src.model_vit import vit_base_patch16  # o vit_large_patch16 para el checkpoint ViT-L

# crear el modelo
model = vit_base_patch16(num_classes=10, global_pool=False)

# cargar los pesos preentrenados
path = './weights/CopernicusFM_ViT_base_varlang_e100.pth'  # 558 MB; variante large: CopernicusFM_ViT_large_varlang_e100.pth (1.5 GB)
check_point = torch.load(path)
state_dict = check_point['model'] if 'model' in check_point else check_point
msg = model.load_state_dict(state_dict, strict=False)
print(msg)

# codificar una imagen espectral (cualquier pila de bandas espectrales)
img = torch.randn(1, 4, 224, 224)
meta = torch.full((1, 4), float('nan'))  # [lon, lat, delta_time, patch_token_area], asumir desconocido
wvs = [490, 560, 665, 842]   # longitud de onda: B,G,R,NIR (Sentinel 2)
bws = [65, 35, 30, 115]      # ancho de banda: B,G,R,NIR (Sentinel 2)
language_embed = None        # N/A para entrada espectral
kernel_size = 16
input_mode = 'spectral'
logit, embed = model(img, meta, wvs, bws, language_embed, input_mode, kernel_size)
print(logit.shape, embed.shape)  # embed contiene las características codificadas de la imagen
```

Para entradas no espectrales (p. ej., Sentinel-5P NO2/CO/SO2/O3 o Copernicus DEM), cargue los embeddings de nombres de variables pre-codificados (`varname_embed_llama3.2_1B.pt`, 128-d por variable, codificados con Llama 3.2-1B) y use `input_mode='variable'` con el tamaño de parche apropiado (4 para S5P, 64 para DEM). Consulte el notebook de demostración oficial para ejemplos detallados: https://github.com/zhu-xlab/Copernicus-FM/blob/main/Copernicus-FM/demo.ipynb

## Detalles del entrenamiento

### Datos de entrenamiento

Copernicus-FM se preentrena sobre **Copernicus-Pretrain**, un dataset unificado de imágenes alineadas de todas las principales misiones operativas Sentinel de Copernicus, organizado en ~310K celdas regionales de 0.25°×0.25° (consistente con las coordenadas de ERA5), cubriendo densamente toda la superficie terrestre y océano costero. Las imágenes se descargaron con Google Earth Engine alrededor del año ancla **2021** y pasaron controles de calidad (eliminando imágenes corruptas o dominadas por NaN). El subconjunto unión final contiene 312,567 celdas y 18,713,054 imágenes; el subconjunto alineado en todas las modalidades ("joint") usado para el preentrenamiento contiene 219,543 celdas y 15,720,353 imágenes. El dataset está disponible en formatos GeoTIFF y WebDataset en Hugging Face (`wangyi111/Copernicus-Pretrain`, tamaño total 20.7 TB), con licencia CC-BY-4.0.

| Modalidad | Tipo | GSD | Tamaño de imagen | # Celdas | # Parches | # Marcas temporales | # Imágenes totales |
|---|---|---|---|---|---|---|---|
| Sentinel-1 GRD | SAR | 10 m | 264×264×2 | 247,723 | 1,067,267 | ~4 | 4,227,387 |
| Sentinel-2 TOA | MS | 10 m | 264×264×13 | 247,723 | 1,067,267 | ~4 | 4,218,065 |
| Sentinel-3 OLCI | MS | 300 m | 96×96×21 | 281,375 | 281,375 | ~8 | 2,189,561 |
| Sentinel-5P CO | atmos. | 1 km | 28×28 | 306,097 | 306,097 | 1–12 | 2,104,733 |
| Sentinel-5P NO2 | atmos. | 1 km | 28×28 | 291,449 | 291,449 | 1–12 | 1,752,558 |
| Sentinel-5P SO2 | atmos. | 1 km | 28×28 | 262,259 | 262,259 | 1–12 | 1,366,452 |
| Sentinel-5P O3 | atmos. | 1 km | 28×28 | 306,218 | 306,218 | 1–12 | 2,556,631 |
| Copernicus DEM (GLO-30) | elevación | 30 m | 960×960 | 297,665 | 297,665 | 1 | 297,665 |
| **Copernicus-Pretrain (unión)** | | | | **312,567** | **3,879,597** | | **18,713,054** |

En promedio, cada celda contiene 4 pares de series temporales locales S1/S2 (16 imágenes cada una), 8 imágenes S3, 7/6/5/8 imágenes S5P CO/NO2/SO2/O3 y 1 imagen DEM. Las ubicaciones S1/S2 se muestrearon con una estrategia Gaussiana (std 50 km) alrededor de las top-10K ciudades pobladas (~1M ubicaciones) más 40K ubicaciones polares; cada ubicación aporta 4 parches estacionales de 264×264. Los mosaicos S3 con porcentaje de píxeles brillantes superior al 20% se filtraron como nublados; se eliminaron mosaicos S5P completamente NaN; los productos S5P son 12 medias mensuales por variable; el DEM proporciona una única imagen de elevación por celda.

### Procedimiento de entrenamiento

#### Preprocesado [opcional]

Dataset crudo convertido a formato WebDataset (una celda = una muestra mínima por shard). Por iteración, se muestrea una imagen de cada una de las ocho modalidades de una celda. Normalización: S1/S2 media/std por canal; S3 multiplicado por el factor de escala por canal de Google Earth Engine `COPERNICUS_S3_OLCI`; S5P valores crudos con píxeles NaN reemplazados por cero (las medias mensuales usan el primer día del mes); DEM dividido por 10000. Metadatos por imagen: geolocalización (lon/lat central), área de calculada desde GSD × tamaño de parche, en km², adaptada dinámicamente al recorte), y tiempo (días desde 1970-01-01; el DEM usa el primer día de 2015).

#### Hiperparámetros de entrenamiento

- **Backbones publicados:** ViT-B/16 (embed_dim 768, 12 bloques, 12 cabezas, parche 16) y ViT-L/16 (embed_dim 1024, 24 bloques, 16 cabezas, parche 16); decodificador embed_dim 512, 8 bloques, 16 cabezas.
- **Régimen de entrenamiento:** optimizador AdamW, weight decay 0.05; tasa de aprendizaje base 1.5e-4 (definida en batch 256, escalada linealmente), batch efectivo 288; warmup 10 épocas seguido de decaimiento coseno; 100 épocas sobre el subconjunto joint de 220K celdas; modelado de imágenes enmascarado estilo MAE con ratio de enmascaramiento 0.7 para todas las modalidades; probabilidad de caída de metadatos 0.7.
- **Destilación:** destilación continua de profesores congelados — DINOv2 (ViT-B/14) para canales RGB derivados de Sentinel-2 (peso de pérdida 0.1) y SoftCon (ViT-B/14) para canales completos de Sentinel-1/2 (peso 0.2), pérdida de similitud coseno tras average pooling global con cabeza de proyección estudiante.
- **Aumentos:** recorte redimensionado aleatorio (escala [0.2, 1.0]) y volteo horizontal aleatorio.
- **Tamaños de parche por modalidad:** 16×16 para S1/S2, 8×8 para S3, 4×4 para S5P, 64×64 para DEM (núcleo redimensionado dinámicamente desde la base 16×16).
- **Codificación de variables:** nombres de variables no espectrales codificados con Llama 3.2 (congelado) en vectores de 128 dimensiones (costo adicional cero en tiempo de entrenamiento).
- **Rangos de codificación Fourier:** longitudes de onda [1e2, 1e9] nm; anchos de banda [1, 1e9] nm; lon/lat [1e-4, 720]; área de parche [1e-3, 5.1e8] km²; tiempo [1, 365.25] días.

#### Velocidades, tamaños, tiempos [opcional]

- **Cómputo:** una corrida de entrenamiento toma 512 horas GPU en NVIDIA A100, es decir, 128 horas-nodo en un nodo de cómputo con 4× A100 (40 GB); el README oficial indica que entrenar el modelo ViT-B/16 por 100 épocas sobre el dataset completo de 220K celdas toma ~60 horas en 8× A100 (40 GB).
- **Checkpoints publicados:** `CopernicusFM_ViT_base_varlang_e100.pth` (558 MB) y `CopernicusFM_ViT_large_varlang_e100.pth` (1.5 GB), más `varname_embed/varname_embed_llama3.2_1B.pt` (embeddings de lenguaje para nombres de variables S5P/DEM); un checkpoint antiguo DOFA-S location se conserva en una carpeta `deprecated/`.
- **Componentes del modelo de preentrenamiento (no todos publicados):** codificador (arriba), decodificador MAE (embed_dim 512, 8 bloques, 16 cabezas), predictores de parche de decodificador dinámicos y cabeza de proyección de destilación.
- **Tamaño del dataset:** tamaño total de archivos de Copernicus-Pretrain 20.7 TB (Hugging Face), formatos GeoTIFF crudo y WebDataset en streaming.

## Evaluación

### Datos de prueba, factores y métricas

#### Datos de prueba

La evaluación se realiza sobre **Copernicus-Bench**, un conjunto de 15 datasets descendentes en tres niveles de aplicación, abarcando S1, S2, S3 y S5P (9 datasets obtenidos de fuentes existentes con licencias permisivas, 6 curados nuevos por los autores, todos disponibles en Hugging Face `wangyi111/Copernicus-Bench`):

| Nivel | Nombre | Tarea | Imágenes train/val/test | Tamaño de imagen | # Clases | Fuente | Licencia |
|---|---|---|---|---|---|---|---|
| L1 | Cloud-S2 | segmentación (nubes) | 1699/567/551 | 512×512×13 | 4 | CloudSEN12+ | CC0-1.0 |
| L1 | Cloud-S3 | segmentación (nubes) | 1197/399/399 | 256×256×21 | 5 | nuevo | CC-BY-4.0 |
| L2 | EuroSAT-S1 | clasificación (LULC) | 16200/5400/5400 | 64×64×2 | 10 | EuroSAT-SAR | CC-BY-4.0 |
| L2 | EuroSAT-S2 | clasificación (LULC) | 16200/5400/5400 | 64×64×13 | 10 | EuroSAT | MIT |
| L2 | BigEarthNet-S1 | clasificación (LULC) | 11894/6117/5991 | 120×120×2 | 19 | BigEarthNet v2.0 | CDLA-Permissive-1.0 |
| L2 | BigEarthNet-S2 | clasificación (LULC) | 11894/6117/5991 | 120×120×12 | 19 | BigEarthNet v2.0 | CDLA-Permissive-1.0 |
| L2 | LC100Cls-S3 | clasificación (LULC) | 5181/1727/1727* | 96×96×21 | 23 | nuevo | CC-BY-4.0 |
| L2 | DFC2020-S1 | segmentación (LULC) | 3156/986/986 | 256×256×2 | 10 | DFC2020 | CC-BY-4.0 |
| L2 | DFC2020-S2 | segmentación (LULC) | 3156/986/986 | 256×256×13 | 10 | DFC2020 | CC-BY-4.0 |
| L2 | LC100Seg-S3 | segmentación (LULC) | 5181/1727/1727* | 96×96×21 (etiqueta 288×288) | 23 | nuevo | CC-BY-4.0 |
| L3 | Flood-S1 | detección de cambios (inundación) | 3000/1000/1000* | 224×224×2 | 3 | Kuro Siwo | MIT |
| L3 | LCZ-S2 | clasificación (zona climática local) | 15000/5000/5000 | 32×32×10 | 17 | So2Sat LCZ42 | CC-BY-4.0 |
| L3 | Biomass-S3 | regresión (biomasa) | 3000/1000/1000* | 96×96×21 (etiqueta 288×288) | – | nuevo | CC-BY-4.0 |
| L3 | AQ-NO2-S5P | regresión (calidad del aire) | 1480/493/494* | 56×56×1 | – | nuevo | CC-BY-4.0 |
| L3 | AQ-O3-S5P | regresión (calidad del aire) | 1480/493/494* | 56×56×1 | – | nuevo | CC-BY-4.0 |

*El modo por defecto es 1 imagen para segmentación y 2 para detección de cambios; LC100Cls-S3/LC100Seg-S3/Biomass-S3/AQ-S5P también soportan modos estacionales (4 imágenes). Las ablaciones usan k-NN en EuroSAT y sonda lineal en LC100Cls-S3; el estudio de embeddings de celda climática usa 10K celdas muestreadas globalmente con objetivos ERA5.

#### Factores

Dado que Copernicus-FM procesa datos físicos de sensores en lugar de personas, los factores de evaluación se adaptan de Mitchell et al. (2019) a instrumentación y entorno:

- **Instrumentación (sensores y bandas):**
  - Sentinel-1 GRD (SAR): polarizaciones VV + VH, 2 canales, GSD 10 m, parches 264×264 px (~2.6 km).
  - Sentinel-2 TOA (óptico): 13 bandas (longitudes de onda 440–2190 nm; p. ej., B4 665 nm/30 nm, B8 842 nm/115 nm), GSD 10 m.
  - Sentinel-3 OLCI: 21 bandas (400–1020 nm; anchos de banda 7.5–40 nm), GSD 300 m.
  - Sentinel-5P: variables de columna única (columna troposférica de NO2, densidad numérica de columna de CO, SO2, O3), ~1 km, 28×28 px.
  - Copernicus DEM GLO-30: elevación, GSD 30 m.
  - Los tamaños de parche difieren por modalidad (16/16/8/4/64 px) siguiendo la resolución de cada sensor.
- **Entorno (dominios de aplicación):**
  - Región geográfica: Copernicus-Pretrain cubre tierra + buffer costero 0.5°, muestreado alrededor de las top-10K ciudades pobladas y regiones polares; los datasets de benchmark abarcan Europa (EuroSAT, subconjunto BigEarthNet, LCZ), tierra global (LC100, mosaicos de prueba DFC2020) e inundaciones globales (Kuro Siwo); AQ-S5P cubre Europa (cuadrícula EEA 1 km²).
  - Cobertura de nubes: mosaicos S3 de preentrenamiento filtrados al >20% de píxeles brillantes; imágenes de benchmark LC100/Biomass filtradas de nubes con un modelo entrenado en Cloud-S3; las tareas Cloud-S2/S3 apuntan explícitamente a escenas nubladas.
  - Estación: los parches de preentrenamiento S1/S2 son 4 adquisiciones estacionales; las tareas de benchmark soportan modos estático (1 imagen), par (detección de cambios) o estacional (4 imágenes); el preentrenamiento S5P usa 12 medias mensuales.
  - Tipo de cobertura del suelo: los datasets LULC cubren 10–23 clases de cobertura CORINE (EuroSAT, BigEarthNet, LC100, DFC2020) y 17 zonas climáticas locales (LCZ).
  - La disponibilidad de metadatos varía por benchmark: DFC2020-S1/S2 y LCZ-S2 carecen de metadatos de geolocalización y tiempo; EuroSAT-S1/S2 carecen de metadatos de tiempo.

#### Métricas

- **mIoU (Intersección sobre Unión media):** tareas de segmentación (Cloud-S2, Cloud-S3, DFC2020-S1/S2, LC100Seg-S3, Flood-S1).
- **OA (Precisión global):** tareas de clasificación de etiqueta única (EuroSAT-S1/S2, LCZ-S2).
- **mAP (Precisión media):** tareas de clasificación multietiqueta (BigEarthNet-S1/S2, LC100Cls-S3).
- **RMSE (Error cuadrático medio):** tareas de regresión (Biomass-S3 en t/ha AGB; AQ-NO2-S5P y AQ-O3-S5P niveles de calidad del aire).
- **Protocolo:** promedios de tres corridas con desviaciones estándar; evaluación de codificador congelado (sonda lineal con SGD, batch 64; decodificador UPerNet con AdamW, batch 16; 50 épocas) frente a líneas base supervisadas ViT-S/16 y ViT-B/16 entrenadas desde cero por 80 épocas.

### Resultados

**Resultados de benchmark (Copernicus-Bench, Copernicus-FM ViT-B/16 con tamaños de parche adaptativos por modalidad; media ± std de tres corridas):**

| Tarea | Sensor | Métrica | Supervisado ViT-B/16 | DOFA | Copernicus-FM |
|---|---|---|---|---|---|
| Cloud-S2 | S2 TOA | mIoU | 59.4 ± 1.0 | 65.0 ± 0.2 | 66.7 ± 0.1 |
| Cloud-S3 | S3 OLCI | mIoU | 63.0 ± 0.8 | 58.2 ± 0.1 | **62.0 ± 0.7** |
| EuroSAT-S1 | S1 GRD | OA | 81.5 ± 0.9 | 81.7 ± 0.1 | **87.2 ± 0.1** |
| EuroSAT-S2 | S2 TOA | OA | 97.6 ± 0.1 | 97.2 ± 0.1 | **97.9 ± 0.1** |
| BigEarthNet-S1 | S1 GRD | mAP | 70.6 ± 0.4 | 70.5 ± 0.0 | 77.9 ± 0.0 |
| BigEarthNet-S2 | S2 SR | mAP | 80.1 ± 0.1 | 75.5 ± 0.0 | 79.0 ± 0.0 |
| LC100Cls-S3 | S3 OLCI | mAP | 91.4 ± 0.5 | 89.5 ± 0.0 | **93.3 ± 0.4** |
| DFC2020-S1 | S1 GRD | mIoU | 50.8 ± 0.5 | 49.7 ± 0.1 | 52.4 ± 0.1 |
| DFC2020-S2 | S2 TOA | mIoU | 66.2 ± 0.7 | 61.8 ± 0.1 | 64.5 ± 0.1 |
| LC100Seg-S3 | S3 OLCI | mIoU | 19.3 ± 0.5 | 16.5 ± 0.1 | **24.1 ± 0.0** |
| Flood-S1 | S1 GRD | mIoU | 78.3 ± 0.3 | 76.0 ± 0.1 | 77.7 ± 0.0 |
| LCZ-S2 | S2 TOA | OA | 85.3 ± 0.8 | 83.0 ± 0.3 | 84.4 ± 0.0 |
| Biomass-S3 | S3 OLCI | RMSE ↓ | 68.3 ± 0.4 | 74.1 ± 0.1 | **66.3 ± 0.1** |
| AQ-NO2-S5P | S5P NO2 | RMSE ↓ | 3.4 ± 0.0 | 3.3 ± 0.0 | **2.8 ± 0.0** |
| AQ-O3-S5P | S5P O3 | RMSE ↓ | 1766.8 ± 22.1 | 1755.6 ± 19.8 | **789.4 ± 2.6** |

Copernicus-FM supera al entrenamiento supervisado en la mayoría (11/15) de las tareas, a pesar de utilizar menos parámetros entrenables e iteraciones, y logra las mejores puntuaciones en las tareas S3/S5P (ningún otro modelo fundacional de la competencia soporta esas modalidades). En BigEarthNet-S1/S2 y LCZ-S2 las líneas base especializadas SoftCon y la supervisada ViT-S/16 siguen siendo mejores.

**Estudio de ablación (ViT-Small, subconjunto de 10K celdas, 100 épocas; adiciones acumulativas de componentes):**

| Componente | EuroSAT-S1 (OA ↑) | EuroSAT-S2 (OA ↑) | EuroSAT-RGB (mAP ↑) | LC100Cls-S3 (mAP ↑) | AQ-O3-S5P (RMSE ↓) |
|---|---|---|---|---|---|
| Línea base (DOFA) + tamaño de parche dinámico | 56.3 | 87.6 | 62.2 | 86.7 | 2218.0 |
| + ancho de banda (codificación Fourier) | 56.5 | 88.9 | 65.4 | 87.1 | 1710.7 |
| + hiperred de variables | 57.5 | 88.9 | 65.8 | 86.6 | 1598.1 |
| + codificación de metadatos | 77.9 | 88.9 | 78.5 | 90.7 | 839.3 |
| + destilación continua | 81.0 | 89.5 | 78.9 | 90.7 | 811.6 |

La codificación de metadatos proporciona la mayor mejora individual, especialmente en modalidades no ópticas (RMSE AQ-O3-S5P −758.8; OA EuroSAT-S1 +22.4).

**Regresión de embeddings de celda climática (regresión lineal sobre 10K celdas, objetivos ERA5; RMSE promedio de tres corridas):**

| Entrada | Temperatura (°C) media / std | Precipitación (m) media / std | Presión superficie (Pa) media / std | Presión nivel del mar (Pa) media / std | viento uu (m/s) media / std | viento vv (m/s) media / std |
|---|---|---|---|---|---|---|
| Coordenadas (crudas) | 8.36 ± 0.00 / 3.06 ± 0.00 | 0.79 ± 0.00 / 0.50 ± 0.00 | 7305.91 ± 0.37 / 141.38 ± 0.01 | 290.68 ± 0.02 / 164.84 ± 0.01 | 1.12 ± 0.01 / 0.55 ± 0.00 | 0.95 ± 0.00 / 0.58 ± 0.00 |
| Coordenadas (codificadas Fourier) | 3.99 ± 0.00 / 2.09 ± 0.00 | 0.61 ± 0.00 / 0.42 ± 0.00 | 6537.54 ± 1.78 / 101.09 ± 0.18 | 200.93 ± 0.09 / 108.12 ± 0.01 | 0.96 ± 0.01 / 0.51 ± 0.00 | 0.80 ± 0.00 / 0.54 ± 0.00 |
| Embeddings de celda Copernicus-FM | 2.66 ± 0.01 / 1.72 ± 0.00 | 0.47 ± 0.00 / 0.33 ± 0.00 | 1627.21 ± 30.13 / 97.53 ± 0.71 | 197.17 ± 0.62 / 117.57 ± 0.86 | 0.94 ± 0.00 / 0.43 ± 0.00 | 0.78 ± 0.00 / 0.43 ± 0.00 |
| **Embeddings + coordenadas** | **1.98 ± 0.01 / 1.35 ± 0.00** | **0.46 ± 0.00 / 0.32 ± 0.00** | **1676.39 ± 33.64 / 78.53 ± 0.08** | **174.80 ± 0.21 / 90.37 ± 0.15** | **0.91 ± 0.00 / 0.42 ± 0.00** | **0.76 ± 0.00 / 0.43 ± 0.00** |
| Coordenadas (codificador de ubicación SatCLIP) | 3.67 ± 0.03 / 1.58 ± 0.00 | 0.48 ± 0.00 / 0.32 ± 0.00 | 3178.91 ± 8.75 / 97.74 ± 0.19 | 202.76 ± 0.27 / 110.71 ± 0.41 | 0.92 ± 0.00 / 0.43 ± 0.00 | 0.79 ± 0.00 / 0.45 ± 0.00 |

(Los embeddings de celda se obtienen promediando las imágenes codificadas por el modelo de cada celda; los objetivos son la media y desviación estándar de 10 años de cada parámetro según ERA5.) Los embeddings de celda superan consistentemente a las líneas base basadas en coordenadas, y combinar embeddings con coordenadas produce el mejor rendimiento global en cada parámetro — p. ej., el RMSE de media de temperatura baja de 8.36 (coordenadas crudas) a 1.98 (embeddings + coordenadas).

#### Resumen

Copernicus-FM es comparable o superior a los modelos fundacionales unimodales o multimodales de estado del arte en sus tareas aplicables, y mejora sustancialmente el rendimiento descendente en tareas Sentinel-3 y Sentinel-5P, verificando los beneficios del preentrenamiento cruzado por modalidades tanto para aplicaciones superficiales como atmosféricas.

## Examen del modelo [opcional]

El artículo proporciona análisis orientados a interpretabilidad: (1) ablaciones aislando cada componente arquitectónico (tamaño de parche dinámico, codificación Fourier de ancho de banda, hiperred de variables, codificación de metadatos, destilación continua — ver Resultados); (2) ablaciones de metadatos mostrando que la ubicación es el metadato más importante entre los tres tipos, y que una mayor tasa de caída de metadatos es mejor; (3) visualización de las codificaciones Fourier aprendidas para longitudes de onda/anchos de banda, nombres de variables y metadatos; y (4) visualización global del mapa de embeddings Copernicus-Embed-025deg (PCA a 3 dimensiones), mostrando que las coordenadas crudas producen predicciones climáticas demasiado suaves mientras que los embeddings de celda derivados de EO capturan detalles y extremos más finos.

## Impacto ambiental

Las emisiones de carbono pueden estimarse usando la [calculadora de impacto de Machine Learning](https://mlco2.github.io/impact#compute) presentada en [Lacoste et al. (2019)](https://arxiv.org/abs/1910.09700).

- **Tipo de hardware:** GPUs NVIDIA A100 (40 GB).
- **Horas usadas:** 512 horas GPU por corrida de preentrenamiento (128 horas-nodo en un nodo con 4× A100 40 GB; ~60 horas en 8× A100 según el README oficial).
- **Proveedor de nube:** No reportado por los autores.
- **Región de cómputo:** No reportada por los autores.
- **Carbono emitido:** No reportado por los autores.

## Especificaciones técnicas [opcional]

### Arquitectura y objetivo del modelo

**Arquitectura.** Codificador ViT unificado con parcheo dinámico consciente del sensor:
- **Hiperred espectral:** la longitud de onda central y ancho de banda por canal se codifican mediante Fourier (128-d), pasan por una capa FC residual y un generador de pesos TransformerEncoder de 1 capa que emite los pesos y sesgos del núcleo de convolución 16×16×embed_dim para el parcheo; el núcleo se redimensiona dinámicamente (estilo FlexiViT) al tamaño de parche de cada modalidad.
- **Hiperred de variables:** las variables no espectrales (S5P NO2/CO/SO2/O3, DEM) se representan mediante embeddings de lenguaje de 128-d de sus nombres de variable (Llama 3.2 congelado, calculado una vez offline) y alimentan la misma estructura de generador de pesos.
- **Integración de metadatos:** geolocalización (lon/lat), área de parche (km²) y tiempo (días desde 1970-01-01) se codifican mediante Fourier, se proyectan linealmente, se expanden a lo largo de los parches y se suman a las embeddings posicionales; tokens aprendibles reemplazan metadatos faltantes (probabilidad de caída 0.7).
- **Backbone:** bloques Transformer estándar de timm (qkv_bias=True, GELU, LayerNorm), ratio MLP 4.0; variantes publicadas ViT-B/16 (768-d, 12 bloques, 12 cabezas) y ViT-L/16 (1024-d, 24 bloques, 16 cabezas).
- **Modelo de preentrenamiento:** codificador-decodificador estilo MAE con decodificador ligero (512-d, 8 bloques, 16 cabezas), predictores de parche de decodificador dinámicos y cabeza de proyección de destilación.

**Número de parámetros (calculado desde el código oficial, ver abajo):**

| Componente | ViT-B/16 | ViT-L/16 |
|---|---|---|
| Bloques Transformer | 85,054,464 | 302,309,376 |
| Hiperred espectral (parcheo) | 26,104,064 | 34,591,232 |
| Hiperred de variables (parcheo) | 26,104,064 | 34,591,232 |
| Codificadores MLP de metadatos (coord/escala/tiempo) | 1,771,776 | 3,148,800 |
| Token de clase + tokens de metadatos + norm final | 4,608 | 6,144 |
| **Backbone codificador (entrenable)** | **139,038,976** | **374,646,784** |
| Embeddings posicionales fijos (no entrenables) | 151,296 | 201,728 |

Método: los parámetros se calcularon analíticamente desde las definiciones de arquitectura del código oficial (`CopernicusFMViT` en `src/model_vit.py`, `MaskedAutoencoderViT` en `src/models_mae_cfm.py`, hiperredes en `src/dynamic_hypernetwork.py`), sumando cada submódulo (cada bloque Transformer aporta 12d²+13d parámetros; cada hiperred consiste en un generador de pesos TransformerEncoder de 1 capa (d_model=128) más FCResLayer y cabezas de salida; más codificadores de metadatos y tokens). El cálculo se validó contra los tamaños de archivo de los checkpoints publicados: 139,038,976 × 4 bytes ≈ 556 MB, coincidiendo con el checkpoint ViT-B de 558 MB; 374,646,784 × 4 bytes ≈ 1,499 MB, coincidiendo con el de 1.5 GB — confirmando que los pesos publicados son los backbones codificadores en fp32. Los archivos de checkpoint también confirman dos variantes publicadas: `CopernicusFM_ViT_base_varlang_e100.pth` y `CopernicusFM_ViT_large_varlang_e100.pth` (ambas en época 100).

**Objetivo.** Pérdida de preentrenamiento = modelado de imágenes enmascarado (reconstrucción de parches estilo MAE sobre tokens aplanados, ratio de enmascaramiento 0.7, predictor de parche dinámico por modalidad) + destilación continua (pérdida de similitud coseno entre la proyección estudiante y características de profesor congelado: DINOv2 ViT-B/14 sobre RGB derivado de S2 con peso 0.1; SoftCon ViT-B/14 sobre canales completos S1/S2 con peso 0.2).

### Infraestructura de cómputo

#### Hardware

- Preentrenamiento: GPUs NVIDIA A100 (40 GB); una corrida = 512 horas GPU (nodo 4× A100, 128 horas-nodo; ~60 h en 8× A100 según el README oficial).
- Experimentos de benchmark: GPU única, tres corridas por experimento.

#### Software

- Python 3.10 (entorno conda recomendado); PyTorch + timm + einops + rasterio + pyproj (+ torchvision, kornia, webdataset, tensorboard, wandb). Ver `requirements.txt` en el repositorio oficial.
- Embeddings de nombres de variables: Llama 3.2 (congelado), pre-codificados offline.

## Citación [opcional]

**BibTeX:**

```bibtex
@misc{wang2025unifiedcopernicusfoundationmodel,
      title={Towards a Unified Copernicus Foundation Model for Earth Vision},
      author={Yi Wang and Zhitong Xiong and Chenying Liu and Adam J. Stewart and Thomas Dujardin and Nikolaos Ioannis Bountos and Angelos Zavras and Franziska Gerken and Ioannis Papoutsis and Laura Leal-Taixé and Xiao Xiang Zhu},
      year={2025},
      eprint={2503.11849},
      archivePrefix={arXiv},
      primaryClass={cs.CV},
      url={https://arxiv.org/abs/2503.11849},
}
```

**APA:**

Wang, Y., Xiong, Z., Liu, C., Stewart, A. J., Dujardin, T., Bountos, N. I., Zavras, A., Gerken, F., Papoutsis, I., Leal-Taixé, L., & Zhu, X. X. (2025). Towards a Unified Copernicus Foundation Model for Earth Vision. *arXiv preprint arXiv:2503.11849*. Presentado en ICCV 2025 (oral).

## Glosario [opcional]

- **MIM (Modelado de Imágenes Enmascarado):** objetivo auto-supervisado que enmascara tokens de parche aleatorios y los reconstruye con un decodificador ligero.
- **MAE (Autoencoder Enmascarado):** la arquitectura MIM (He et al., 2022) en la que se basa el preentrenamiento de Copernicus-FM.
- **Hiperred:** una red que genera los pesos de otra red; aquí genera núcleos de convolución de parcheo condicionados a la respuesta espectral del sensor (hiperred espectral) o al nombre de variable (hiperred de variables).
- **GSD (Distancia de muestreo en tierra):** tamaño de píxel en el suelo (p. ej., 10 m para Sentinel-2).
- **Sentinel-1/2/3/5P:** misiones Copernicus de la ESA — S1: SAR banda C; S2: óptico multiespectral (13 bandas, 10 m); S3: instrumento de color océano/terraza OLCI (21 bandas, 300 m); S5P: sensor de química atmosférica TROPOMI (~1 km columnas de NO2, CO, SO2, O3).
- **mIoU:** Intersección sobre Unión media (métrica de segmentación). **OA:** Precisión global. **mAP:** Precisión media (clasificación multietiqueta). **RMSE:** Error cuadrático medio (regresión).
- **LCZ (Zona Climática Local):** esquema de clasificación de zonas urbanas/rurales basado en clima (17 clases).
- **SOMO35:** métrica de concentración de ozono de máximo diario de 8 horas usada para los objetivos AQ-O3-S5P (percentil 93.2, EEA 2021).
- **ERA5:** dataset de reanálisis global de ECMWF; la estructura de celdas de Copernicus-Pretrain está alineada con las coordenadas ERA5.
- **UPerNet:** decodificador de segmentación usado sobre codificadores congelados en Copernicus-Bench.

## Más información [opcional]

- Datasets: Copernicus-Pretrain (20.7 TB, `wangyi111/Copernicus-Pretrain`), Copernicus-Bench (`wangyi111/Copernicus-Bench`) y Copernicus-Embed-025deg (`wangyi111/Copernicus-Embed-025deg`; mapa global de embeddings 721×1440×768 a 0.25°) están disponibles en Hugging Face bajo CC-BY-4.0.
- Los autores declaran su intención de contribuir el dataset, modelo y benchmark a bibliotecas de código abierto como TorchGeo.
- Trabajo futuro declarado por los autores: escalado eficiente a satélites más allá de la serie Sentinel, y soporte nativo del modelo para fusión multimodal y procesamiento de series temporales; posible incorporación de embeddings de celda en predicción meteorológica de medio alcance basada en ML.

## Autores de la ficha [opcional]

Maicol Julian Torres Ochoa — mjtorreso@udistrital.edu.co — GitHub: @maicooljt8a-ops (https://github.com/maicooljt8a-ops) — Inteligencia Artificial Aplicada al Territorio, Universidad Distrital Francisco Jose de Caldas.

## Contacto de la ficha

Para preguntas sobre esta ficha: Maicol Julian Torres Ochoa (mjtorreso@udistrital.edu.co). Para preguntas sobre el modelo mismo, contacte a los autores vía el repositorio oficial (https://github.com/zhu-xlab/Copernicus-FM) o la página del modelo en Hugging Face (https://huggingface.co/wangyi111/Copernicus-FM).

## Fuentes

- Mitchell, M., Wu, S., Zaldivar, A., Barnes, P., Vasserman, L., Hutchinson, B., Spitzer, E., Raji, I. D., & Gebru, T. (2019). *Model Cards for Model Reporting*. FAT* 2019. https://arxiv.org/abs/1810.03993
- Wang, Y., et al. (2025). *Towards a Unified Copernicus Foundation Model for Earth Vision*. ICCV 2025 (oral), arXiv:2503.11849. https://arxiv.org/abs/2503.11849 (última versión: v3, 31 jul 2025 — https://arxiv.org/html/2503.11849v3)
- Repositorio oficial de código: https://github.com/zhu-xlab/Copernicus-FM (README, LICENSE, `Copernicus-FM/demo.ipynb`, `Copernicus-FM/src/`)
- Pesos del modelo: https://huggingface.co/wangyi111/Copernicus-FM (`CopernicusFM_ViT_base_varlang_e100.pth`, `CopernicusFM_ViT_large_varlang_e100.pth`, `varname_embed/varname_embed_llama3.2_1B.pt`)
- Tarjeta del dataset de preentrenamiento: https://huggingface.co/datasets/wangyi111/Copernicus-Pretrain
- Datasets de Copernicus-Bench: https://huggingface.co/datasets/wangyi111/Copernicus-Bench
- Dataset Copernicus-Embed-025deg: https://huggingface.co/datasets/wangyi111/Copernicus-Embed-025deg
- Plantilla de ficha de modelo de Hugging Face (estructura): https://github.com/huggingface/huggingface_hub/tree/main/src/huggingface_hub/templates
