# Calculadora-riesgo-esclerosis-radiologico
# 🧠 Calculadora Clínica de Riesgo de Esclerosis Hipocampal

**Herramienta web exploratoria basada en modelo de regresión logística regularizada con validación cruzada leave-one-out.**

> ⚠️ **Aviso:** Esta herramienta es exclusivamente una prueba exploratoria derivada de una investigación clínica preliminar. No debe utilizarse para tomar decisiones diagnósticas o terapéuticas en pacientes reales.

---

## 📋 Descripción

Calculadora clínica de una sola página (`calculadora_esclerosis_hipocampal.html`) que estima la probabilidad de esclerosis hipocampal a partir de parámetros morfométricos y de asimetría obtenidos por resonancia magnética de alta resolución.

El usuario ingresa los valores **bilaterales** (izquierdo y derecho) de cada variable. La calculadora computa automáticamente la diferencia porcentual entre ambos lados y estima la probabilidad diagnóstica en tiempo real, sin necesidad de conexión a internet ni instalación de software.

---

## ✨ Características

- ✅ **Sin dependencias externas** — archivo HTML único, funciona offline
- ✅ **Ingreso bilateral** — valores izquierdo/derecho con cálculo automático de asimetría
- ✅ **Resultado en tiempo real** — probabilidad, veredicto clínico y análisis bayesiano actualizados al instante
- ✅ **Responsive y mobile-friendly** — optimizado para tablet y smartphone
- ✅ **Curva ROC integrada** — visualización del rendimiento diagnóstico
- ✅ **Desglose de contribuciones** — transparencia en la ponderación de cada variable
- ✅ **Nota metodológica** — descripción del proceso de construcción del modelo

---

## 🚀 Uso

1. Descargue el archivo `calculadora_esclerosis_hipocampal.html`
2. Ábralo en cualquier navegador moderno (Chrome, Firefox, Safari, Edge)
3. Ingrese los valores bilaterales de cada variable en los campos correspondientes
4. Lea el resultado automático en la sección **"Resultado del modelo"**

No requiere servidor web, instalación ni conexión a internet.

---

## 📊 Variables del Modelo

El modelo utiliza **5 índices de asimetría bilateral** (diferencia porcentual |Der – Izq| / Máximo × 100):

| Variable | β | OR | AUC univariante |
|---|---|---|---|
| Asimetría de volumen hipocampal (%) | 1.327 | 3.77 | 0.945 |
| Asimetría del ángulo coronal hipocampal (%) | 1.125 | 3.08 | 0.860 |
| Asimetría del grosor del giro dentado (%) | 1.033 | 2.81 | 0.885 |
| Asimetría del grosor CA1 (%) | 0.747 | 2.11 | 0.710 |
| Asimetría de intensidad FLAIR (%) | 0.206 | 1.23 | 0.729 |

**Intercepto del modelo:** 0.287

**Variables excluidas por multicolinealidad** (r > 0.70):
- Volumen hipocampo izquierdo (absoluto)
- Ángulo coronal cabeza hipocampo derecho (absoluto)
- Ángulo coronal cabeza hipocampo izquierdo (absoluto)

---

## 📈 Rendimiento Diagnóstico (Validación LOO-CV)

| Métrica | Valor |
|---|---|
| **AUC** | **0.970** |
| IC 95% (bootstrap) | 0.90 – 1.00 |
| Sensibilidad | 92.9% |
| Especificidad | 100% |
| Valor predictivo positivo (VPP) | 100% |
| Valor predictivo negativo (VPN) | 94.3% |
| Exactitud | 96.5% |
| Brier Score | 0.132 |
| n | 46 |

---

## 🎯 Criterios de Clasificación

| Probabilidad estimada | Clasificación |
|---|---|
| **> 60%** | ⚠️ **Alta probabilidad** de esclerosis hipocampal |
| 35% – 60% | △ **Probabilidad intermedia** — seguimiento recomendado |
| **< 35%** | ✓ **Baja probabilidad** de esclerosis hipocampal |

