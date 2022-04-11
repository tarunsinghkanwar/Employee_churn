# Employee_churn
![download](https://user-images.githubusercontent.com/37711056/162683746-ad5ea5ae-ec67-44ba-9ec7-4154cb1bb652.jpg)


# App link
https://employee-churn2022.herokuapp.com/


# EDA

https://github.com/tarunsinghkanwar/Employee_churn/blob/main/churn%20analysis.ipynb
 
# Aim
Build a predictive models to find out the Employee retention.

# Architecture

![image](https://user-images.githubusercontent.com/37711056/162637304-0cef4219-e411-4682-88af-8f57c5caf27e.png)

# Data Description
![image](https://user-images.githubusercontent.com/37711056/162637134-8d4abc08-d061-4e9d-bb5a-2b4c98b0b6e8.png)

Column names:
EMPID
SATISFACTION_LEVEL
LAST_EVALUATION
NUMBER_PROJECT
AVERAGE_MONTHLY_HOURS
TIME SEPEND_COMPANY
WORK_ACCIDENT
PROMOTION_LAST5YEARS
SALARY

we also require a "schema" file from the client, which contains all the relevant information about the training files such as:
Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns, and their datatype.


# Data Validation 
In this step, we perform different sets of validation on the given set of training files.  
⦁	 Name Validation- We validate the name of the files based on the given name in the schema_train file. We have created a regex pattern as per the name given in the schema file to use for validation. After validating the pattern in the name, we check for the length of date in the file name as well as the length of time in the file name. If all the values are as per requirement, we move such files to "training_data_processed" else we move such files to "training_data_rejects."

⦁	 Number of Columns - We validate the number of columns present in the files, and if it doesn't match with the value given in the schema file, then the file is moved to "training_data_rejects."


⦁	 Name of Columns - The name of the columns is validated and should be the same as given in the schema file. If not, then the file is moved to "training_data_rejects".

⦁	 The datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If the datatype is wrong, then the file is moved to "Bad_Data_Folder".


⦁	Null values in columns - If any of the columns in a file have all the values as NULL or missing, we discard such a file and move it to "training_data_rejects".



# Data Insertion in Database
 
1) Database Creation and connection - Create a database with the given name passed. If the database is already created, open the connection to the database. 
2) Table creation in the database - Table with name - "training_raw_data_t", is created in the database for inserting the files in the "Good_Data_Folder" based on given column names and datatype in the schema file. If the table is already present, then the new table is not created and new files are inserted in the already present table as we want training to be done on new as well as old training files.     
3) Insertion of files in the table - All the files in the "training_data_proccessd" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "training_data_rejects".
 
# Model Training 
1) Data Export from Db - The data in a stored database is exported as a CSV file to be used for model training.
2) Data Preprocessing  
   a) Check for null values in the columns. If present, impute the null values using the KNN imputer
   b) transform the features using log transformation
   c) Scale the training and test data separately 
3) Clustering - KMeans algorithm is used to create clusters in the preprocessed data. The optimum number of clusters is selected by plotting the elbow plot, and for the dynamic selection of the number of clusters, we are using "KneeLocator" function. The idea behind clustering is to implement different algorithms
   To train data in different clusters. The Kmeans model is trained over preprocessed data and the model is saved for further use in prediction.
4) Model Selection - After clusters are created, we find the best model for each cluster. We are using two algorithms, "Random forest Regressor" and “Linear Regression”. For each cluster, both the algorithms are passed with the best parameters derived from GridSearch. We calculate the Rsquared scores for both models and select the model with the best score. Similarly, the model is selected for each cluster. All the models for every cluster are saved for use in prediction. 
 

 # Prediction Data Description
 
 ![image](https://user-images.githubusercontent.com/37711056/162683194-05ba918f-ab89-44ca-9285-e396dd37c6a0.png)
![image](https://user-images.githubusercontent.com/37711056/162683243-acc248ae-e07a-4b3c-9025-83d7a4b34836.png)



 # Data Validation  
 
In this step, we perform different sets of validation on the given set of Test files.  
1) Name Validation- We validate the name of the files on the basis of given Name in the schema_predict file. We have created a regex pattern as per the name given in schema file, to use for validation. After validating the pattern in the name, we check for length of date in the file name as well as length of time in the file name. If all the values are as per requirement, we move such files to "prediction_data_processed" else we move such files to "prediction_data_rejects". 
2) Number of Columns - We validate the number of columns present in the files, if it doesn't match with the value given in the schema file then the file is moved to "prediction_data_rejects". 
3) Name of Columns - The name of the columns is validated and should be same as given in the schema file. If not, then the file is moved to "prediction_data_rejects". 
4) Datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If dataype is wrong then the file is moved to "prediction_data_rejects". 
5) Null values in columns - If any of the columns in a file has all the values as NULL or missing, we discard such file and move it to "prediction_data_rejects".  
Data Insertion in Database 
1) Database Creation and connection - Create database with the given name passed. If the database is already created, open the connection to the database. 
2) Table creation in the database - Table with name - "prediction_data_processed", is created in the database for inserting the files in the "prediction_data_processed" on the basis of given column names and datatype in the schema file. If table is already present then new table is not created, and new files are inserted the already present table as we want training to be done on new as well old training files.     
3) Insertion of files in the table - All the files in the "prediction_data_processed" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "prediction_data_rejects".


# Prediction 
 
1) Data Export from Db - The data in the stored database is exported as a CSV file to be used for prediction.
2) Data Preprocessing   
   a) Check for null values in the columns. If present, impute the null values using the KNN imputer
   b) transform the features using log transformation
   c) Scale the training and test data separately 
3) Clustering - KMeans model created during training is loaded, and clusters for the preprocessed prediction data is predicted.
4) Prediction - Based on the cluster number, the respective model is loaded and is used to predict the data for that cluster.
5) Once the prediction is made for all the clusters, the predictions along with the original names before label encoder are saved in a CSV file at a given location and the location is returned to the client.




# How to use?

Clone the repository Install the required packages in "requirements.txt" file. Some packages are:

numpy pandas scikit-learn

Run the "main.py" file And you are good to go.



