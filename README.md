# Poultry Farm Ventilation Controller (Fuzzy Logic System)

![Python](https://img.shields.io/badge/Python-3.8%2B-blue?logo=python&logoColor=white)
![scikit-fuzzy](https://img.shields.io/badge/Library-scikit--fuzzy-orange)
![NumPy](https://img.shields.io/badge/NumPy-1.20%2B-013243?logo=numpy&logoColor=white)
![Matplotlib](https://img.shields.io/badge/Matplotlib-Visualization-11557c)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

A **Mamdani Fuzzy Logic Control (FLC)** system built with Python and `scikit-fuzzy` to automate exhaust fan speed in commercial poultry houses. The controller simultaneously evaluates **ambient temperature**, **relative humidity**, and **ammonia ($\text{NH}_3$) concentration** to prevent heat stress and toxic gas buildup while conserving energy during cool, clean-air conditions.

---

## Key Features
* **Multi-Variable Mamdani Inference:** Evaluates 3 crisp environmental inputs across a complete **27-rule knowledge base** using `min` implication and `max` aggregation.
* **Centroid Defuzzification:** Computes smooth, continuous fan speed percentages ($0\text{--}100\%$) rather than abrupt on/off relay switching.
* **Comprehensive Diagnostic Suite:** Includes 2D membership plots, per-case fuzzification breakdowns, rule firing-strength bar charts, 3D control surfaces, and single-variable sensitivity analysis.

---

## System Architecture & Membership Functions

All input and output variables are modeled using **Triangular Membership Functions (`trimf`)**:

| Variable | Type | Range | Linguistic Terms & Triangular Parameters `[a, b, c]` |
| :--- | :--- | :--- | :--- |
| **Temperature** | Input | $15\text{--}45\ ^\circ\text{C}$ | **Low:** `[15, 15, 27]` • **Medium:** `[22, 30, 38]` • **High:** `[33, 45, 45]` |
| **Humidity** | Input | $30\text{--}100\ \%$ | **Low:** `[30, 30, 55]` • **Medium:** `[45, 65, 85]` • **High:** `[75, 100, 100]` |
| **Ammonia ($\text{NH}_3$)** | Input | $0\text{--}50\ \text{ppm}$ | **Low:** `[0, 0, 18]` • **Medium:** `[12, 25, 38]` • **High:** `[30, 50, 50]` |
| **Fan Speed** | Output | $0\text{--}100\ \%$ | **Very Low:** `[0, 0, 25]` • **Low:** `[10, 30, 50]` • **Medium:** `[40, 55, 70]`<br>**High:** `[60, 80, 95]` • **Very High:** `[80, 100, 100]` |

![Membership Functions](assets/membership_functions.png)

---

## Fuzzy Rule Base (27 Rules)

The rule matrix balances thermal comfort with air quality. Even at low temperatures, elevated ammonia or high humidity forces minimum ventilation to purge toxic gases and moisture.

### 1. When Temperature is **LOW**
| Humidity \ Ammonia | Low $\text{NH}_3$ | Medium $\text{NH}_3$ | High $\text{NH}_3$ |
| :--- | :---: | :---: | :---: |
| **Low Humidity** | Very Low | Low | Medium |
| **Medium Humidity** | Very Low | Low | Medium |
| **High Humidity** | Medium | Medium | High |

### 2. When Temperature is **MEDIUM**
| Humidity \ Ammonia | Low $\text{NH}_3$ | Medium $\text{NH}_3$ | High $\text{NH}_3$ |
| :--- | :---: | :---: | :---: |
| **Low Humidity** | Low | Medium | High |
| **Medium Humidity** | Medium | Medium | High |
| **High Humidity** | High | High | Very High |

### 3. When Temperature is **HIGH**
| Humidity \ Ammonia | Low $\text{NH}_3$ | Medium $\text{NH}_3$ | High $\text{NH}_3$ |
| :--- | :---: | :---: | :---: |
| **Low Humidity** | Medium | High | Very High |
| **Medium Humidity** | High | Very High | Very High |
| **High Humidity** | Very High | Very High | Very High |

---

## Simulation & Benchmark Test Cases

The controller is verified against 5 representative poultry house scenarios:

| Scenario | Temp ($^\circ\text{C}$) | Humidity ($\%$) | Ammonia ($\text{ppm}$) | Expected Behavior |
| :--- | :---: | :---: | :---: | :--- |
| **1. Cool & dry, clean air** | `20` | `40` | `5` | **Very Low** (conserve heat & power) |
| **2. Moderate all-round** | `25` | `65` | `25` | **Medium** (steady baseline airflow) |
| **3. Hot, humid, high $\text{NH}_3$** | `32` | `80` | `35` | **High / Very High** (active cooling & purging) |
| **4. Worst-case scenario** | `40` | `90` | `45` | **Very High** (maximum emergency ventilation) |
| **5. Cool but high ammonia** | `18` | `75` | `42` | **Medium–High** (prioritize gas removal over heat) |

![Centroid Defuzzification Across Test Cases](assets/centroid_defuzzification.png)

---

## System Visualizations

### 3D Control Surfaces
Demonstrates the non-linear control response across continuous input spaces:
![3D Control Surfaces](assets/control_surfaces_3d.png)

### One-at-a-Time Sensitivity Analysis
Illustrates how fan speed responds when isolating a single environmental variable while holding the other two at nominal baseline values ($T = 30^\circ\text{C}$, $H = 65\%$, $\text{NH}_3 = 25\text{ ppm}$):
![Sensitivity Analysis](assets/sensitivity_analysis.png)

---

## Installation & Usage

### 1. Clone the repository
```bash
git clone [https://github.com/YOUR-USERNAME/poultry-ventilation-fuzzy-controller.git](https://github.com/YOUR-USERNAME/poultry-ventilation-fuzzy-controller.git)
cd poultry-ventilation-fuzzy-controller
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Run the controller
Open `ventilation_controller.ipynb` in VS Code or Jupyter Notebook, or run the Python script directly:
```python
from ventilation_controller import infer

# Evaluate a custom sensor reading (Temp=32°C, Humidity=80%, Ammonia=35ppm)
fan_speed = infer(crisp_temp=32, crisp_humid=80, crisp_ammonia=35, plot=True)
print(f"Recommended Fan Speed: {fan_speed:.2f}%")
```
