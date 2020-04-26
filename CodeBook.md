---
title: "CodeBook_Run_Analysis"
author: "Ravi Agrawal"
date: "4/26/2020"
output: html_document
---



## Read the Activity label in the detaframe df_activity_lbl

```
df_activity_lbl <-read.table("./Data/UCI HAR Dataset/activity_labels.txt",col.names=c("activity_cd","activity_desc"))
```

## Read the feature label in the detaframe df_feature_lbl
```
df_feature_lbl <-read.table("./Data/UCI HAR Dataset/features.txt",col.names=c("feature_id","feature_desc"))
```

## Read the train dataset feature in df_train_feature. 
```
df_train_feature <-read.table("./Data/UCI HAR Dataset/train/X_train.txt")
```

##Also added the columns from feature label dataframe by concatnating the id and name
```
colnames(df_train_feature) = paste(df_feature_lbl$feature_id,df_feature_lbl$feature_desc,sep="_")
```

## Read the train dataset activity in df_train_activity
```
df_train_activity<-read.table("./Data/UCI HAR Dataset/train/Y_train.txt",col.names="activity_cd")
```

## Read the train dataset subject in df_train_subject
```
df_train_subject<-read.table("./Data/UCI HAR Dataset/train/subject_train.txt",col.names="subject_id")
```


##Join all the train dataset using cbind
```
df_train_all<-cbind(df_train_subject,df_train_activity,df_train_feature)
```

##Refine the train dataset by selecting only mean,activity, standard deviation column. Also name the column as descriptve possible
```
df_train_refine<-df_train_all%>%select(contains("activity_cd")|contains("subject_id")|contains("mean()")|contains("std()"))%>%
                             setNames(gsub("[0-9]","",names(.)))%>% ##Removed the numbers
                             setNames(gsub("_tBody","time_body",names(.)))%>%
                             setNames(gsub("_fBody","freq_body",names(.)))%>%
                             setNames(gsub("Acc","_accelerometer_",names(.)))%>%
                             setNames(gsub("Gyro","_gyroscope_",names(.)))%>%
                             setNames(gsub("Mag","_magnitude_",names(.)))%>%
                             setNames(gsub("_tGravity","time_Gravity_",names(.)))%>%
                             setNames(gsub("-","_",names(.)))%>%
                             setNames(gsub("\\()","",names(.)))%>%
                             setNames(gsub("__","_",names(.)))

```
## Read the test dataset feature in df_test_feature. 
```
df_test_feature <-read.table("./Data/UCI HAR Dataset/test/X_test.txt")
```

##Also added the columns from feature label dataframe by concatnating the id and name
```
colnames(df_test_feature) = paste(df_feature_lbl$feature_id,df_feature_lbl$feature_desc,sep="_")
```

## Read the test dataset activity in df_test_activity
```
df_test_activity<-read.table("./Data/UCI HAR Dataset/test/Y_test.txt",col.names="activity_cd")
```

## Read the test dataset subject in df_test_subject
```
df_test_subject<-read.table("./Data/UCI HAR Dataset/test/subject_test.txt",col.names="subject_id")
```

##Join all the test dataset using cbind
```
df_test_all<-cbind(df_test_subject,df_test_activity,df_test_feature)
```


##Refine the test dataset by selecting only mean,activity, standard deviation column. Also name the column as descriptve possible
```
df_test_refine<-df_test_all%>%select(contains("activity_cd")|contains("subject_id")|contains("mean()")|contains("std()"))%>%
  setNames(gsub("[0-9]","",names(.)))%>% ##Removed the numbers
  setNames(gsub("_tBody","time_body",names(.)))%>%
  setNames(gsub("_fBody","freq_body",names(.)))%>%
  setNames(gsub("Acc","_accelerometer_",names(.)))%>%
  setNames(gsub("Gyro","_gyroscope_",names(.)))%>%
  setNames(gsub("Mag","_magnitude_",names(.)))%>%
  setNames(gsub("_tGravity","time_Gravity_",names(.)))%>%
  setNames(gsub("-","_",names(.)))%>%
  setNames(gsub("\\()","",names(.)))%>%
  setNames(gsub("__","_",names(.)))
```
##Append both train and test dataset
```
df_full_stg1<-rbind(df_train_refine,df_test_refine)
```

##populate the value of activity
```
df_full_final<-merge(df_full_stg1,df_activity_lbl,by="activity_cd")
```
```
write.table(df_full_final, file="merge_train_test.txt", append = FALSE, sep = "|", dec = ".",col.names = TRUE)
```

##get the average of test and train data group by subject and activity
```
df_summary<-df_full_final%>%group_by(subject_id,activity_desc,activity_cd)%>%summarise_all(mean,na.rm=TRUE)
```

```
write.table(df_summary, file="avearage_summary.txt", append = FALSE, sep = "|", dec = ".",col.names = TRUE)
```