> **Nota:** El umbral estadístico óptimo por índice de Youden es 37.6% (sensibilidad 92.9%, especificidad 100%). El umbral clínico de *alta probabilidad* ha sido fijado en 60% por criterio del investigador principal.

---

## 🔬 Metodología Resumida

### Población
Cohorte de **46 pacientes** (21 con esclerosis hipocampal confirmada, 25 controles) sometidos a resonancia magnética de alta resolución con protocolo hipocampal: secuencias volumétricas T1, morfometría subcampal y FLAIR coronal oblicuo.

### Construcción del modelo
1. **Análisis univariante:** prueba de Mann-Whitney U y AUC individual por variable. Se retuvieron predictores con AUC > 0.66 y p < 0.05.
2. **Control de multicolinealidad:** exclusión de variables con correlación cruzada r > 0.70.
3. **Modelo:** regresión logística regularizada con penalización Ridge (L2), selección del parámetro de regularización C mediante 5-fold cross-validation.
4. **Validación interna:** Leave-One-Out Cross-Validation (LOO-CV) — el modelo fue reentrenado n = 46 veces, evaluando cada caso como conjunto de prueba independiente.
5. **Calibración:** Brier Score = 0.132. La calibración formal (Hosmer-Lemeshow) requiere validación externa en cohorte independiente.
6. **Umbral de decisión estadístico:** 37.6%, seleccionado por maximización del índice de Youden.

### Fórmula del modelo
```
logit(P) = 0.287 + 1.327·z(vol) + 1.125·z(ang) + 1.033·z(dent) + 0.747·z(CA1) + 0.206·z(FLAIR)
P = 1 / (1 + e^(-logit))
```
Donde `z(x) = (x - media) / DE` es la puntuación estandarizada de cada índice de asimetría.

### Análisis bayesiano
La herramienta reporta probabilidades posteriores usando la prevalencia muestral observada (45.8%, CrI 95%: 32.1–59.9%) como distribución *a priori* Beta(23, 27).

---

## 🛠️ Implementación Técnica

- **Lenguaje:** HTML5 + CSS3 + JavaScript vanilla (sin frameworks)
- **Tipografías:** Source Sans 3, JetBrains Mono, Lora (Google Fonts — requiere conexión en la primera carga para fuentes; la lógica del modelo funciona offline)
- **Gráfico ROC:** Canvas 2D API nativo
- **Tamaño:** ~50 KB (archivo único)
- **Compatibilidad:** Chrome 90+, Firefox 88+, Safari 14+, Edge 90+, iOS Safari, Android Chrome

---

## 📁 Estructura del Repositorio

```
├── calculadora_esclerosis_hipocampal.html   # Aplicación principal
├── nomogram_hippocampal_sclerosis.pdf       # Nomograma clínico (formato publicación)
└── README.md                                # Este archivo
```

---

## ⚠️ Limitaciones

- Muestra de desarrollo pequeña (n = 46); el rendimiento real en poblaciones más amplias puede diferir.
- Validación exclusivamente interna (LOO-CV); se requiere validación externa en cohorte independiente antes de cualquier aplicación clínica.
- Los intervalos de confianza de los OR son aproximados.
- La calibración formal (Hosmer-Lemeshow) no fue posible evaluar con suficiente potencia en la muestra disponible.

---

## 👨‍⚕️ Autoría

**Dr. Doriam Perera**  
Investigación clínica en neuroimagen hipocampal  

> *Herramienta de prueba exploratoria de una investigación clínica realizada por el Dr. Doriam Perera. No validada para uso clínico.*

---

## 📄 Licencia

Este proyecto se distribuye con fines exclusivamente académicos y de investigación. Queda prohibido su uso para diagnóstico clínico sin validación externa apropiada y sin supervisión especializada.

---

*Última actualización: mayo 2026*
