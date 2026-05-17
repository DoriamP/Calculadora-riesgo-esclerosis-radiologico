# Herramientas de Diagnóstico de Esclerosis Hipocampal
### Hippocampal Sclerosis Diagnostic Tools

**Calculadoras predictivas basadas en morfometría hipocampal por RM de alta resolución**  
**Predictive calculators based on high-resolution MRI hippocampal morphometry**

Dirección científica / Scientific direction: **Dr. Doriam Perera · Neurocirugía Funcional / Functional Neurosurgery**  
Estado / Status: **Investigación en proceso · Research in progress**

---

> ⚠️ **Uso exclusivamente exploratorio · Exploratory use only**  
> Estas herramientas forman parte de una investigación en proceso y **no deben utilizarse con fines clínicos** ni para tomar decisiones diagnósticas o terapéuticas en pacientes reales. Los resultados son estimaciones estadísticas generadas a partir de muestras pequeñas no validadas externamente.  
> These tools are part of ongoing research and **must not be used for clinical purposes** or to inform diagnostic or therapeutic decisions in real patients. Results are statistical estimates from small, externally unvalidated samples.

---

## Archivos del repositorio · Repository files

| Archivo | Descripción |
|---|---|
| `index.html` | Página principal de acceso a ambas calculadoras / Main hub page |
| `calculadora.html` | Calculadora A — Modelo frecuentista de 3 variables |
| `Frecuentista_y_bayesiano.html` | Calculadora B — Modelo de 5 variables frecuentista + bayesiano |
| `README.md` | Este archivo / This file |

Todos los archivos son **autocontenidos** (HTML + CSS + JS en un solo fichero, sin dependencias externas ni servidor). Las imágenes de referencia de la Calculadora A están embebidas como base64.

All files are **self-contained** (HTML + CSS + JS in a single file, no external dependencies or server). Reference images in Calculator A are embedded as base64.

---

## Calculadora A — Modelo Frecuentista de 3 Variables

**Archivo:** `calculadora.html`

### Variables de entrada

| Variable | Unidad | Referencia de medición |
|---|---|---|
| Volumen hipocampal izquierdo y derecho | mm³ | Segmentación volumétrica manual (3D Slicer, ITK-Snap, Horos, etc.) |
| Grosor del giro dentado–CA4 izquierdo y derecho | mm | Plano coronal, parte más gruesa de la cabeza hipocampal |
| Ángulo coronal de la cabeza hipocampal izquierdo y derecho | ° | Plano coronal (verificar en sagital); vértice en pared ventricular más cercana |

La diferencia porcentual se calcula automáticamente:

```
Diferencia (%) = |mayor − menor| / mayor × 100
```

### Modelo de esclerosis hipocampal

**Tipo:** Regresión logística binaria con regularización Ridge (L2, C = 5)

**Ecuación:**
```
logit(p) = −26.55 + 0.812 × DifVol + 1.128 × DifDentado + 0.230 × DifÁngulo
p = 1 / (1 + e^(−logit))
```

**Coeficientes:**

| Variable | β | OR por unidad | OR por 5 unidades |
|---|---|---|---|
| Intercepto (β₀) | −26.545 | — | — |
| Dif. Volumen Hipocampal (%) | 0.812 | 2.25 | 57.9 |
| Dif. Grosor Giro Dentado (%) | 1.128 | 3.09 | 280.8 |
| Dif. Ángulo Hipocampal (%) | 0.230 | 1.26 | 3.16 |

**Umbrales:**

| Rango | Clasificación |
|---|---|
| p < 5.3% | Probabilidad baja · Low probability |
| 5.3% ≤ p < 70% | Probabilidad moderada · Moderate probability |
| p ≥ 70% | Probabilidad alta · High probability |

El umbral de 5.3% corresponde al punto de Youden óptimo. El umbral de 70% es criterio clínico.  
La calculadora funciona con 1, 2 o 3 pares de variables (modelo parcial automático).

### Modelo de lateralidad (lado afectado)

Regresión logística L2 (C = 5) entrenada en los 20 casos con datos completos. Predice P(lado derecho afectado) usando diferencias direccionales normalizadas:

```
signed = (valor_der − valor_izq) / max(|der|, |izq|) × 100

logit_lat = −0.006 + (−0.257) × vol_signed + (−0.147) × dent_signed + (−0.034) × ang_signed
P(der) = 1 / (1 + e^(−logit_lat))   |   P(izq) = 1 − P(der)
```

