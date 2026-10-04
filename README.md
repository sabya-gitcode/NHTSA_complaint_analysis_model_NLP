# NHTSA_complaint_analysis_model_NLP

# Automotive Complaint Intelligence

An NLP and early-warning analysis of NHTSA vehicle complaint narratives.

In this project, I collected consumer complaints for five 2021 vehicle models and used them to answer two questions:

1. Can a complaint narrative be classified into its reported vehicle component?
2. Can unusual increases in vehicle-component complaints be flagged for investigation?

The aim is not to diagnose a defect or predict recalls. It is to build a simple complaint-monitoring workflow that could help an automotive quality or safety team decide where to look more closely.

---

## Vehicles included

- Honda Accord
- Toyota Camry
- Ford Explorer
- Tesla Model 3
- Hyundai Elantra

All vehicles are from model year 2021.

---

## Data

The data was collected from the public NHTSA Complaints API.

- **1,617** raw complaints collected
- **1,048** complaints with one official component label
- **858** final complaint narratives used for NLP modelling
- **10** component categories
- Complaint filing dates ranged from **February 2021 to September 2026**

I used only single-component complaints in this version so that the classification task remained a clear multiclass NLP problem.

---

## What the notebook does

### 1. Collects complaint data

The notebook requests complaint data for the five selected vehicles through the NHTSA API (https://api.nhtsa.gov/complaints/complaintsByVehicle)

### 2. Cleans the complaint narratives

- Checks missing values
- Keeps complaints with one component label
- Removes text containing explicit `COMPONENT:` wording to avoid label leakage
- Cleans the narrative text for modelling

### 3. Classifies complaint components

The baseline model uses:

- TF-IDF text vectorization
- Logistic Regression
- An 80/20 stratified train-test split
- Balanced class weights to account for uneven class sizes

### 4. Looks for complaint spikes

Monthly complaint counts are calculated for every vehicle-component combination.

A warning is created when a month has:

- At least 3 complaints
- At least double the previous three-month average
- A previous three-month average of at least 1 complaint

### 5. Investigates the language behind major alerts

For high-priority alerts, the notebook checks whether repeated themes appear in the complaint narratives.

---

## NLP model results

| Metric | Score |
|---|---:|
| Accuracy | 0.744 |
| Macro F1-score | 0.659 |
| Training narratives | 686 |
| Test narratives | 172 |

The model handled distinctive categories such as **Forward Collision Avoidance**, **Power Train**, **Air Bags**, and **Engine** relatively well.

It was less reliable for broader or overlapping categories such as **Electrical System**, **Service Brakes**, **Structure**, and **Unknown or Other**. These categories had fewer examples and often used similar language.

---

## Main finding: Tesla Model 3 warning pattern

The strongest warning pattern involved **Forward Collision Avoidance complaints for the 2021 Tesla Model 3**.

| Month | Complaints | Previous 3-month average | Increase |
|---|---:|---:|---:|
| November 2021 | 16 | 1.0 | 16.0× |
| February 2022 | 70 | 7.3 | 9.5× |

The November 2021 increase would have been flagged before the much larger February 2022 spike.

The February complaints also showed repeated references to driver-assistance and braking behaviour:

| Theme mentioned in complaint text | November 2021 | February 2022 |
|---|---:|---:|
| Cruise control | 11 | 40 |
| Autopilot | 3 | 28 |
| Unexpected or abrupt braking | 5 | 26 |
| Phantom braking | 5 | 20 |
| Emergency braking | 1 | 12 |

This does not prove a defect or explain the cause. It does show that the rise in complaint volume contained recurring language around cruise control, Autopilot, and unexpected braking.

---

## Limitations

- NHTSA complaints are consumer reports, not confirmed engineering findings. I used the official NHTSA API to get the information for the data. Please note this can change over time. 
- Complaint volume is not adjusted for sales, registrations, miles driven, or reporting behaviour.
- The analysis covers only five selected vehicles.
- The first version uses only single-component complaints.
- Theme counts overlap because one complaint can mention more than one theme.

---

## Tools used

- Python
- Pandas
- Requests
- Scikit-learn
- Matplotlib
- Seaborn
- Google Colab

---

## Running the project

Open `NHTSA_Vehicle_Complaint_Intelligence.ipynb` in Google Colab or Jupyter Notebook and run the cells from top to bottom.

If needed, install the libraries first:

```python
!pip install pandas numpy requests scikit-learn matplotlib seaborn
