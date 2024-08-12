# HR-Analytics
This repository analyze Information related to demographics, education, experience are in hands from candidates signup and enrollment to known which of these candidates are really wants to work for the company after training or looking for a new employment.

## **1. Introduction**

###**Context and Content**

A company which is active in Big Data and Data Science wants to hire data scientists among people who successfully pass some courses which conduct by the company.

Many people signup for their training. Company wants to know which of these candidates are really wants to work for the company after training or looking for a new employment because it helps to reduce the cost and time as well as the quality of training or planning the courses and categorization of candidates.

Information related to demographics, education, experience are in hands from candidates signup and enrollment.

## **2. Data Source**

### **2.1 Enrollies' data**

As enrollies are submitting their request to join the course via Google Forms, we have the Google Sheet that stores data about enrolled students, containing the following columns:

- enrollee_id: unique ID of an enrollee

- full_name: full name of an enrollee

- city: the name of an enrollie's city

- gender: gender of an enrollee

The source: https://docs.google.com/spreadsheets/d/1VCkHwBjJGRJ21asd9pxW4_0z2PWuKhbLR3gUHm-p4GI/edit?usp=sharing

### **2.2 Enrollies' education**

After enrollment everyone should fill the form about their education level.

This form is being digitalized manually. Educational department stores it in the Excel format here: https://assets.swisscoding.edu.vn/company_course/enrollies_education.xlsx

This table contains the following columns:

- enrollee_id: A unique identifier for each enrollee. This integer value uniquely distinguishes each participant in the dataset.

- enrolled_university: Indicates the enrollee's university enrollment status. Possible values include no_enrollment, Part time course, and Full time course.

- education_level: Represents the highest level of education attained by the enrollee. Examples include Graduate, Masters, etc.

- major_discipline: Specifies the primary field of study for the enrollee. Examples include STEM, Business Degree, etc.

### **2.3  Enrollies' working experience**

Another survey that is being collected manually by educational department is about working experience.

Educational department stores it in the CSV format here: https://assets.swisscoding.edu.vn/company_course/work_experience.csv

This table contains the following columns:

- enrollee_id: A unique identifier for each enrollee. This integer value uniquely distinguishes each participant in the dataset.

- relevent_experience: Indicates whether the enrollee has relevant work experience related to the field they are currently studying or working in. Possible values include Has relevent experience and No relevent experience.

- experience: Represents the number of years of work experience the enrollee has. This can be a specific number or a range (e.g., >20, <1).

- company_size: Specifies the size of the company where the enrollee has worked, based on the number of employees. Examples include 50−99, 100−500, etc.

- company_type: Indicates the type of company where the enrollee has worked. Examples include Pvt Ltd, Funded Startup, etc.

- last_new_job: Represents the number of years since the enrollee's last job change. Examples include never, >4, 1, etc.

### **2.4 Training hours**

From LMS system's database you can retrieve a number of training hours for each student that they have completed.

Database credentials:

- Database type: MySQL

- Host: 112.213.86.31

- Port: 3360

- Login: etl_practice

- Password: 550814

- Database name: company_course

- Table name: training_hours

### **2.5 City development index**

Another source that can be usefull is the table of City development index.

The City Development Index (CDI) is a measure designed to capture the level of development in cities. It may be significant for the resulting prediction of student's employment motivation.

It is stored here: https://sca-programming-school.github.io/city_development_index/index.html

### **2.6  Employment**

From LMS database you can also retrieve the fact of employment. If student is marked as employed, it means that this student started to work in our company after finishing the course.

Database credentials:

- Database type: MySQL

- Host: 112.213.86.31

- Port: 3360

- Login: etl_practice

- Password: 550814

- Database name: company_course

- Table name: employment

##**3. Extract Data**

Import needed library:

```
import pandas as pd
import numpy as np
import math
import statistics
import statsmodels.api as sm
```
Then extract data from sources:

###**Enrollies_data**

We extract file on the Google Sheet that stores data about enrolled students

```
google_sheet_id='1VCkHwBjJGRJ21asd9pxW4_0z2PWuKhbLR3gUHm-p4GI'
url='https://docs.google.com/spreadsheets/d/' + google_sheet_id + '/export?format=xlsx'
enrollies_data= pd.read_excel(url, sheet_name='enrollies')
enrollies_data.info()
```

<class 'pandas.core.frame.DataFrame'>
RangeIndex: 19158 entries, 0 to 19157
Data columns (total 4 columns):
 #   Column       Non-Null Count  Dtype   
---  ------       --------------  -----   
 0   enrollee_id  19158 non-null  int64   
 1   full_name    19158 non-null  string  
 2   city         19158 non-null  category
 3   gender       19158 non-null  category
dtypes: category(2), int64(1), string(1)
memory usage: 342.0 KB
