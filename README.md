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
**Satisfaction level must be between 0 and 1**
We will proceed with a check to ensure all our data is within the [0, 1] range


<img src="img/data.png" alt="Project Logo" width="2000" height="444"/>
