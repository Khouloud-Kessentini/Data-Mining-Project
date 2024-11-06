# Data Analysis and Clustering Techniques

**Author:** Khouloud Kessentini  

## Project Summary
This project involves studying a dataset using various data preprocessing, normalization, visualization, and clustering techniques to uncover patterns and insights.

## Project Structure

1. **Data Preprocessing**  
   Initial data cleaning and transformation steps to prepare the dataset.

2. **Data Normalization**  
   Standardizing data to ensure consistency across variables.

3. **Visualization**  
   Creating visualizations to explore data patterns and trends.

4. **Principal Component Analysis (PCA)**  
   Reducing dimensionality to focus on the primary components that capture most of the data’s variance.

5. **Interpretations**  
   Analyzing PCA results for insights into underlying data structures.

6. **Hierarchical Clustering**  
   Using hierarchical clustering to group similar data points and create a dendrogram.

7. **K-Means Algorithm**  
   Applying K-Means clustering to organize data into specific groups based on similarity.

   
```python
import pandas as pd
```

**Read data file**
```python
DATA = pd.read_csv('/content/drive/MyDrive/HR_SBA_Software.csv')
```
<img src="img/data.png" alt="Project Logo" width="1500" height="400"/>

# Data preprocessing

**Satisfaction level must be between 0 and 1** <br/>
We will proceed with a check to ensure all our data is within the [0, 1] range
```python
print("Valeur minimale",min(DATA.satisfaction_level))
print("Valeur maximale",max(DATA.satisfaction_level))
```

**The values for the variable "last_evaluation" must be between 0 and 1**<br/>
We will proceed with a check to ensure all our data is within the [0, 1] range

```python
print("Valeur minimale",min(DATA.last_evaluation))
print("Valeur maximale",max(DATA.last_evaluation))
```

**We must check that there are no negative values for the variable "number_project"**
```python
if (min(DATA.number_project>=0)):
  print ("Pas de valeurs négative! La première condition est vérifiée")
else:
  print("Il y'a des valeurs négatives! On doit corriger quelques observations")
```

**We must check that there are no excessive values for the number of projects**
```python
print("Nombre maximal de projets: ", max(DATA.number_project))
```

**On doit vérifier qu'il n'y'a pas de valeurs négatives pour le nombre de projets**
```python
print("Nombre maximal de projets: ", min(DATA.number_project))
```

**The minimum number must not be negative** <br/>
**It should also be a reasonable value (for example: 2 hours/month is not reasonable)**
```python
print(min(DATA.average_montly_hours))
```
**The reasonable maximum number per month is 275 (which is 10 hours maximum per day and 6 days/week)**

```python
print(max(DATA.average_montly_hours))
```
**We will eliminate observations greater than 250 hours**<br/>
**An observation with a number of hours greater than 250 is considered to be an excessively high value**

```python
indexNames = DATA[ DATA['average_montly_hours']>250].index
DATA.drop(indexNames, inplace=True)
```
<img src="img/preprocessing1.png" alt="Project Logo" width="1500" height="400"/>

**The minimum and maximum values are 2 and 10 years (reasonable values)**
```python
print ("temps minimal à l'entreprise", min(DATA.time_spend_company))
print ("temps maximal à l'entreprise", max(DATA.time_spend_company ))
```
**This value is binary, either 0 or 1<br/>
We will check that there are no other values (e.g., 2, 0.5, -1, etc.)<br/>
We calculate the occurrence of 0 and the occurrence of 1<br/>
and compare their sums against the total number of observations**
```python
occurence_0 = DATA.Work_accident[DATA['Work_accident']==0].count()
occurence_1 = DATA.Work_accident[DATA['Work_accident']==1].count()
len(DATA) == occurence_0 + occurence_1
```

**This value is binary, either 0 or 1<br/>
We will check that there are no values other than 0 and 1<br/>
We calculate the occurrence of 0 and the occurrence of 1 and compare their sums against the total number of observations**
```python
occurence_0 = DATA.left[DATA['left']==0].count()
occurence_1 = DATA.left[DATA['left']==1].count()
len(DATA) == occurence_0 + occurence_1
```

**The number of promotions must not be negative<br/>
It should also be a reasonable value (for example: 10 promotions in 5 years is not reasonable)**
```python
print ("Nombre minimal de promotions", min(DATA.promotion_last_5years))
print ("Nombre maximal de promotions", max(DATA.promotion_last_5years))
```
**It seems there is a mistake in the column name<br/>
It should be "Departement" instead of "sales"<br/>
We will change the column name from "sales" to "Departement"**
```python
DATA.columns=['satisfaction_level','last_evaluation','number_project','average_montly_hours','time_spend_company',
              'Work_accident','left','promotion_last_5years','Departement', 'salary']
```
**We will visualize the existing departments and check for any input errors**
```python
DATA['Departement'].unique()
```

**Elimination of duplicates**
```python
DATA.drop_duplicates(keep='first', inplace=True)
```
**We need numerical data to apply PCA**<br\> 
We will replace qualitative data with quantitative data<br\> 
sales = 1  -- accounting = 2 -- hr = 3 -- technical = 4 -- support = 5 management = 6 -- IT = 7 -- product_mng = 8 -- marketing = 9 -- RandD = 10<br\> 

**There are two methods to perform this task:**
**1. Replacing a single value, for example:**
```python
DATA['Departement'] = DATA['Departement'].replace(['sales'], 0.1)
DATA['Departement'] = DATA['Departement'].replace(['accounting'], 0.2)
DATA['Departement'] = DATA['Departement'].replace(['hr'], 0.3)
DATA['Departement'] = DATA['Departement'].replace(['technical'], 0.4)
```