Valor negativo → lado derecho menor → mayor probabilidad de esclerosis derecha.

### Rendimiento diagnóstico — LOO-CV (n = 40)

| Métrica | Valor |
|---|---|
| AUC aparente | 1.000 |
| **AUC LOO-CV** | **0.995** (IC 95%: 0.977 – 1.000) |
| Optimismo Bootstrap (2 000 remuestras) | 0.005 |
| Nagelkerke R² | 0.943 |
| Brier Score (LOO-CV) | 0.055 |
| Hosmer-Lemeshow p | 0.88 (buen ajuste) |
| Sensibilidad | 100% |
| Especificidad | 95% |
| VPP / PPV | 95.2% |
| VPN / NPV | 100% |
| Accuracy | 97.5% |
| Índice Youden | 0.95 |
| Lateralidad (LOO-CV, n = 20) | Accuracy 100%, AUC 1.000 |

---

## Calculadora B — Modelo Frecuentista + Bayesiano de 5 Variables

**Archivo:** `Frecuentista_y_bayesiano.html`

### Variables de entrada

| Variable | Unidad | Rango normal orientativo |
|---|---|---|
| Volumen hipocampal izquierdo y derecho | mm³ | 2 800 – 4 000 mm³ |
| Ángulo coronal de la cabeza hipocampal izquierdo y derecho | ° | 40 – 50° |
| Grosor del giro dentado–CA4 izquierdo y derecho | mm | 2.0 – 4.0 mm |
| Grosor del subcampo CA1 izquierdo y derecho | mm | 1.5 – 3.5 mm |
| Intensidad de señal FLAIR hipocampal izquierdo y derecho | unidades relativas | — |

Diferencias calculadas automáticamente: `|Der – Izq| / Mayor × 100`.

Medición de CA1: mayor grosor en cabeza hipocampal plano coronal, verificar en ≥ 2 cortes consecutivos.  
Medición FLAIR: segmentación ROI del hipocampo completo en secuencia FLAIR sagital con 3D Slicer.

### Componente frecuentista

**Tipo:** Regresión logística regularizada Ridge (L2). Las diferencias se estandarizan internamente antes de aplicar los coeficientes.

**Parámetros del modelo:**

```
logit = 0.2874 + Σ [ β_i × (dif_i − media_i) / DE_i ]
P(EH) = 1 / (1 + e^(−logit))
```

| Variable | β (estand.) | OR | Media (%) | DE (%) |
|---|---|---|---|---|
| Intercepto | 0.2874 | — | — | — |
| Dif. volumen | 1.3274 | 3.77 | 16.42 | 15.21 |
| Dif. ángulo | 1.1245 | 3.08 | 15.34 | 10.81 |
| Dif. giro dentado | 1.0332 | 2.81 | 15.41 | 12.33 |
| Dif. CA1 | 0.7468 | 2.11 | 14.15 | 14.33 |
| Dif. FLAIR | 0.2060 | 1.23 | 4.96 | 2.14 |

### Componente bayesiano

```
Prior: Beta(21, 25) → prevalencia muestral = 45.8% (IC 95%: 32.1 – 59.9%)
Posterior ∝ Likelihood(datos | EH) × Prior
```

El usuario puede ajustar la prevalencia esperada para su contexto clínico, actualizando la probabilidad posterior en tiempo real.

### Umbrales de clasificación

| Rango (frecuentista) | Clasificación |
|---|---|
| < 35% | Baja · Low |
| 35% – 60% | Intermedia · Intermediate |
| > 60% | Alta (criterio clínico) · High (clinical criterion) |

Umbral estadístico óptimo por Youden: **37.6%**

### Rendimiento diagnóstico — LOO-CV (n = 46)

| Métrica | Valor |
|---|---|
| **AUC LOO-CV** | **0.970** (IC 95%: 0.90 – 1.00) |
| Sensibilidad / Sensitivity | 92.9% |
| **Especificidad / Specificity** | **100%** — sin falsos positivos |
| VPP / PPV | 100% |
| VPN / NPV | 94.3% |

Validación: L2 Ridge · LOO-CV (n = 46, modelo reentrenado dejando un caso fuera en cada iteración).

---

## Diseño del estudio · Study design

