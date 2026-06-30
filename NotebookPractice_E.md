# Notebook Practice: Pandas, NumPy, and Plotly Basics

This document is a GitHub-friendly Markdown version of `NotebookPractice.ipynb`.  
It covers basic practice with loading CSV data using Pandas, calculating sampling/statistics with NumPy, and creating simple visualizations with Plotly.

---


---

## Libraries Used

This notebook uses the following Python libraries:

- `numpy`: Array operations, random number generation, mean, variance, and standard deviation
- `pandas`: Loading CSV files and analyzing DataFrames
- `plotly`: Creating interactive graphs
- `requests`: Downloading CSV files from the web
- `scipy.stats`: Using statistical functions such as mode

### Checking the NumPy Version

```python
import numpy as np
print(np.__version__)
```

Example output:

```text
2.1.3
```

### Installing Required Packages

```python
!pip install numpy
!pip install pandas
!pip install plotly
```

---

## Loading the Possum Dataset

The `possum.csv` file is loaded directly from the web and stored as a Pandas DataFrame.

```python
import pandas as pd
import plotly.express as px
import plotly.io as pio
import requests
from io import StringIO

pio.renderers.default = "iframe"

url = "https://www.openintro.org/data/csv/possum.csv"
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 "
                  "(KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3"
}

response = requests.get(url, headers=headers)
response.raise_for_status()

df = pd.read_csv(StringIO(response.text))
```

This code downloads the CSV file from the web and loads it into the Jupyter Notebook environment when the file is not available locally.

---

## Basic Exploration of the Possum Dataset

### Checking Dataset Information

```python
df.info()
```

Example output:

```text
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 104 entries, 0 to 103
Data columns (total 8 columns):
 #   Column   Non-Null Count  Dtype  
---  ------   --------------  -----  
 0   site     104 non-null    int64  
 1   pop      104 non-null    object 
 2   sex      104 non-null    object 
 3   age      102 non-null    float64
 4   head_l   104 non-null    float64
 5   skull_w  104 non-null    float64
 6   total_l  104 non-null    float64
 7   tail_l   104 non-null    float64
```

### Checking the DataFrame Type

```python
type(df)
```

Example output:

```text
pandas.core.frame.DataFrame
```

### Displaying the First Few Rows

The original notebook contains the following code:

```python
df.head
```

However, to display the first five rows, parentheses should be added:

```python
df.head()
```

### Checking Basic Summary Statistics

```python
df.describe()
```

Example summary:

| Column | Count | Mean | Std | Min | 50% | Max |
|---|---:|---:|---:|---:|---:|---:|
| site | 104 | 3.63 | 2.35 | 1.00 | 3.00 | 7.00 |
| age | 102 | 3.83 | 1.91 | 1.00 | 3.00 | 9.00 |
| head_l | 104 | 92.60 | 3.57 | 82.50 | 92.80 | 103.10 |
| skull_w | 104 | 56.88 | 3.11 | 50.00 | 56.35 | 68.60 |
| total_l | 104 | 87.09 | 4.31 | 75.00 | 88.00 | 96.50 |
| tail_l | 104 | 37.01 | 1.96 | 32.00 | 37.00 | 43.00 |

---

## Visualizing the Possum Dataset

This scatter plot shows the relationship between `tail_l` and `head_l`.

```python
fig = px.scatter(
    df,
    x="tail_l",
    y="head_l",
    labels={
        "head_l": "Head Length",
        "tail_l": "Tail Length"
    },
    title="Possum Tail Length vs Head Length"
)

fig.show()
```

This graph is useful for checking whether there is a visible relationship between tail length and head length.

---

## Loading the Loan50 Dataset

The `loan50.csv` file is loaded from the local directory.

```python
df1 = pd.read_csv("loan50.csv")
df1.head()
```

> **Note:** To run this code successfully, `loan50.csv` must be in the same folder as the notebook.

---

## Basic Exploration of the Loan50 Dataset

### Checking Basic Summary Statistics

```python
df1.describe()
```

Example summary:

| Column | Count | Mean | Std | Min | 50% | Max |
|---|---:|---:|---:|---:|---:|---:|
| emp_length | 48 | 4.90 | 3.52 | 0.00 | 5.00 | 10.00 |
| term | 50 | 42.72 | 10.89 | 36.00 | 36.00 | 60.00 |
| annual_income | 50 | 86,170.00 | 57,566.50 | 28,800.00 | 74,000.00 | 254,000.00 |
| debt_to_income | 50 | 0.72 | 0.96 | 0.05 | 0.54 | 6.46 |

### Counting Non-Null Values by Column

```python
df1.count()
```

Partial example output:

```text
state                      50
emp_length                 48
term                       50
homeownership              50
annual_income              50
verified_income            50
debt_to_income             50
total_credit_limit         50
total_credit_utilized      50
```

Since `emp_length` has only 48 non-null values, we can tell that this column has 2 missing values.

### Checking Dataset Information

```python
df1.info()
```

Example output:

```text
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 50 entries, 0 to 49
Data columns (total 18 columns):
 #   Column                   Non-Null Count  Dtype  
---  ------                   --------------  -----  
 0   state                    50 non-null     object 
 1   emp_length               48 non-null     float64
 2   term                     50 non-null     int64  
 3   homeownership            50 non-null     object 
 4   annual_income            50 non-null     int64  
```

---

## Visualizing the Loan50 Dataset

This scatter plot shows the relationship between `total_credit_limit` and `total_income`.

```python
fig1 = px.scatter(
    df1,
    x="total_credit_limit",
    y="total_income",
    labels={
        "total_income": "Total Income",
        "total_credit_limit": "Total Credit Limit"
    },
    title="Total Credit Limit vs Total Income"
)

fig1.show()
```

