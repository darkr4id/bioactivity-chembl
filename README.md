# Bioactivity Classification of Compounds in ChEMBL Database

## Overview
This repository provides a Python-based workflow to classify bioactivity of chemical compounds retrieved from the ChEMBL database. The pipeline retrieves bioactivity data for a specified target protein, processes the data, and classifies compounds into three categories: **active, intermediate, and inactive**, based on their IC50 values.
This project is partially based on a tutorial by Data Professor on Youtube
## Features
- Retrieves bioactivity data from the **ChEMBL database** using the `chembl_webresource_client` package.
- Filters data based on **IC50 values** (in nanomolar units).
- Preprocesses data by removing missing values.
- Classifies compounds into **active, intermediate, or inactive**.
- Exports processed data to CSV files.

## Installation
Ensure you have the necessary Python packages installed:

```bash
pip install chembl_webresource_client pandas
```

## Usage
1. **Import required libraries:**

```python
import pandas as pd
from chembl_webresource_client.new_client import new_client
```

2. **Retrieve bioactivity data for a specific target protein:**

```python
target = new_client.target
target_query = target.search('aromatase')
targets = pd.DataFrame.from_dict(target_query)

# Select target protein (e.g., SARS coronavirus 3C-like proteinase)
selected_target = targets.target_chembl_id[4]

# Retrieve activity data
activity = new_client.activity
res = activity.filter(target_chembl_id=selected_target).filter(standard_type="IC50")
df = pd.DataFrame.from_dict(res)
```

3. **Filter missing values:**

```python
df2 = df[df.standard_value.notna()]
```

4. **Classify compounds based on IC50 values:**

```python
bioactivity_class = []
for i in df2.standard_value:
    if float(i) >= 10000:
        bioactivity_class.append("inactive")
    elif float(i) <= 1000:
        bioactivity_class.append("active")
    else:
        bioactivity_class.append("intermediate")
```

5. **Create a new DataFrame with classification:**

```python
data_tuples = list(zip(df2.molecule_chembl_id, df2.canonical_smiles, bioactivity_class, df2.standard_value))
df3 = pd.DataFrame(data_tuples, columns=['molecule_chembl_id', 'canonical_smiles', 'bioactivity_class', 'standard_value'])
```

6. **Save preprocessed data to CSV:**

```python
df3.to_csv('bioactivity_preprocessed_data.csv', index=False)
```

7. **Copy the file to Google Drive (if using Google Colab):**

```python
from google.colab import drive
drive.mount('/content/gdrive/', force_remount=True)
! cp bioactivity_preprocessed_data.csv "/content/gdrive/My Drive/Colab Notebooks/data"
```

## Data Format
The final processed dataset consists of the following columns:

| molecule_chembl_id | canonical_smiles | bioactivity_class | standard_value |
|--------------------|------------------|-------------------|---------------|
| CHEMBL187579      | Cc1noc(C)c1CN1... | intermediate      | 7200.0        |
| CHEMBL188487      | O=C1C(=O)N(Cc2... | intermediate      | 9400.0        |
| CHEMBL185698      | O=C1C(=O)N(CC2... | inactive          | 13500.0       |
| CHEMBL426082      | O=C1C(=O)N(Cc2... | inactive          | 13110.0       |

## Contribution
Feel free to contribute by adding new functionalities or improving the classification methodology.

## License
This project is open-source and available under the **MIT License**.

## Author
[Your Name]

