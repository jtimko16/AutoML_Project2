# AutoML_Project2

### Please, save data file into the data folder (data\Final_Africa_Master_File.numbers). It is not part of the git folder due to large size.

## Data preparation by Kea ([notebook](https://github.com/jtimko16/AutoML_Project2/blob/main/notebooks/Data_prep_Kea.ipynb))

* Included only the columns Radwa said we should include. "Take into consideration: Sow_Maize, Harvest_Maize, Sand (1-7), Clay (1-7), OC (1-7), PAW (1-7), pcp, tmax, tmin, spi, and Y_maize_major. We should predict next year crop yield."
* Dropped rows where the target Y_maize_major is NA.
* For 6 countries the Sow Maize and Harvest Maize were NA. Dropped those countries. 30 countries remained.
* Created a Farm column from latitude and longitude columns. So e.g. Farm value "Farm_110_Angola" means farm number 110 in Angola. Dropped latitude and longitude columns after that.
* From Sow Maize and Harvest Maize extracted the month, encoded it and dropped the original columns.
* Created lagged dependent variable values for the preceding 3 years.
* Kept only latest 10 years of data, so 2007-2016.

In the final dataframe there are 32,359 rows, 30 countries, 3,887 Farms, 10 years of data.

## Baseline selection by Kea ([notebook](https://github.com/jtimko16/AutoML_Project2/blob/main/notebooks/Baseline_Kea.ipynb))

* One-hot encoded Countries and Farm variables.
* Made a train-test split. The train split got years 2007-2015. The test split got the year 2016.
![image](https://github.com/jtimko16/AutoML_Project2/assets/55859977/ff4f1633-be5f-4d91-b711-1d30d19dc301)
* From the train split created 5 Time Series data splits.
![image](https://github.com/jtimko16/AutoML_Project2/assets/55859977/4edc7758-0c5b-4025-80da-e582f09bc33c)
* As regressors took KNN, Random Forest, Adaboost.
* Used MinMaxScaler.
* Cross-validation (CV) took about 50 minutes.
* Based on CV Random Forest had the best performance in both RMSE and MAE. Mean CV RMSE is 0.410461. Mean CV MAE is 0.244674.
![image](https://github.com/jtimko16/AutoML_Project2/assets/55859977/7863efb2-b72d-4e99-b7ab-51244b1079ff)
* Refitted Random Forest on train and predicted on test. Performance on test set (year 2016) (baseline results): RMSE 0.3496, MAE 0.2037.

An example of predictions:
![image](https://github.com/jtimko16/AutoML_Project2/assets/55859977/c7b9670d-7119-4f6e-baea-af6eb803141c)