**2. Replacing multiple values in parallel**
```
DATA['Departement'] = DATA['Departement'].replace(['support','management','IT'], [0.5,0.6,0.7])
DATA['Departement'] = DATA['Departement'].replace(['product_mng','marketing','RandD'], [0.8,0.9,1])
```

**Now let's verify that all departments have been modified**
```
DATA['Departement'].unique()
```
**Transform qualitative data into quantitative data**
```python
DATA['salary'] = DATA['salary'].replace(['low', 'medium', 'high'], [0.1, 0.2, 0.3])
```
**Verify the transformations**
```python
print(DATA['salary'].unique())
```
<img src="img/preprocessed_data.png" alt="Project Logo" width="1500" height="400"/>

on remarque ici que la corrélation entre nos données n'est pas très forte
pas de corrélation forte ni positive (tend vers 1) ni négative (tends vers -1)
```python
import matplotlib.pyplot as plt
import seaborn
plt.subplots_adjust(hspace=10, wspace=0.1,bottom=0.1)
matrice=DATA.corr()
seaborn.heatmap(matrice,annot=True)
plt.show()
```

<img src="img/correlation.png" alt="Project Logo" width="300" height="300"/>

**Import necessary libraries**
```python
from sklearn.decomposition import PCA
```
**Create an instance of the PCA class, here named "ACP"**
```python
ACP = PCA()
```
**Apply PCA transformation on dataset Z**
```python
Z_transformed = ACP.fit_transform(Z)
```
Z_transformed now contains the transformed data in terms of principal components

**Now we calculate the variance of each variable, i.e., the contribution of each variable to the total amount of information.**
```python
ACP.explained_variance_
```
**Now we calculate the variance in percentage for each variable**
```python
def explained_variance_en_pourcentage(ACP):
  for i in range (10):
    print(ACP.explained_variance_ratio_[i]*100)
explained_variance_en_pourcentage(ACP)
```
<img src="img/variance" alt="Project Logo" width="300" height="300"/>

```python
import numpy as np
import matplotlib.pyplot as plt
```

**Assuming ACP.explained_variance_ contains the explained variance for each principal component**
```python
val_proportion = ACP.explained_variance_
```
**Set the number of principal components**
```python
num_components = ACP.n_components_
```
**Plotting the explained variance as a line plot and bar plot**
```python
plt.plot(np.arange(1, num_components + 1), val_proportion, color="#FF0000", marker='o', label="Explained Variance (Line)")
plt.bar(np.arange(1, num_components + 1), val_proportion, width=0.6, color="#000000", alpha=0.7, label="Explained Variance (Bar)")
```

**Annotation of the figure**
```python
plt.title("Scree Plot of Eigenvalues")
plt.ylabel("Eigenvalue")
plt.xlabel("Principal Component Order")
plt.legend()
plt.show()
```
<img src="img/elbow.png" alt="Project Logo" width="450" height="450"/>

**Visualization of the cumulative explained variance curve**
**Since the variance of the variables is close, the cumulative explained variance curve is almost linear**
```python
plt.plot(np.arange(0, 10), np.cumsum(ACP.explained_variance_ratio_))
plt.title("Explained Variance")
plt.ylabel("Cumsum Explained Variance Ratio")
plt.xlabel("Factor Number")
plt.show()
```

The data might not be strongly correlated, but some deductions can still be made:

* Promotion in the last 5 years: It doesn't show a strong correlation with other variables. The highest correlation is with the salary variable at 0.08. This suggests that promotions are likely given arbitrarily without a well-defined formula.

* Number of projects and left: The covariance between the number of projects and the "left" variable is -0.25, indicating that as the number of projects increases, the likelihood of leaving the company decreases, and vice versa. However, since the correlation is just -0.25 and not close to -1, the relationship between these two variables is not very strong.

* Department and promotion in the last 5 years: The covariance between the department and promotion in the last 5 years is 0.028, indicating that the department is not a major factor in determining promotions. Additionally, the correlation between the department and other variables is very weak, suggesting that the department has little influence on the total amount of information.

* Number of projects and satisfaction level: The covariance between the number of projects and satisfaction level is 0.24. Although this is a weak correlation, it suggests that the more projects a person handles, the higher their satisfaction level (positive correlation).

* Satisfaction level and left: The covariance between satisfaction level and the "left" variable is -0.24. This negative correlation suggests that as the satisfaction level increases, the likelihood of leaving the company decreases (negative correlation).

* Salary and work accident: The correlation between salary and work accidents is 0.0004. This indicates that there is no significant correlation between these two variables, meaning there is no relationship between salary and work accidents.

**Selecting the top 4 variables that give the most information (based on the Kaiser criterion)**
```python
ACP = PCA(n_components=4) 
Z = ACP.fit_transform(Z)  # Perform PCA transformation to reduce dimensions
Z = pd.DataFrame(Z)  # Convert the result to a DataFrame
Z  # Display the transformed data
```
<img src="img/kaiser" alt="Project Logo" width="450" height="450"/>

```python
from sklearn.cluster import KMeans
```
**Create an instance of KMeans with 4 clusters (k=4) for the dataset Z**
```python
kmeans = KMeans(n_clusters=4).fit(Z)  
print(kmeans)  # Print the KMeans object, which includes details of the clustering
```

**Display the coordinates of each centroid and the associated inertia**
```python
centroids = kmeans.cluster_centers_  # Extract the coordinates of the centroids
print(centroids)  # Print the centroids' coordinates
```

**Note: Here, we have the coordinates of the centroids on 4 axes.**
**If we hadn't chosen just 4 principal components through PCA, we would need to work on 10 axes, which would be computationally expensive (high time complexity).**