| Parámetro | Valor |
|---|---|
| Diseño | Estudio observacional de casos y controles |
| Total pacientes | 46 |
| Casos con EH confirmada histopatológicamente | 21 |
| Controles | 25 |
| Muestra analítica Calculadora A | 40 (6 excluidos por datos incompletos) |
| Muestra analítica Calculadora B | 46 |
| Protocolo RM | Alta resolución: T1 volumétrico + morfometría de subcampos + FLAIR coronal oblicuo |
| Software principal | 3D Slicer (segmentación volumétrica y FLAIR ROI) |

### Selección de variables

Análisis univariante: Mann-Whitney U + AUC individual por variable. Se retuvieron predictores con AUC > 0.66 y p < 0.05.

Variables excluidas y motivo:

| Variable | Motivo de exclusión |
|---|---|
| Girificación | p = 1.0; no discrimina |
| Intensidad FLAIR (solo Calculadora A) | 35% datos faltantes |
| Grosor CA1 (solo Calculadora A) | Correlación con dentado r = 0.33; menor poder discriminativo |
| Elongación | p = 0.027 marginal; discriminación débil |
| Volúmenes absolutos por lado (Calculadora B) | Alta multicolinealidad (r > 0.70) |
| Ángulos absolutos por lado (Calculadora B) | Alta multicolinealidad (r > 0.70) |

### Justificación regularización L2

Separación casi perfecta entre casos y controles → coeficientes MLE no convergen sin penalización. Ridge (L2) estabiliza coeficientes sin eliminar variables (equivalente funcional al método de Firth).  
EPV Calculadora A = 6.7 (20 eventos / 3 predictores; < 10 clásico; la regularización mitiga el riesgo).

---

## Publicación en GitHub Pages

```
repositorio/
├── index.html                      ← renombrar desde index.html
├── calculadora.html
├── Frecuentista_y_bayesiano.html
└── README.md
```

1. Crear repositorio público en GitHub y subir los cuatro archivos
2. **Settings → Pages → Branch: main / Folder: / (root)**
3. Acceder a `https://usuario.github.io/nombre-repositorio/`

Los archivos funcionan también abriéndolos directamente en el navegador sin servidor.

---

## Comparativa de modelos · Model comparison

| Métrica | Calculadora A (3 vars) | Calculadora B (5 vars) |
|---|---|---|
| Variables | 3 | 5 |
| Muestra | n = 40 | n = 46 |
| AUC LOO-CV | **0.995** | 0.970 |
| Sensibilidad | **100%** | 92.9% |
| Especificidad | 95% | **100%** |
| VPP | 95.2% | **100%** |
| VPN | **100%** | 94.3% |
| Accuracy | **97.5%** | — |
| Umbral alto | > 70% | > 60% |
| Lateralidad | ✓ | — |
| Componente bayesiano | — | ✓ |
| Curva ROC interactiva | — | ✓ |
| Tamaño archivo | ~3.3 MB | ~462 KB |
| Datos faltantes FLAIR | No aplica | Sí (35% en muestra original) |

---

## Características de la interfaz

- Bilingüe ES / EN con selector de idioma integrado
- Cálculo automático en tiempo real (sin botón de calcular)
- Botón de inicio (🏠) en ambas calculadoras enlazando a la página principal
- Guías de medición con imágenes de RM reales embebidas (acordeón desplegable)
- Diseño responsive apto para dispositivos móviles
- Sin cookies, sin tracking, sin anuncios

---

## Limitaciones · Limitations

- Muestra pequeña (n = 40–46): riesgo de sobreajuste pese a validación interna favorable
- EPV = 6.7 en Calculadora A (< umbral clásico de 10)
- Validación interna únicamente — **pendiente validación externa en cohortes independientes**
- Datos faltantes manejados con análisis de casos completos (posible sesgo de selección)
- Prevalencia de referencia derivada de la muestra del estudio, no de prevalencia poblacional

---

## Software estadístico · Statistical software

Python 3.11: `scikit-learn 1.x` · `statsmodels 0.14` · `scipy 1.x` · `numpy`

---

## Citación · Citation

Si utiliza estas herramientas en investigación / If you use these tools in research:

> Perera, D. (en preparación). *Modelos predictivos de esclerosis hipocampal mediante morfometría por resonancia magnética de alta resolución*. Investigación en proceso. Neurocirugía Funcional.

---

## Contacto · Contact

**Dr. Doriam Perera** · Neurocirugía Funcional / Functional Neurosurgery

---

*Herramientas de uso exclusivamente exploratorio. No aptas para uso clínico autónomo.*  
*Exploratory use only. Not suitable for autonomous clinical use.*
