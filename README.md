# ECE2112---EXPERIMENT-4-DATA-WRANGLING-AND-VISUALIZATION
# Seiya A. Gawat | 2ECE-A

---

## 📋 Table of Contents
- [Overview](#-overview)
- [Data Wrangling & Visualization Summary](#-data-wrangling--visualization-summary)
- [Problem Specifications & Solutions](#-problem-specifications--solutions)
  - [A. Visayas Communication DataFrame](#a-visayas-communication-dataframe)
  - [B. Visayas Female DataFrame](#b-visayas-female-dataframe)
  - [C. Category-Average Visualization & Interpretation](#c-category-average-visualization--interpretation)
- [Project File Structure](#-project-file-structure)
- [Prerequisites & Requirements](#-prerequisites--requirements)
- [How to Run](#-how-to-run)
  - [Using Jupyter Notebook](#using-jupyter-notebook)
  - [Using Terminal / Command Prompt](#using-terminal--command-prompt)
- [Edge Cases & Key Considerations](#-edge-cases--key-considerations)

---

## 📌 Overview

This repository contains Python solutions for **Experiment 4: Data Wrangling and Data Visualization**[cite: 11]. The activity focuses on analyzing ECE Board Exam performance data by applying complex multi-condition filtering, feature selection, category aggregation, and clear multi-panel plotting[cite: 11]. Key concepts demonstrated include:
* Feature engineering (deriving the composite `Average` score across subject components)[cite: 11]
* Explicit boolean multi-condition indexing (`&`)[cite: 11]
* Column selection and non-destructive secondary filtering[cite: 11]
* Categorical grouping and mean aggregation (`.groupby()`)[cite: 11]
* Comparative data visualization using `matplotlib` and `seaborn` subplots[cite: 11]

---

## ⚙️ Data Wrangling & Visualization Summary

| Operation / Method | Syntax Example | Return Type | Key Logic |
| :--- | :--- | :--- | :--- |
| Composite Calculation | `df[['Math', 'Electronics', ...]].mean(axis=1)` | `Series` | Derives the overall `Average` score for each student across all subjects[cite: 11]. |
| Multi-Condition Filter | `df[(df['Hometown']=='Visayas') & (df['Track']=='Comm')]` | `DataFrame` | Filters rows matching both categorical conditions explicitly[cite: 11]. |
| Group Aggregation | `df.groupby('Track')['Average'].mean().reset_index()` | `DataFrame` | Computes the sample mean score for each distinct category[cite: 11]. |
| Multi-Panel Plotting | `fig, axes = plt.subplots(1, 3, figsize=(16, 5))` | `Figure, Axes` | Constructs a 3-panel side-by-side figure sharing a uniform y-axis scale[cite: 11]. |

---

## 💻 Problem Specifications & Solutions

### Setup & Data Import

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Set visual style
sns.set_theme(style="whitegrid")

# Load dataset
try:
    df = pd.read_csv('board2.csv')
except FileNotFoundError:
    df = pd.read_excel('board2.xlsx')

# Compute the required 'Average' column across all subject scores
df['Average'] = df[['Math', 'Electronics', 'GEAS', 'Communication']].mean(axis=1)
```

---

### A. Visayas Communication DataFrame
**Requirement:** Create a DataFrame named `VisComm` containing students whose `Hometown` is `Visayas` AND whose `Track` is `Communication`[cite: 11]. Retain only `Name`, `Gender`, `Math`, `Electronics`, and `Average` in that exact order[cite: 11]. Display the DataFrame and its total row count[cite: 11].

```python
# Explicit multi-condition filter
vis_comm_mask = (df['Hometown'] == 'Visayas') & (df['Track'] == 'Communication')

# Feature selection in required column order
VisComm = df[vis_comm_mask][['Name', 'Gender', 'Math', 'Electronics', 'Average']]

# Display output and row count
print("--- Visayas Communication DataFrame (VisComm) ---")
display(VisComm)
print(f"\nTotal number of rows in VisComm: {len(VisComm)}")
```

---

### B. Visayas Female DataFrame
**Requirement:** Create a DataFrame named `VisFemale` containing female students (`Gender == 'Female'`) from `Visayas` (`Hometown == 'Visayas'`)[cite: 11]. Retain columns `Name`, `Track`, `GEAS`, `Electronics`, and `Average`[cite: 11]. Display `VisFemale`, then display a subset of rows where `Average >= 60` without overwriting the original `VisFemale` DataFrame[cite: 11].

```python
# Explicit filter for female students from Visayas
vis_female_mask = (df['Hometown'] == 'Visayas') & (df['Gender'] == 'Female')

# Select specified features
VisFemale = df[vis_female_mask][['Name', 'Track', 'GEAS', 'Electronics', 'Average']]

# 1. Display complete VisFemale DataFrame
print("--- Visayas Female DataFrame (VisFemale) ---")
display(VisFemale)

# 2. Display secondary filter without mutating VisFemale
print("\n--- VisFemale Students with Average Score >= 60 ---")
display(VisFemale[VisFemale['Average'] >= 60])
```

---

### C. Category-Average Visualization & Interpretation
**Requirement:** Compute category sample means of `Average` score across `Track`, `Gender`, and `Hometown`[cite: 11]. Display the 3 summary tables[cite: 11]. Create a single figure with three bar charts on a consistent scale[cite: 11]. Provide 3 concise statements identifying the category with the highest sample mean for each feature[cite: 11].

```python
# a & b. Calculate and display summary tables
track_mean = df.groupby('Track')['Average'].mean().reset_index()
gender_mean = df.groupby('Gender')['Average'].mean().reset_index()
hometown_mean = df.groupby('Hometown')['Average'].mean().reset_index()

print("=== Summary Table: Mean Average Score by Track ===")
display(track_mean)

print("\n=== Summary Table: Mean Average Score by Gender ===")
display(gender_mean)

print("\n=== Summary Table: Mean Average Score by Hometown ===")
display(hometown_mean)

# c. Create figure with 3 bar charts side-by-side
fig, axes = plt.subplots(1, 3, figsize=(16, 5), sharey=True)

# 1. Track Chart
sns.barplot(data=track_mean, x='Track', y='Average', ax=axes[0], hue='Track', legend=False, palette='Blues_d')
axes[0].set_title('Mean Average Board Score by Track', fontsize=12, fontweight='bold')
axes[0].set_xlabel('Track', fontsize=11)
axes[0].set_ylabel('Mean Board Score', fontsize=11)
axes[0].set_ylim(0, 100)

for p in axes[0].patches:
    axes[0].annotate(f'{p.get_height():.2f}', (p.get_x() + p.get_width() / 2., p.get_height()), 
                     ha='center', va='bottom', xytext=(0, 3), textcoords='offset points', fontsize=10)

# 2. Gender Chart
sns.barplot(data=gender_mean, x='Gender', y='Average', ax=axes[1], hue='Gender', legend=False, palette='Greens_d')
axes[1].set_title('Mean Average Board Score by Gender', fontsize=12, fontweight='bold')
axes[1].set_xlabel('Gender', fontsize=11)
axes[1].set_ylabel('Mean Board Score', fontsize=11)

for p in axes[1].patches:
    axes[1].annotate(f'{p.get_height():.2f}', (p.get_x() + p.get_width() / 2., p.get_height()), 
                     ha='center', va='bottom', xytext=(0, 3), textcoords='offset points', fontsize=10)

# 3. Hometown Chart
sns.barplot(data=hometown_mean, x='Hometown', y='Average', ax=axes[2], hue='Hometown', legend=False, palette='Oranges_d')
axes[2].set_title('Mean Average Board Score by Hometown', fontsize=12, fontweight='bold')
axes[2].set_xlabel('Hometown', fontsize=11)
axes[2].set_ylabel('Mean Board Score', fontsize=11)

for p in axes[2].patches:
    axes[2].annotate(f'{p.get_height():.2f}', (p.get_x() + p.get_width() / 2., p.get_height()), 
                     ha='center', va='bottom', xytext=(0, 3), textcoords='offset points', fontsize=10)

plt.suptitle('ECE Board Exam Category Performance Analysis', fontsize=14, fontweight='bold', y=1.03)
plt.tight_layout()
plt.show()
```

#### d. Interpretation Statements
1. **Track:** In the observed dataset, students enrolled in the **Communication** track recorded the highest sample mean for overall board exam score (`67.98`)[cite: 11].
2. **Gender:** Among all recorded genders, **Male** students achieved the highest sample mean overall score (`67.18`)[cite: 11].
3. **Hometown:** Across student origin regions, students whose hometown is **Luzon** recorded the highest sample mean board score (`68.08`)[cite: 11].

*Note: As per experiment instructions, these findings purely describe sample statistics within this observed dataset and do not establish causal relationships between student features and exam performance[cite: 11].*

---

## 📁 Project File Structure

```text
.
├── board2.csv                # Dataset file containing ECE Board Exam scores
├── board2.xlsx               # Excel version of dataset
├── Experiment4_Gawat.ipynb   # Main Jupyter Notebook containing executed solutions
└── README.md                 # Project documentation file
```

---

## 🛠️ Prerequisites & Requirements

* **Python 3.8+**
* **Pandas** (`pip install pandas`)
* **Matplotlib** (`pip install matplotlib`)
* **Seaborn** (`pip install seaborn`)
* **Jupyter Notebook / Anaconda**

---

## 🚀 How to Run

### Using Jupyter Notebook
1. Ensure `board2.csv` (or `board2.xlsx`) and `Experiment4_Gawat.ipynb` are placed in the **same folder**.
2. Launch Jupyter Notebook:
   ```bash
   jupyter notebook
   ```
3. Open `Experiment4_Gawat.ipynb`.
4. Run all cells sequentially (**Cell** -> **Run All**).

### Using Terminal / Command Prompt
1. Open a terminal in the project directory.
2. Execute the notebook via `nbconvert`:
   ```bash
   python -m jupyter nbconvert --to notebook --execute Experiment4_Gawat.ipynb
   ```

---

## 🛡️ Edge Cases & Key Considerations

* **Derived `Average` Attribute**: Raw source dataset files do not include an `Average` column. Computing `df[['Math', 'Electronics', 'GEAS', 'Communication']].mean(axis=1)` at the beginning prevents `KeyError: "['Average'] not in index"` exceptions[cite: 12].
* **Non-Destructive Slicing**: Filtering `VisFemale[VisFemale['Average'] >= 60]` is performed dynamically without reassigning or overwriting `VisFemale`, keeping the parent DataFrame complete[cite: 11].
* **Explicit Operator Precedence**: All multi-condition Boolean expressions enclose individual filters in parentheses (e.g., `(df['A'] == x) & (df['B'] == y)`) to avoid Python bitwise operator precedence errors[cite: 11].
* **Standardized Axis Scales**: All three subplots explicitly enforce `ylim(0, 100)` to ensure fair and accurate visual comparisons across categories[cite: 11].
