# Project: Identify Customer Segments

In this project, I apply unsupervised learning techniques to identify segments of the population that form the core customer base for a mail-order sales company in Germany. These segments can then be used to direct marketing campaigns towards audiences that will have the highest expected rate of returns. The data used has been provided by partners at Bertelsmann Arvato Analytics, and represents a real-life data science task.

In this notebook a framework within which I perform the analysis steps. With **Discussion** document the decisions that I made in the approach.

## About this data
There are four files associated with this project:

- `Udacity_AZDIAS_Subset.csv`: Demographics data for the general population of Germany; 891211 persons (rows) x 85 features (columns).
- `Udacity_CUSTOMERS_Subset.csv`: Demographics data for customers of a mail-order company; 191652 persons (rows) x 85 features (columns).
- `Data_Dictionary.md`: Detailed information file about the features in the provided datasets.
- `AZDIAS_Feature_Summary.csv`: Summary of feature attributes for demographics data; 85 features (rows) x 4 columns

Each row of the demographics files represents a single person, but also includes information outside of individuals, including information about their household, building, and neighborhood. This information will be used to cluster the general population into groups with similar demographic properties. Then, you will see how the people in the customers dataset fit into those created clusters. The hope here is that certain clusters are over-represented in the customers data, as compared to the general population; those over-represented clusters will be assumed to be part of the core userbase. This information can then be used for further applications, such as targeting for a marketing campaign.

## Steps

### Step 0: Load the datasets

In this step, the data files are loaded and simple EDA is performed.

### Step 1: Preprocessing
- Assessed missing data
- Convert missing values codes to NAN
- Assess missing data in each column
### Assess Missing Data in Each Column

> As observed in the upper figure, a pattern was noticed regarding some columns would have the equal percentages of missing values.

### Assess Missing Data in Each Row

> Almost **half of the data points have 60% missing values** in a row, with a **maximum of 49 missing values out of 79 columns**.  

> On average, **18% of the values are missing per row**.  

> The dataset was divided into two subsets: **rows with fewer missing values** and **rows with many missing values**.  

- Rows with **fewer missing values** showed **normal or bimodal distributions**, indicating a well-represented dataset.  
- Rows with **many missing values** had **dominant frequencies for specific values**, suggesting systematic data loss rather than random missingness.  
- The distributions of non-missing features differ significantly between the two subsets, indicating that the missingness is **not completely random**.  
- Simply dropping rows with many missing values could lead to **bias**, so they should be noted for further investigation.  
- The analysis will continue using the **subset with fewer missing values**, while keeping track of the excluded rows for potential reassessment.


#### Re-Encode Categorical Features

> **Multi-Level Categorical Features**  
- **`CAMEO_DEUG_2015`**: Converted to `int`.  

> **Ordinal Encoding**  
- **`CAMEO_DEU_2015_life_stage`**: Mapped `A–F` to `1–6`.  

#### Engineer Mixed-Type Features

- **`CAMEO_INTL_2015`**:  
  - Split into `CAMEO_INTL_2015_wealth` (first digit) and `CAMEO_INTL_2015_life_stage` (second digit).  
  - Dropped original column.  
- **`CAMEO_DEU_2015`**:  
  - Split into `CAMEO_DEU_2015_wealth` (first digit) and `CAMEO_DEU_2015_life_stage` (second character).  
  - Dropped original column.  
- **`PRAEGENDE_JUGENDJAHRE`**:
    - used dictionary data to enginner features.
- **`'LP_LEBENSPHASE_FEIN', 'LP_LEBENSPHASE_GROB', 'WOHNLAGE', 'PLZ8_BAUMAX'`**:
    - these mixed are dropped due to time limitiation and the need to perform advanced data wrangling including merge.


### Apply Feature Scaling

> The standard scaler is applied to the dataset to perform feature scaling. So that to ensure the min and max values could be extreme and the standrad scaler is better in this case.

### Perform Dimensionality Reduction

> The number of componnets where chosen based on the variave with the threshold of .95 and the # of compments is 50.
> Also the graph was observed using the elbow method. It shows that the variance changes slowly between 20 - 30


### Interpret Principal Components

- **Principal Component 2 (PC2)**:
  - `MOBI_REGIO`, `LP_STATUS_FEIN`, `KBA05_ANTG1`, and `FINANZ_MINIMALIST` have strong positive weights. related to mobility, financial status, and minimalistic tendencies. Positive values suggest high scores on variables such as mobility and financial minimalism
  - `SEMIO_KULT`, `FINANZ_ANLEGER`, and `PLZ8_ANTG3` have negative weights, these features contrasts with the positive features in PC2. negative values suggest lower scores on socio-cultural and financial investor characteristics.

- **Principal Component 3 (PC3)**:
  - there is Strong positive weights for `ANREDE_KZ`, `SEMIO_KAEM`, and `SHOPPER_TYP`. They are demographic data and consumer preferences. Positive values reflect stronger associations with consumer types and personal demographics.
  - `FINANZ_VORSORGER`, `SEMIO_FAM`, and `FINANZTYP` are negatively weighted, indicating contrasting preferences for family-related or financial-oriented behaviors. negative values point to opposing characteristics related to financial preparation and family orientation.

- **General Observations**:
  - Positive and negative weights are meaningful. Positive values show features that contribute to the principal component, while negative values indicate contrasting influences.

  ### Apply Clustering to General Population

The k means were applied in the number of clusters between 20 and 21 and the follwing was noticed:

> more clusters are used, the score improves.

>  a clear elbow is immediately obvious. The decrease seems relatively consistent throughout the range.  the elbow is not very sharp, but it's likely around K=3 or K=4.

> Chosen number was 4 based on the graph elbow method

### Compare Customer Data to Demographics Data

**Features Positively Correlated with Response Variable:**

- Segments such as SEMIO_ERL, SEMIO_KRIT, and LP_STATUS_FEIN show higher centroid values, indicating a strong alignment with the target audience.
- Financial behaviors like FINANZ_MINIMALIST and FINANZ_ANLEGER exhibit a significant positive correlation, suggesting these groups are more likely to engage with the response variable.

**Features Negatively Correlated with Response Variable:**

- Lower centroid values in SEMIO_REL, SEMIO_FAM, and SEMIO_KULT imply these features are less relevant to the target group.
- Financially conservative behaviors (FINANZ_VORSORGER, FINANZTYP) and more traditional lifestyles (SEMIO_TRADV) are less strongly associated with the response variable, indicating a weaker connection.