# Grupo Bimbo Inventory Demand Prediction

## Overview
This is a real world business problem faced by almost all companies. We always want to know the expcted Demand of a product so that we can match the Sales/Supply accordingly. Mostly, this work is done by marketing/sales managers who manually calculate the expected demand and try to meet that. But this is a lot of work to be done for each product. And we apply rules of aggregations to all products, we might be ignoring some key factors. The machine learning solutions aims to count in most of the factors for identifying the inherent relation to demand for a product. That is what I hoped to achieve here.

## DataSet
The Dataset was from a Kaggle competition, organised by Grupo Bimbo Bakery company for predicting demand for each product/client combination for 1 week based on 7 weeks of available data. The number of products were around 1000 and the client number was 9,00,000. The dataset consisted of client and product details, the sales data and Town data. The sales data itself was 78 million rows of sales over 7 weeks(3.5 GB).

## Problem Breakdown
My System Specification was not enough to handle this data. So I broke the process into two parts. First part was where I used a significantly smaller data (1,000,000 rows) for exploratory analysis and feature extraction, model optimisation and comparing models.     
In the second part, I used same features as in (1), and the same model as finalized from (1). Here I used 16 million rows of data. 

## Part 1
I used PySpark framework to extract the rows from dataset. Here I used smaller datatypes for columns to save space. It required a bit of cleaing where duplicate values were removed, Long names were shortened, and Nan data was removed.        
The base sales data was used to for model training to set a benchmark. It came as 0.38(RMSLE) for LGBM. 
Now I tried a bunch of different feature combinations which could give the best performance. The features that were extracted were thought of based on sales intuition and structure of the sales data. I created a total of 20 new features initially and finally chose 11 of them.     
The selection was based on three themes. The highly correlated features were merged together. Secondly, the feature importance property of LGBM was used to find the best predictors. Thirdly, the improvement in performance by adding a new feature was observed.     
Finally using the best features and running GridSearch on them gave a RMSLE of 0.157.     

## Part 2
Here, again I used Pyspark to extract the data and reduce the size by managing data types. The data was divided into four smaller sets - PreTraining Set, Training Set, Cross Val set and Test Set. Pretraining set was (week 1-4), TrainingSet was (week 5-6), Cross Val set was week 7, and Test set was week 8. The features were created using the pretraining set and added to training set. The training and Cross Val were used to create and tune the model. Once the model was ready, it was retrained with the final training data(including Cross Val). We retaining the same tuning parameters.     
Finally, the model was fit on the test data to give the final results. This gave a RMSLE around 0.31.

## Conclusion/Future Work
The main challenge in this project was the size of data. I went through a lot of literature to find ways to handle large datasets. But that did not work on my 8GB Memory Laptop. Using Spark SQL, Dask Dataframes, Pandas Memorymap were few such techniques.Finally found a lengthly but simple solution using Pyspark and chunks(in read_csv). Using a random smaller dataset meant that many of the Product/Client sales would be missing in training sets and cross val on them would give big errors. I tried to minimize this using aggregate features so that a general behaviour around a Product/of a Client could be captured and new Products/Client demands can also be estimated. This did not give performance as good as smaller set(0.31 vs 0.157).     
The next steps should be to use a cloud computing system to run the model on all of training data to see if there is improvement in performance. Also, I could try using some better features which capture deeper interations between the base features. I saw this in top solutions of kagglers where they used Vowpal Wabbit/Linear regression on previous week and used the predicted outputs as a training set feature. Another idea should be to create an end to end web service around it so that a Sales guy can easily study the Predicted demands by Product,Region or Client. 

Reference Links ->    
https://www.kaggle.com/c/grupo-bimbo-inventory-demand/data   
https://towardsdatascience.com/how-to-handle-large-datasets-in-python-with-pandas-and-dask-34f43a897d55   
https://towardsdatascience.com/build-xgboost-lightgbm-models-on-large-datasets-what-are-the-possible-solutions-bf882da2c27d  
https://towardsdatascience.com/how-to-learn-from-bigdata-files-on-low-memory-incremental-learning-d377282d38ff#d167    
https://towardsdatascience.com/inventory-management-using-python-17cb7ddf9314


