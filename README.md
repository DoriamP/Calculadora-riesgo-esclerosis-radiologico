# 🧠 Hippocampal Sclerosis Calculator / Calculadora de Esclerosis Hipocampal

**Bilingual clinical decision-support tool based on a logistic regression predictive model**  
**Herramienta clínica bilingüe de apoyo diagnóstico basada en regresión logística**

> ⚕️ *Dr. Doriam Perera Valdivia — Functional Neurosurgeon / Neurocirujano Funcional*  
> *Part of an exploratory clinical research study / Parte de un estudio exploratorio clínico*

---

## ✨ Features / Características

| Feature | Descripción |
|---------|-------------|
| 🌐 **Bilingual** (ES/EN) | Toggle between Spanish and English with one tap |
| ⚡ **Live calculation** | Probability updates automatically as values are entered |
| 📈 **Regression curve** | Visual risk zones (Low / Indeterminate / Moderate / High) with patient marker |
| 🔵🟡 **Lateralization** | Automatically identifies most affected hippocampus |
| 📊 **Clinical nomogram** | Collapsible section with embedded nomogram image |
| 📱 **Mobile-first** | Fully responsive, optimized for smartphones and tablets |
| 🔬 **Illustrated guide** | How-to section with MRI measurement images |
| 📥 **Standalone file** | All images embedded as base64 — no server required |

---

## 🚀 Deployment / Despliegue

### GitHub Pages (recommended)
1. Upload this folder to a GitHub repository
2. Go to **Settings → Pages → Source: main / root**
3. Access at: `https://[username].github.io/[repo]/`

### Local use
Open `index.html` in any modern browser — no installation needed.

### Netlify Drop
Drag the folder to [netlify.com/drop](https://netlify.com/drop).

---

## 📐 Model variables

| Variable | Description | Unit | Std. Coefficient |
|----------|-------------|------|-----------------|
| Δ% Volume | Total hippocampal volume asymmetry | mm³ | **1.680** ⭐⭐⭐ |
| Δ% Dentate Gyrus | Max dentate gyrus thickness (head, coronal) | mm | **0.997** ⭐⭐ |
| Δ% Coronal Angle | Hippocampal head angle (coronal plane) | ° | **0.984** ⭐⭐ |
| Δ% CA1 | Max CA1 thickness (head, coronal) | mm | **0.672** ⭐ |

**Asymmetry formula:** `|left − right| / max(left, right) × 100`

---

## 📊 Diagnostic accuracy (LOO-CV validation)

| Metric | Value |
|--------|-------|
| **AUC-ROC** | **0.997** |
| Accuracy | 97.5% |
| Sensitivity | 95.0% |
| Specificity | 100.0% |
| PPV | 100.0% |
| NPV | 95.2% |
| Optimal threshold | 0.517 |
| Sample | n = 40 (20 HS / 20 controls) |
| Validation | Leave-One-Out Cross-Validation |

---

## 📁 Repository structure

```
hipocampo_calculator/
├── index.html                              # Bilingual web calculator (self-contained)
├── nomograma_hipocampal_publicacion.pdf    # High-res bilingual nomogram for publication
├── nomograma_esclerosis_hipocampal.pdf     # Spanish nomogram (previous version)
├── README.md                              # This file
└── images/                                # Reference images (copies)
    └── image1.png … image10.png
```

---

## ⚕️ Disclaimer

Investigational tool. Does **not** replace clinical judgment or definitive diagnosis based on neurological, EEG, neuropsychological, neuroimaging, and histopathological evaluation.

---

**Dr. Doriam Perera Valdivia** — Functional Neurosurgeon · Exploratory Clinical Study  
*Logistic Regression | LOO-CV | AUC-ROC = 0.997 | n = 40*
