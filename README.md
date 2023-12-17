# AutoML_Project2

### Please, save data file as csv into the data folder ('data\Final_Africa_Master_File 2.csv'). It is not part of the git folder due to large size.

## Data preparation ([notebook](https://github.com/jtimko16/AutoML_Project2/blob/main/notebooks/1.Data_preparation.ipynb))
Contributors: Jan and Kea

* Included only the columns Radwa said we should include. "Take into consideration: Sow_Maize, Harvest_Maize, Sand (1-7), Clay (1-7), OC (1-7), PAW (1-7), pcp, tmax, tmin, spi, and Y_maize_major. We should predict next year crop yield."
* Dropped rows where the target Y_maize_major is NA.
* For 6 countries the Sow Maize and Harvest Maize were NA. Dropped those countries. 30 countries remained.
* Created a Farm column from latitude and longitude columns. So e.g. Farm value "Farm_110_Angola" means farm number 110 in Angola. Dropped latitude and longitude columns after that.
* From Sow Maize and Harvest Maize extracted the month, encoded it and dropped the original columns.
* Added time between sow and harvest.
* Calculated the mean of pcp, tmax, tmin, and spi columns.
* Created lagged dependent variable values and the mean pcp, tmax, tmin, and spi for the preceding 3 years.
* Dropped the pcp, tmax, tmin, and spi columns for the same year because we do not have this information at prediction time.
* Kept only latest 10 years of data, so 2007-2016.

In the final dataframe there are 32,359 rows, 30 countries, 3,887 Farms, 10 years of data.

## Baseline selection ([notebook](https://github.com/jtimko16/AutoML_Project2/blob/main/notebooks/2.Baseline_selection.ipynb))
Contributors: Kea and Jan

* Dropped Countries and Farm variables. We also tried one-hot-encoding them but it increased the runtime multiple times.
* Made a train-test split. The train split got years 2007-2015. The test split got the year 2016.
![image](https://github.com/jtimko16/AutoML_Project2/assets/55859977/ff4f1633-be5f-4d91-b711-1d30d19dc301)
* From the train split created 5 Time Series data splits.
![image](https://github.com/jtimko16/AutoML_Project2/assets/55859977/4edc7758-0c5b-4025-80da-e582f09bc33c)
* As regressors for baseline selection we took KNN, Random Forest, Adaboost, Linear Regression and LightGBM. First three cannot extrapolate, last two can.
* Used MinMaxScaler.
* Based on CV Linear Regression had the best performance in both RMSE and MAE. Mean CV RMSE is 0.3915. Mean CV MAE is 0.2391.
  
  ![image](https://github.com/jtimko16/AutoML_Project2/assets/55859977/1944f369-56a1-4cae-8af0-4a8c0b183782)

* Refitted Linear Regression on train and predicted on test. Performance on test set (year 2016) (baseline results): RMSE 0.3288, MAE 0.2161.

## AutoML Frameworks comparison

* We compare 5 AutoML frameworks against the baseline results. The frameworks are: TPOT, AutoGluon, PyCaret, AutoKeras, and X.
* The notebooks are here:
  * TPOT [notebook](https://github.com/jtimko16/AutoML_Project2/blob/main/notebooks/5.TPOT.ipynb) by Jan.
  * AutoGluon [notebook](https://github.com/jtimko16/AutoML_Project2/blob/main/notebooks/3.AutoGluon.ipynb) by Andri.
  * PyCaret [notebook](https://github.com/jtimko16/AutoML_Project2/blob/main/notebooks/4.PyCaret.ipynb) by Kea. Best model was tuned HuberRegressor(alpha=0.01, epsilon=1.1).
  * AutoKeras [notebook](https://github.com/jtimko16/AutoML_Project2/blob/main/notebooks/6.AutoKeras.ipynb) and H2O [notebook](https://github.com/jtimko16/AutoML_Project2/blob/main/notebooks/7.h2o_no_countries_and_farm.ipynb) notebooks by Valerija. In H2O two best models have been compared, since those are StackedEnsemble_BestOfFamily and StackedEnsemble_AllModels and the results of the cross-validation could have been misleading.

 
 Comparison of AutoML frameworks by performance on the test set (year 2016).

 Approach details | Baseline | TPOT | AutoGluon | PyCaret | AutoKeras | H2O
--- | --- | --- | --- |--- |--- |---
Data preprocessing | MinMax Scaler | None | MinMax Scaler | Robust Scaler | None | None 
Model | Linear Regression | PCA + ElasticNetCV | ExtraTreesMSE | Tuned Huber Regressor | NN* |  'StackedEnsemble_BestOfFamily'  
RMSE | 0.3288 | 0.2818 | 0.3069 | 0.3248 | 0.4081 |  0.334 
MAE  | 0.2161 | 0.1856 | 0.1901 | 0.1994 | 0.3361 | 0.2143

AutoKeras NN - The structure of the model that AutoKeras chose is the following:
* An input layer that takes in the features.
* A multi-category encoding layer which is likely handling categorical variables.
* A dense layer with 32 neurons followed by a ReLU activation.
* Another dense layer* with 16 neurons also followed by a ReLU activation.
* A regression head(a single dense neuron with a linear activation), which is the output layer for regression tasks

## Model interpretation

The best model in terms of performance on the test set, includes PCA which makes interpretation difficult. We can examine which features had most influence on each component and which components were most important as inputs to the model:

![image](https://github.com/jtimko16/AutoML_Project2/blob/main/plots/PCA_components_importance.png)

However, we use explainability tools on simple Random Forest model without PCA to get more insights to the important features, their relationship with the dependant variable and explanations for specific predictions.

.. insert plots here

