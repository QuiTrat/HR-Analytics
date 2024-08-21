# HR-Analytics
This repository analyze Information related to demographics, education, experience are in hands from candidates signup and enrollment to known which of these candidates are really wants to work for the company after training or looking for a new employment.

## **1. Introduction**

### **Context and Content**

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

## **3. Extract Data**

Import needed library:

```
import pandas as pd
import numpy as np
import math
import statistics
import statsmodels.api as sm
```
Then extract data from sources:

### **Enrollies's data**

We extract file on the Google Sheet that stores data about enrolled students, and look at some information about this data

```
google_sheet_id='1VCkHwBjJGRJ21asd9pxW4_0z2PWuKhbLR3gUHm-p4GI'
url='https://docs.google.com/spreadsheets/d/' + google_sheet_id + '/export?format=xlsx'
enrollies_data= pd.read_excel(url, sheet_name='enrollies')
enrollies_data.info()
enrollies_data.head()
```

![image](https://github.com/user-attachments/assets/d7b84f98-9f2c-4629-a3df-6267827a8681)


|    |   enrollee_id | full_name     | city     | gender   |
|---:|--------------:|:--------------|:---------|:---------|
|  0 |          8949 | Mike Jones    | city_103 | Male     |
|  1 |         29725 | Laura Jones   | city_40  | Male     |
|  2 |         11561 | David Miller  | city_21  | Male     |
|  3 |         33241 | Laura Davis   | city_115 | Male     |
|  4 |           666 | Alex Martinez | city_162 | Male     |

### **Enrollies's Education**

Extract enrollies's education level in the Excel format. This data is stored in my Google Drive, so I need to set a path to my Google Drive before extract data

```
## Mount the path to Google Drive
from google.colab import drive
drive.mount('/content/drive')

# check the file in my drive
!ls /content/drive/MyDrive/Sample_data/enrollies_education.xlsx

# load the data and look information

enrollies_education = pd.read_excel('/content/drive/MyDrive/Sample_data/enrollies_education.xlsx')
enrollies_education.info()
enrollies_education.head()
```
![image](https://github.com/user-attachments/assets/5e2e95d4-7ba3-40fa-9a6e-2bd146429e83)


|    |   enrollee_id | enrolled_university   | education_level   | major_discipline   |
|---:|--------------:|:----------------------|:------------------|:-------------------|
|  0 |          8949 | no_enrollment         | Graduate          | STEM               |
|  1 |         29725 | no_enrollment         | Graduate          | STEM               |
|  2 |         11561 | Full time course      | Graduate          | STEM               |
|  3 |         33241 | nan                   | Graduate          | Business Degree    |
|  4 |           666 | no_enrollment         | Masters           | STEM               |


### **Enrollies' working experience**

Extract enrollies working experience which is stored as csv file in Google drive and explore its information

```
!ls /content/drive/MyDrive/Sample_data/work_experience.csv
work_experience = pd.read_csv('/content/drive/MyDrive/Sample_data/work_experience.csv')
work_experience.info()
work_experience.head()
```
![image](https://github.com/user-attachments/assets/4bcad900-7877-47ba-b9e2-b61739a3c2a2)


|    |   enrollee_id | relevent_experience     | experience   | company_size   | company_type   | last_new_job   |
|---:|--------------:|:------------------------|:-------------|:---------------|:---------------|:---------------|
|  0 |          8949 | Has relevent experience | >20          | nan            | nan            | 1              |
|  1 |         29725 | No relevent experience  | 15           | 50-99          | Pvt Ltd        | >4             |
|  2 |         11561 | No relevent experience  | 5            | nan            | nan            | never          |
|  3 |         33241 | No relevent experience  | <1           | nan            | Pvt Ltd        | never          |
|  4 |           666 | Has relevent experience | >20          | 50-99          | Funded Startup | 4              |


### **Training hours**

Extract tranining hour from LMS system's database
```
!pip install pymysql

from sqlalchemy import create_engine
import pymysql

engine= create_engine(
    'mysql+pymysql://etl_practice:550814@112.213.86.31:3360/company_course'

training_hours=pd.read_sql_table('training_hours', con=engine)
training_hours.info()
training_hours.head()
```

![image](https://github.com/user-attachments/assets/2d5621b0-8697-4cfa-87dc-8651e055139e)


|    |   enrollee_id |   training_hours |
|---:|--------------:|-----------------:|
|  0 |          8949 |               36 |
|  1 |         29725 |               47 |
|  2 |         11561 |               83 |
|  3 |         33241 |               52 |
|  4 |           666 |                8 |


### **City development index**

Extract data from website
```
# Use pandas to read the HTML table
tables = pd.read_html('https://sca-programming-school.github.io/city_development_index/index.html')

# Assuming the first table on the webpage is the one you want (indexing starts from 0)
City = tables[0]

City.info()
City.head()
```

![image](https://github.com/user-attachments/assets/092db3e1-7b30-4c58-9c8c-267fbe574fae)


|    | City     |   City Development Index |
|---:|:---------|-------------------------:|
|  0 | city_103 |                    0.92  |
|  1 | city_40  |                    0.776 |
|  2 | city_21  |                    0.624 |
|  3 | city_115 |                    0.789 |
|  4 | city_162 |                    0.767 |

### **Employment**

Employment data is also stored in the same place as Training hours

```
employment=pd.read_sql_table('employment', con=engine)
employment.info()
employment.head()
```
![image](https://github.com/user-attachments/assets/9f07b4c8-4a43-43b3-b388-4d9ffae1de28)


|    |   enrollee_id |   employed |
|---:|--------------:|-----------:|
|  0 |             1 |          0 |
|  1 |             2 |          1 |
|  2 |             4 |          0 |
|  3 |             5 |          0 |
|  4 |             7 |          0 |

## **4. Transform Data**

### **Enrollies Data**

- Change dtypes of columns: full_name, city and gender

```
enrollies_data['city'] = enrollies_data['city'].astype('category')
enrollies_data['gender'] = enrollies_data['gender'].astype('category')
enrollies_data['full_name'] = enrollies_data['full_name'].astype('string')
```

|             | 0        |
|:------------|:---------|
| enrollee_id | Int64    |
| full_name   | string   |
| city        | category |
| gender      | category |
  
- Handle with missing value in gender column
  
```
### calculate mode() of gender - replace missing value with mode()
gender_mode = enrollies_data['gender'].mode()[0]
enrollies_data['gender'].fillna(gender_mode, inplace=True)
enrollies_data.head()
```

|    |   enrollee_id | full_name     | city     | gender   |
|---:|--------------:|:--------------|:---------|:---------|
|  0 |          8949 | Mike Jones    | city_103 | Male     |
|  1 |         29725 | Laura Jones   | city_40  | Male     |
|  2 |         11561 | David Miller  | city_21  | Male     |
|  3 |         33241 | Laura Davis   | city_115 | Male     |
|  4 |           666 | Alex Martinez | city_162 | Male     |

![image](https://github.com/user-attachments/assets/db10d316-f9fe-4845-91ac-1b4a9afad460)


### **Enrollies Education**

- Handle missing value:

```
enrollies_education['enrolled_university'].fillna('missing',inplace=True)
enrollies_education['education_level'].fillna('missing',inplace=True)
enrollies_education['major_discipline'].fillna('missing',inplace=True)
```


- Change all letter of string in column to lower case


```
enrollies_education['enrolled_university'] = enrollies_education['enrolled_university'].str.lower()
enrollies_education['education_level'] = enrollies_education['education_level'].str.lower()
enrollies_education['major_discipline'] = enrollies_education['major_discipline'].str.lower()
```

- Change space in string to "_" in columns


```
enrollies_education['enrolled_university'] = enrollies_education['enrolled_university'].str.replace(' ', '_')
enrollies_education['education_level'] = enrollies_education['education_level'].str.replace(' ', '_')
enrollies_education['major_discipline'] = enrollies_education['major_discipline'].str.replace(' ', '_')
enrollies_education.head(10)
```

|    |   enrollee_id | enrolled_university   | education_level   | major_discipline   |
|---:|--------------:|:----------------------|:------------------|:-------------------|
|  0 |          8949 | no_enrollment         | graduate          | stem               |
|  1 |         29725 | no_enrollment         | graduate          | stem               |
|  2 |         11561 | full_time_course      | graduate          | stem               |
|  3 |         33241 | missing               | graduate          | business_degree    |
|  4 |           666 | no_enrollment         | masters           | stem               |
|  5 |         21651 | part_time_course      | graduate          | stem               |
|  6 |         28806 | no_enrollment         | high_school       | missing            |
|  7 |           402 | no_enrollment         | graduate          | stem               |
|  8 |         27107 | no_enrollment         | graduate          | stem               |
|  9 |           699 | no_enrollment         | graduate          | stem               |

### **Working Experience**

- Handle missing values

```
work_experience['experience'].fillna('Unknown',inplace=True)
work_experience['company_size'].fillna('Unknown',inplace=True)
work_experience['last_new_job'].fillna('Unknown',inplace=True)
work_experience['company_type'].fillna('Other',inplace=True)
```

|    |   enrollee_id | relevent_experience     | experience   | company_size   | company_type   | last_new_job   |
|---:|--------------:|:------------------------|:-------------|:---------------|:---------------|:---------------|
|  0 |          8949 | Has relevent experience | >20          | Unknown        | Other          | 1              |
|  1 |         29725 | No relevent experience  | 15           | 50-99          | Pvt Ltd        | >4             |
|  2 |         11561 | No relevent experience  | 5            | Unknown        | Other          | never          |
|  3 |         33241 | No relevent experience  | <1           | Unknown        | Pvt Ltd        | never          |
|  4 |           666 | Has relevent experience | >20          | 50-99          | Funded Startup | 4              |
|  5 |         21651 | Has relevent experience | 11           | Unknown        | Other          | 1              |
|  6 |         28806 | Has relevent experience | 5            | 50-99          | Funded Startup | 1              |
|  7 |           402 | Has relevent experience | 13           | <10            | Pvt Ltd        | >4             |
|  8 |         27107 | Has relevent experience | 7            | 50-99          | Pvt Ltd        | 1              |
|  9 |           699 | Has relevent experience | 17           | 10000+         | Pvt Ltd        | >4             |


- Change dtypes of columns [company_size] and [company_type] from object to category

```
work_experience['company_size'] = work_experience['company_size'].astype('category')
work_experience['company_type'] = work_experience['company_type'].astype('category')
```

- Change string (10/49) to (10-49)

```
work_experience.groupby('company_size').count().to_markdown()
```

| company_size   |   enrollee_id |   relevent_experience |   experience |   company_type |   last_new_job |
|:---------------|--------------:|----------------------:|-------------:|---------------:|---------------:|
| 10/49          |          1471 |                  1471 |         1471 |           1471 |           1471 |
| 100-500        |          2571 |                  2571 |         2571 |           2571 |           2571 |
| 1000-4999      |          1328 |                  1328 |         1328 |           1328 |           1328 |
| 10000+         |          2019 |                  2019 |         2019 |           2019 |           2019 |
| 50-99          |          3083 |                  3083 |         3083 |           3083 |           3083 |
| 500-999        |           877 |                   877 |          877 |            877 |            877 |
| 5000-9999      |           563 |                   563 |          563 |            563 |            563 |
| <10            |          1308 |                  1308 |         1308 |           1308 |           1308 |
| Unknown        |          5938 |                  5938 |         5938 |           5938 |           5938 |


```
work_experience['company_size'] = work_experience['company_size'].str.replace('/', '-')
```

| company_size   |   enrollee_id |   relevent_experience |   experience |   company_type |   last_new_job |
|:---------------|--------------:|----------------------:|-------------:|---------------:|---------------:|
| 10-49          |          1471 |                  1471 |         1471 |           1471 |           1471 |
| 100-500        |          2571 |                  2571 |         2571 |           2571 |           2571 |
| 1000-4999      |          1328 |                  1328 |         1328 |           1328 |           1328 |
| 10000+         |          2019 |                  2019 |         2019 |           2019 |           2019 |
| 50-99          |          3083 |                  3083 |         3083 |           3083 |           3083 |
| 500-999        |           877 |                   877 |          877 |            877 |            877 |
| 5000-9999      |           563 |                   563 |          563 |            563 |            563 |
| <10            |          1308 |                  1308 |         1308 |           1308 |           1308 |
| Unknown        |          5938 |                  5938 |         5938 |           5938 |           5938 |

### **Training hours**

I calculate summary Statistics training hours Columns
```
training_hours.describe()
```

|       |   enrollee_id |   training_hours |
|:------|--------------:|-----------------:|
| count |      19158    |       19158      |
| mean  |      16875.4  |          65.3669 |
| std   |       9616.29 |          60.0585 |
| min   |          1    |           1      |
| 25%   |       8554.25 |          23      |
| 50%   |      16982.5  |          47      |
| 75%   |      25169.8  |          88      |
| max   |      33380    |         336      |

We see that the max training hours is 336 a, It looks like a invalid data, we would've needed to consider this as a wrong input.
I will remove these outlier to make data more realistic

```
### Calculate q25, q75

q25 = training_hours['training_hours'].quantile(0.25)
q75 = training_hours['training_hours'].quantile(0.75)

### IQR

iqr = q75 - q25
lower_bound = q25 - (iqr * 1.5)
upper_bound = q75 + (iqr * 1.5)

### remove outlier

training_hours = training_hours[(training_hours['training_hours'] >= lower_bound) & (training_hours['training_hours'] <= upper_bound)]
```

|       |   enrollee_id |   training_hours |
|:------|--------------:|-----------------:|
| count |      18174    |       18174      |
| mean  |      16886.7  |          55.4997 |
| std   |       9612.65 |          42.3055 |
| min   |          1    |           1      |
| 25%   |       8598.25 |          22      |
| 50%   |      17009.5  |          45      |
| 75%   |      25181.2  |          80      |
| max   |      33380    |         184      |


## **5. Load data into warehouse**