> **Note:** If the `total_income` column does not exist in `loan50.csv`, this code will raise an error.  
> Check the dataset column names first, then replace the column name with one that actually exists.

Example:

```python
print(df1.columns)
```

---

## Dotplot Practice

This section visualizes `total_credit_limit` as a dotplot and marks the mean value on the graph.

```python
import plotly.express as px

mean_rate = df1["total_credit_limit"].mean()

fig = px.strip(
    df1,
    x="total_credit_limit",
    title="Total Credit Limit Dotplot",
    template="plotly_white"
)

fig.update_traces(
    jitter=0.7,
    marker_size=12,
    opacity=0.8,
    marker_line_width=1,
    marker_line_color="black"
)

fig.add_annotation(
    x=mean_rate,
    y=0.8,
    yref="paper",
    text=f"Mean = {mean_rate:.2f}",
    showarrow=True,
    arrowhead=2,
    arrowsize=1.2,
    arrowwidth=2,
    arrowcolor="red",
    ax=0,
    ay=-40,
    font=dict(size=16, color="red")
)

fig.update_layout(
    title=dict(
        x=0.5,
        font=dict(size=24)
    ),
    xaxis_title="Total Credit Limit",
    yaxis_title="",
    font=dict(size=16),
    showlegend=False,
    width=900,
    height=350,
    margin=dict(l=40, r=40, t=90, b=40)
)

fig.show()
```

This graph helps visually identify the distribution of each borrower's credit limit and the location of the average value.

---

## Comparing Population and Sample Means

This section creates a population of 100,000 integers, then draws samples of different sizes to compare sample means with the population mean.

```python
import numpy as np

# 1. Create a population: 100,000 integers between 0 and 200
rng = np.random.default_rng(seed=42)
population = rng.integers(low=0, high=201, size=100000)
pop_mean = np.mean(population)

print(f"Population mean: {pop_mean:.4f}\n")

# 2. Calculate and compare means for different sample sizes
sample_sizes = [35000, 25000, 15000]

for size in sample_sizes:
    sample = rng.choice(population, size=size, replace=True)
    sample_mean = np.mean(sample)
    diff = sample_mean - pop_mean
    
    print(f"Sample size {size}:")
    print(f"  - Sample mean: {sample_mean:.4f}")
    print(f"  - Difference in means: {diff:.4f}\n")
```

The key idea of this practice is to observe that when the sample size is large enough, the sample mean does not differ greatly from the population mean.

---

## Basic Statistics Comparison: Uniform vs Normal Distribution

This section generates data from a uniform distribution and a normal distribution, then compares their mean, median, mode, variance, and standard deviation.

```python
import numpy as np
from scipy import stats

# 1. Generate data
rng = np.random.default_rng(seed=42)

# Uniform distribution: integers between 1 and 1000
uniform_samples = rng.integers(low=1, high=1001, size=100000)

# Normal distribution: mean = 500, standard deviation = 100
normal_samples = rng.normal(loc=500, scale=100, size=100000)


def print_stats(data, name):
    print(f"--- {name} ---")
    print(f"Mean: {np.mean(data):.2f}")
    print(f"Median: {np.median(data):.2f}")
    print(f"Mode: {stats.mode(data, keepdims=True).mode[0]:.2f}")
    print(f"Variance: {np.var(data):.2f}")
    print(f"Standard Deviation: {np.std(data):.2f}\n")


# 2. Print results
print_stats(uniform_samples, "Uniform Distribution")
print_stats(normal_samples, "Normal Distribution")
```

Example output:

```text
--- Uniform Distribution ---
Mean: 500.36
Median: 501.00
Mode: 349.00
Variance: 83306.20
Standard Deviation: 288.63

--- Normal Distribution ---
Mean: 499.49
Median: 498.98
Mode: 18.44
Variance: 10098.06
Standard Deviation: 100.49
```

### Interpretation

- **Mean**: The average value
- **Median**: The middle value
- **Mode**: The most frequently occurring value
- **Variance**: A measure of how spread out the data is from the mean
- **Standard Deviation**: The square root of the variance; it shows the spread of the data in a more intuitive way

Because values in a uniform distribution are spread evenly across a fixed range, the variance and standard deviation are relatively large.  
Because values in a normal distribution are concentrated around the mean of 500, the standard deviation is close to the configured value of 100.

---

## Important Notes for Running the Notebook

### 1. `loan50.csv` Is Required

The following code loads a local file, so `loan50.csv` must be in the same directory as the notebook.

```python
df1 = pd.read_csv("loan50.csv")
```

If the file is missing, the following error may occur:

```text
FileNotFoundError: [Errno 2] No such file or directory: 'loan50.csv'
```

### 2. Check Column Names Before Plotting

It is a good idea to check whether the columns used for visualization actually exist in the dataset.

```python
print(df1.columns)
```

### 3. Use `df.head()` Instead of `df.head`

```python
# Incorrect example
df.head

# Correct example
df.head()
```

`df.head` displays the method object itself, while `df.head()` actually prints the first five rows.

---

## Summary

This notebook is a beginner-level practice file for the following topics:

- Loading CSV data with Pandas
- Checking the structure and data types of a DataFrame
- Identifying missing values and counting values by column
- Using `describe()` to check basic summary statistics
- Creating scatter plots and dotplots with Plotly
- Comparing population and sample means with NumPy
- Comparing basic statistics of uniform and normal distributions

When uploading this project to GitHub, it is recommended to include this Markdown file, the original `.ipynb` notebook, and any required CSV files in the same repository.
