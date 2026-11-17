# Technical Test Report — Machine Learning & Optimization

This repository contains reproducible code, data, and the final PDF report for two machine learning tasks:

1. **Question 1:** Predicting Airfoil Surrogate (`q1.ipynb`)
2. **Question 2:** Low-Cement Concrete Mix Optimization (`q2.ipynb`)

---

## Project Structure

```text
├── data/
│   ├── airfoil_self_noise.csv
│   ├── Concrete_Data.xls
│   └── q2_concrete_compressive_strength.csv
├── q1.ipynb
├── q2.ipynb
├── report.pdf
├── requirements.txt
├── .gitignore
└── README.md
```

---

## How to Run

### 1. Create and Activate a Virtual Environment

This project uses **Python 3.11**.

Open a terminal in the project folder and create a virtual environment.

**Linux / macOS:**

```bash
python3.11 -m venv venv
source venv/bin/activate
```

**Windows (Command Prompt):**

```cmd
py -3.11 -m venv venv
venv\Scripts\activate
```

**Windows (PowerShell):**

```powershell
py -3.11 -m venv venv
.\venv\Scripts\Activate.ps1
```

### 2. Install Required Packages

Install all required libraries from `requirements.txt`.

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 3. Run the Notebooks

Launch Jupyter Notebook or open the `.ipynb` files in VS Code using the Python 3.11 virtual environment kernel.

```bash
jupyter notebook
```

Run `q1.ipynb` and `q2.ipynb` from top to bottom.

All random seeds are fixed (`seed = 42`) so the results match `report.pdf`.

---

## How to Remove the Virtual Environment After Running

Once you finish running the notebooks, deactivate and delete the virtual environment folder to free up disk space.

**Linux / macOS:**

```bash
deactivate
rm -rf venv
```

**Windows (Command Prompt):**

```cmd
deactivate
rmdir /s /q venv
```

**Windows (PowerShell):**

```powershell
deactivate
Remove-Item -Recurse -Force venv
```

---

## Key Assumptions

### Question 1: Airfoil Self-Noise Prediction

The raw dataset contains six columns recorded from wind-tunnel tests on NACA 0012 airfoils. To prevent data leakage and match early blade design conditions, the following variable assumptions were defined before modeling:

- **Known Design Inputs:** `angle_of_attack_deg`, `chord_length_m`, and `free_stream_velocity_m_per_s` are included because the engineering team determines the blade angle, chord length, and wind speed before conducting a wind-tunnel test.

- **Frequency Input:** `frequency_hz` is included because the engineering team can select the sound frequencies at which noise predictions are required. For each blade configuration, the model predicts noise across standard frequencies (e.g., 200 Hz to 20,000 Hz).

- **Excluded Leakage Feature:** `suction_side_displacement_thickness_m` is excluded from training because it represents the thickness of the slowed-down airflow layer on the upper surface of the blade. This value is not directly available during early blade design and requires additional computational simulations or wind-tunnel measurements. Excluding it allows the model to function as a faster screening tool using only basic design parameters.

- **Prediction Target:** `scaled_sound_pressure_level_db` is the target variable. It is assumed that wind-tunnel sensors accurately measure the noise level under consistent experimental conditions.

- **Consistent Test Conditions:** All measurements are assumed to use the same NACA 0012 airfoil shape, blade width, and microphone position, with minimal measurement error.

- **Grouped Configurations:** Repeated frequency measurements from the same physical blade configuration (`config_id`) remain together in either the training set or the test set to prevent data leakage.

### Question 2: Low-Cement Concrete Mix Optimization

The dataset contains nine columns representing concrete ingredient weights (in kg/m³), curing age (in days), and measured compressive strength (in MPa). To reflect laboratory batch design and prevent data leakage, the following variable assumptions were defined before modeling:

- **Controllable Ingredients:** The materials team controls the exact weights of all seven ingredients: `cement_kg_per_m3`, `blast_furnace_slag_kg_per_m3`, `fly_ash_kg_per_m3`, `water_kg_per_m3`, `superplasticizer_kg_per_m3`, `coarse_aggregate_kg_per_m3`, and `fine_aggregate_kg_per_m3`. These are treated as decision variables during optimization.

- **Cement Minimization:** `cement_kg_per_m3` is the primary optimization objective. The goal is to minimize cement usage while maintaining the required concrete compressive strength.

- **Cement Replacement Materials:** Blast furnace slag and fly ash are treated as controllable supplementary cementitious materials that can partially replace cement and contribute to concrete strength development.

- **Water Content:** `water_kg_per_m3` is treated as a decision variable. Water is necessary for cement hydration, while excessive water can reduce the resulting concrete strength.

- **Superplasticizer:** `superplasticizer_kg_per_m3` is treated as a decision variable. It is assumed that this additive can improve concrete workability when using lower water content, without introducing external cost or flow constraints.

- **Aggregate Selection:** `coarse_aggregate_kg_per_m3` and `fine_aggregate_kg_per_m3` are treated as decision variables representing the selected weights of gravel or crushed stone and sand, respectively.

- **Fixed Curing Age:** The model learns strength development across all available curing ages in the dataset (1 to 365 days). However, `age_days` is fixed at 28 during optimization because every recommended candidate mix is evaluated at 28 days in the laboratory.

- **Prediction Target:** `compressive_strength_mpa` is the target variable. Laboratory measurements are assumed to be accurate and obtained under consistent material quality and standard curing conditions.

- **Minimum Strength Requirement:** Every recommended concrete mix must achieve at least 40.0 MPa of compressive strength at 28 days. A model safety buffer above this threshold is applied to account for prediction uncertainty.

- **Physical and Data-Derived Constraints:** Ingredient quantities are restricted to their training-data ranges. Total concrete mass is constrained between 2,229 and 2,449 kg/m³, based on the central 90% of the training data. The water-to-binder ratio is restricted to 0.29–0.67 to maintain realistic mix proportions.

- **Laboratory Testing Budget:** A maximum of three candidate mixes can be selected for laboratory validation due to limited testing time and budget.

- **Out-of-Scope Factors:** Workability, long-term durability, chemical grades of materials, non-cement costs, and local building codes are not explicitly modeled because these factors are not available in the dataset. They must be checked during laboratory validation before practical implementation.

---

## AI / Tool Disclosure

AI assistance (Gemini) was used during this project under clear academic and technical integrity rules.

### 1. Categories of AI Assistance
* **Idea & Concept Discussion:** Explaining technical concepts such as decision-tree step behavior, bootstrap ensemble uncertainty, Euclidean distance for extrapolation checks, and `differential_evolution` optimization.
* **Script Generation & Debugging:** Writing and debugging Python code for cross-validation loops, error-anaylsis, 30-model bootstrap uncertainty estimation, optimization penalty functions, nearest-neighbor distance checks.
* **Editing & Formatting:** Rewriting technical findings, chart explanations, failure risks, and lab validation steps into simple sentences and easy to understand, and formatting tables and formulas in Markdown and LaTeX (`report.pdf`).

### 2. Compliance & Verification Rules
* **Permitted Activities:** Explaining concepts, debugging scripts, structuring reports, and improving sentence clarity.
* **Strict Exclusions:** Core problem formulation, final model selection, optimization constraints, and optimization constraints were decided independently without AI substitution.
* **Independent Verification:** All AI-assisted code was run locally, and every data split, error metric, table, and conclusion was checked and verified against the actual dataset outputs.