This script makes use of the WLE data set which are credited to:
Velloso, E.; Bulling, A.; Gellersen, H.; Ugulino, W.; Fuks, H. 
Qualitative Activity Recognition of Weight Lifting Exercises. 
Proceedings of 4th International Conference in Cooperation with SIGCHI 
(Augmented Human '13) . Stuttgart, Germany: ACM SIGCHI, 2013.
Read more: http://groupware.les.inf.puc-rio.br/har#ixzz3rnrcgMnw


Set seed as instructed in project instructions
````
set.seed(1414)
````
Load the training set
````
training <- read.csv("https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv")
````
Load the testing set
````
testing <- read.csv("https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv")
````

Partition the training set into two to allow cross validation
````
inTrain <- createDataPartition(training$classe, p=0.7, list=FALSE)
training1 <- training[inTrain,]
training2 <- training[-inTrain,]
````

Choose predictors
````
summary(training)
````

Using above, identify the variables which are not good for prediction. These results in:

* Qualitative variables not good_for prediction: X, user_name, timestamps, new_window, num_window
* Mostly empty variables : kurtosis_roll_belt, kurtosis_picth_belt, kurtosis_yaw_belt, skewness_roll_belt*,
skewness_yaw_belt, max_yaw_belt, min_yaw_belt, amplitude_yaw_belt,
kurtosis_roll_arm, kurtosis_picth_arm, kurtosis_yaw_arm, skewness_roll_arm, 
skewness_pitch_arm, skewness_yaw_arm, kurtosis_roll_dumbell, kurtosis_picth_dumbell,
kurtosis_yaw_dumbell, skewness_roll_dumbell, skewness_pitch_dumbell, skewness_yaw_dumbell,
max_yaw_dumbbell,min_yaw_dumbbell, amplitude_yaw_dumbbell
* Variables with values mostly NAs: amplitude_roll_belt, amplitude_pitch_belt, var_total_accel_belt, avg_roll_belt
stddev_roll_belt, var_roll_belt, avg_pitch_belt, stddev_pitch_belt,
var_pitch_belt, avg_yaw_belt, stddev_yaw_belt, var_yaw_belt, var_accel_arm,
avg_roll_arm, stddev_roll_arm, var_roll_arm, avg_pitch_arm, stddev_pitch_arm,
var_pitch_arm, avg_yaw_arm, stddev_yaw_arm, var_yaw_arm, max_roll_arm, max_picth_arm,
max_yaw_arm, min_roll_arm, min_pitch_arm, min_yaw_arm, amplitude_roll_arm,
amplitude_pitch_arm, amplitude_yaw_arm, max_roll_dumbell, max_picth_dumbbell,
min_roll_dumbbell, min_pitch_dumbbell, amplitude_roll_dumbbell,
amplitude_pitch_dumbbell, var_accel_dumbbell, avg_roll_dumbbell, stddev_roll_dumbbell,
var_roll_dumbbell, avg_pitch_dumbbell, stddev_pitch_dumbbell, var_pitch_dumbbell,
avg_yaw_dumbbell, stddev_yaw_dumbbell, var_yaw_dumbbell, 

The variables from above will be removed from the data set leaving classe + predictors
````
predictors <- c("classe", "roll_belt","pitch_belt", "yaw_belt","total_accel_belt", "gyros_belt_x", "gyros_belt_y", "gyros_belt_z", "accel_belt_x",
                "accel_belt_y", "accel_belt_z", "magnet_belt_x", "magnet_belt_y", "magnet_belt_z", "roll_arm",
                "pitch_arm", "yaw_arm", "total_accel_arm", "gyros_arm_x", "gyros_arm_y", "gyros_arm_z",
                "accel_arm_x", "accel_arm_y", "accel_arm_z", "magnet_arm_x", "magnet_arm_y", "magnet_arm_z",
                "roll_dumbbell", "pitch_dumbbell","yaw_dumbbell","gyros_dumbbell_x","gyros_dumbbell_y", "gyros_dumbbell_z",
                "accel_dumbbell_x", "accel_dumbbell_y", "accel_dumbbell_z", "magnet_dumbbell_x", "magnet_dumbbell_y", 
                "magnet_dumbbell_z",
                "roll_forearm", "pitch_forearm","yaw_forearm","total_accel_forearm","total_accel_dumbbell",
                "gyros_forearm_x","gyros_forearm_y","gyros_forearm_z","accel_forearm_x","accel_forearm_y","accel_forearm_z",
                "magnet_forearm_x","magnet_forearm_y","magnet_forearm_z")
````
Remove variables not to be used in prediction
````
training1 <- training1[,predictors]
training2 <- training2[,predictors]
````

Do model training
````
modFit <- randomForest(factor(classe)~., data=training1, importance=TRUE)

Predict using training2 set
modPredict <- predict(modFit, newdata=training2)
confusionMatrix(modPredict, training2$classe)
````
Predict test cases
````
modPredict2 <- predict(modFit, newdata=testing)
````
