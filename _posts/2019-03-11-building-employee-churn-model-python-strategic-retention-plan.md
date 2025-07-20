---
layout: post
title: "Building an Employee Churn Model in Python to Develop a Strategic Retention Plan"
date: 2019-03-11 10:00:00 +0400
categories: [data-science, machine-learning]
tags: [machine-learning, hr, data-science, people-analytics, python, employee-churn, predictive-modeling]
original_url: "https://medium.com/data-science/building-an-employee-churn-model-in-python-to-develop-a-strategic-retention-plan-57d5bd882c2d"
canonical_url: "https://medium.com/data-science/building-an-employee-churn-model-in-python-to-develop-a-strategic-retention-plan-57d5bd882c2d"
description: "A comprehensive guide to building machine learning models for predicting employee churn, featuring exploratory data analysis, model comparison, and strategic retention recommendations."
image: "/assets/images/posts/employee-churn-model.jpg"
republished: true
---

> *Originally published on [Medium]({{ page.original_url }}) on {{ page.date | date: "%B %d, %Y" }}*

![Employee working](https://unsplash.com/photos/QBpZGqEMsKg/download?force=true&w=800)
*Everybody's working hard but who is most likely to hand in their resignation letter? (Photo by Alex Kotliarskyi on Unsplash)*

# Contents

1. Problem Definition
2. Data Analysis
3. EDA Concluding Remarks
4. Pre-processing Pipeline
5. Building Machine Learning Models
6. Concluding Remarks

## 1. Problem Definition

Employee turn-over (also known as "employee churn") is a costly problem for companies. The true cost of replacing an employee can often be quite large.

A study by the [Center for American Progress](https://www.americanprogress.org/wp-content/uploads/2012/11/CostofTurnover.pdf) found that companies typically pay about one-fifth of an employee's salary to replace that employee, and the cost can significantly increase if executives or highest-paid employees are to be replaced.

In other words, the cost of replacing employees for most employers remains significant. This is due to the amount of time spent to interview and find a replacement, sign-on bonuses, and the loss of productivity for several months while the new employee gets accustomed to the new role.

Understanding why and when employees are most likely to leave can lead to actions to improve employee retention as well as possibly planning new hiring in advance. I will be using a step-by-step systematic approach using a method that could be used for a variety of ML problems. This project would fall under what is commonly known as HR Analytics or People Analytics.

![Business meeting](https://unsplash.com/photos/n95VMLxqM2I/download?force=true&w=800)
*Ready to make data-driven decisions! (Photo by rawpixel on Unsplash)*

In this study, we will attempt to solve the following problem statement:

• What is the likelihood of an active employee leaving the company?  
• What are the key indicators of an employee leaving the company?  
• What strategies can be adopted based on the results to improve employee retention?

Given that we have data on former employees, this is a standard supervised classification problem where the label is a binary variable, 0 (active employee), 1 (former employee). In this study, our target variable Y is the probability of an employee leaving the company.

**N.B.** For complete code, please refer to this [GitHub repo](https://github.com/hamzaben86/Employee-Churn-Predictive-Model) and/or the [Kaggle Kernel](https://www.kaggle.com/hamzaben/employee-churn-model-w-strategic-retention-plan).

## 2. Data Analysis

In this case study, a HR dataset was sourced from [IBM HR Analytics Employee Attrition & Performance](https://www.ibm.com/communities/analytics/watson-analytics-blog/hr-employee-attrition/) which contains employee data for 1,470 employees with various information about the employees. I will use this dataset to predict when employees are going to quit by understanding the main drivers of employee churn.

As stated on the [IBM website](https://www.ibm.com/communities/analytics/watson-analytics-blog/hr-employee-attrition/): "This is a fictional data set created by IBM data scientists. Its main purpose was to demonstrate the IBM Watson Analytics tool for employee attrition."

![Data analysis](https://unsplash.com/photos/xDE_p0EXpc8/download?force=true&w=800)
*Let's crunch some employee data! (Photo by rawpixel on Unsplash)*

### 2.1 Data Description and Exploratory Visualisations

First, we import the dataset and make of a copy of the source file for this analysis. The dataset contains 1,470 rows and 35 columns.

The dataset contains several numerical and categorical columns providing various information on employee's personal and employment details.

### 2.2 Data source

The data provided has no missing values. In HR Analytics, employee data is unlikely to feature large ratio of missing values as HR Departments typically have all personal and employment data on-file.

However, the type of documentation data is being kept in (i.e. whether it is paper-based, Excel spreadsheets, databases, etc) has a massive impact on the accuracy and the ease of access to the HR data.

### 2.3 Numerical features overview

A few observations can be made based on the information and histograms for numerical features:

• Several numerical features are tail-heavy; indeed several distributions are right-skewed (e.g. MonthlyIncome DistanceFromHome, YearsAtCompany). Data transformation methods may be required to approach a normal distribution prior to fitting a model to the data.  
• Age distribution is a slightly right-skewed normal distribution with the bulk of the staff between 25 and 45 years old.  
• EmployeeCount and StandardHours are constant values for all employees. They're likely to be redundant features.  
• Employee Number is likely to be a unique identifier for employees given the feature's quasi-uniform distribution.

### 2.4 Feature distribution by target attribute

In this section, a more detailed Exploratory Data Analysis is performed. For complete code, please refer to this [GitHub repo](https://github.com/hamzaben86/Employee-Churn-Predictive-Model) and/or the [Kaggle Kernel](https://www.kaggle.com/hamzaben/employee-churn-model-w-strategic-retention-plan).

**2.4.1 Age**

The age distributions for Active and Ex-employees only differs by one year; with the average age of ex-employees at 33.6 years old and 37.6 years old for current employees.

**2.4.2 Gender**

Gender distribution shows that the dataset features a higher relative proportion of male ex-employees than female ex-employees, with normalised gender distribution of ex-employees in the dataset at 17.0% for Males and 14.8% for Females.

**2.4.3 Marital Status**

The dataset features three marital status: Married (673 employees), Single (470 employees), Divorced (327 employees). Single employees show the largest proportion of leavers at 25%.

**2.4.4 Role and Work Conditions**

A preliminary look at the relationship between Business Travel frequency and Attrition Status shows that there is a largest normalized proportion of Leavers for employees that travel "frequently". Travel metrics associated with Business Travel status were not disclosed (i.e. how many hours of Travel is considered "Frequent").

Several Job Roles are listed in the dataset: Sales Executive, Research Scientist, Laboratory Technician, Manufacturing Director, Healthcare Representative, Manager, Sales Representative, Research Director, Human Resources.

**2.4.5 Years at the Company and Since Last Promotion**

The average number of years at the company for currently active employees is 7.37 years and ex-employees is 5.13 years.

**2.4.6 Years with Current Manager**

The average number of years with current manager for currently active employees is 4.37 years and ex-employees is 2.85 years.

**2.4.7 Overtime**

Some employees have overtime commitments. The data clearly show that there is significant larger portion of employees with OT that have left the company.

**2.4.8 Monthly Income**

Employee Monthly Income varies from $1009 to $19999.

**2.4.9 Target Variable: Attrition**

The feature "Attrition" is what this Machine Learning problem is about. We are trying to predict the value of the feature 'Attrition' by using other related features associated with the employee's personal and professional history.

In the supplied dataset, the percentage of Current Employees is 83.9% and of Ex-employees is 16.1%. Hence, this is an imbalanced class problem.

Machine learning algorithms typically work best when the number of instances of each classes are roughly equal. We will have to address this target feature imbalance prior to implementing our Machine Learning algorithms.

### 2.5 Correlation

Let's take a look at some of most significant correlations. It is worth remembering that correlation coefficients only measure linear correlations.

As shown above, "Monthly Rate", "Number of Companies Worked" and "Distance From Home" are positively correlated to Attrition; while "Total Working Years", "Job Level", and "Years In Current Role" are negatively correlated to Attrition.

## 3. EDA Concluding Remarks

• The dataset does not feature any missing or erroneous data values, and all features are of the correct data type.  
• The strongest positive correlations with the target features are: Performance Rating, Monthly Rate, Num Companies Worked, Distance From Home.  
• The strongest negative correlations with the target features are: Total Working Years, Job Level, Years In Current Role, and Monthly Income.  
• The dataset is imbalanced with the majority of observations describing Currently Active Employees.  
• Single employees show the largest proportion of leavers, compared to Married and Divorced counterparts.  
• About 10% of leavers left when they reach their 2-year anniversary at the company.  
• People who live further away from their work show higher proportion of leavers compared to their counterparts.  
• People who travel frequently show higher proportion of leavers compared to their counterparts.  
• People who have to work overtime show higher proportion of leavers compared to their counterparts.  
• Employees that have already worked at several companies previously (already "bounced" between workplaces) show higher proportion of leavers compared to their counterparts.

## 4. Pre-processing Pipeline

In this section, we undertake data pre-processing steps to prepare the datasets for Machine Learning algorithm implementation. For complete code, please refer to this [GitHub repo](https://github.com/hamzaben86/Employee-Churn-Predictive-Model) and/or the [Kaggle Kernel](https://www.kaggle.com/hamzaben/employee-churn-model-w-strategic-retention-plan).

### 4.1 Encoding

Machine Learning algorithms can typically only have numerical values as their predictor variables. Hence Label Encoding becomes necessary as they encode categorical labels with numerical values. To avoid introducing feature importance for categorical features with large numbers of unique values, we will use both Label Encoding and One-Hot Encoding as shown below.

### 4.2 Feature Scaling

Feature Scaling using MinMaxScaler essentially shrinks the range such that the range is now between 0 and n. Machine Learning algorithms perform better when input numerical variables fall within a similar scale. In this case, we are scaling between 0 and 5.

### 4.3 Splitting data into training and testing sets

Prior to implementation or applying any Machine Learning algorithms, we must decouple training and testing dataframe from our master dataset.

## 5. Building Machine Learning Models

### 5.1 Baseline Algorithms

Let's first use a range of baseline algorithms (using out-of-the-box hyper-parameters) before we move on to more sophisticated solutions. The algorithms considered in this section are: Logistic Regression, Random Forest, SVM, KNN, Decision Tree Classifier, Gaussian NB.

Let's evaluate each model in turn and provide accuracy and standard deviation scores. For complete code, please refer to this [GitHub repo](https://github.com/hamzaben86/Employee-Churn-Predictive-Model) and/or the [Kaggle Kernel](https://www.kaggle.com/hamzaben/employee-churn-model-w-strategic-retention-plan).

Classification Accuracy is the number of correct predictions made as a ratio of all predictions made. It is the most common evaluation metric for classification problems.

However, it is often misused as it is only really suitable when there are an equal number of observations in each class and all predictions and prediction errors are equally important. It is not the case in this project, so a different scoring metric may be more suitable.

Area under ROC Curve (or AUC for short) is a performance metric for binary classification problems. The AUC represents a model's ability to discriminate between positive and negative classes, and is better suited to this project. An area of 1.0 represents a model that made all predictions perfectly. An area of 0.5 represents a model as good as random.

Based on our ROC AUC comparison analysis, Logistic Regression and Random Forest show the highest mean AUC scores. We will shortlist these two algorithms for further analysis.

### 5.2 Logistic Regression

GridSearchCV allows use to fine-tune hyper-parameters by searching over specified parameter values for an estimator. As shown below, the results from GridSearchCV provided us with fine-tuned hyper-parameter using ROC_AUC as the scoring metric.

### 5.3 Confusion Matrix

The Confusion matrix provides us with a much more detailed representation of the accuracy score and of what's going on with our labels — we know exactly which/how labels were correctly and incorrectly predicted. The accuracy of the Logistic Regression Classifier on test set is 75.54.

### 5.4 Label Probability

Instead of getting binary estimated target features (0 or 1), a probability can be associated with the predicted target. The output provides a first index referring to the probability that the data belong to class 0 (employee not leaving), and the second refers to the probability that the data belong to class 1 (employee leaving). Predicting probabilities of a particular label provides us with a measure of how likely an employee is to leave the company.

### 5.5 Random Forest Classifier

Let's take a closer look at using the Random Forest algorithm. I'll fine-tune the Random Forest algorithm's hyper-parameters by cross-validation against the AUC score.

Random Forest allows us to know which features are of the most importance in predicting the target feature ("Attrition" in this project). Below, we plot features by their importance.

Random Forest helped us identify the Top 10 most important indicators (ranked in the table below) as: (1) MonthlyIncome, (2) OverTime, (3) Age, (4) MonthlyRate, (5) DistanceFromHome, (6) DailyRate, (7) TotalWorkingYears, (8) YearsAtCompany, (9) HourlyRate, (10) YearsWithCurrManager.

The accuracy of the RandomForest Regression Classifier on test set is 86.14. Below the corresponding Confusion Matrix is shown.

Predicting probabilities of a particular label provides us with a measure of how likely an employee is to leave the company. The AUC when predicting probabilities using RandomForestClassifier is 0.818.

### 5.6 ROC Graphs

AUC — ROC curve is a performance measurement for classification problem at various thresholds settings. ROC is a probability curve and AUC represents degree or measure of separability. It tells how much model is capable of distinguishing between classes. The green line represents the ROC curve of a purely random classifier; a good classifier stays as far away from that line as possible (toward the top-left corner).

As shown above, the fine-tuned Logistic Regression model showed a higher AUC score compared to the Random Forest Classifier.

## 6. Concluding Remarks

### 6.1 Risk Score

As the company generates more data on its employees (on New Joiners and recent Leavers) the algorithm can be re-trained using the additional data and theoretically generate more accurate predictions to identify high-risk employees of leaving based on the probabilistic label assigned to each feature variable (i.e. employee) by the algorithm.

Employees can be assigning a "Risk Score" based on the predicted label such that:

• Low-risk for employees with label < 0.6  
• Medium-risk for employees with label between 0.6 and 0.8  
• High-risk for employees with label > 0.8

![Decision making](https://unsplash.com/photos/a2VqhP3d4Vg/download?force=true&w=800)
*Good work on reading the analysis — but what now? How does this help decision-makers? (Photo by rawpixel on Unsplash)*

### 6.2 Indicators and Strategic Retention Plan

The stronger indicators of people leaving include:

• **Monthly Income**: people on higher wages are less likely to leave the company. Hence, efforts should be made to gather information on industry benchmarks in the current local market to determine if the company is providing competitive wages.  
• **Over Time**: people who work overtime are more likely to leave the company. Hence efforts must be taken to appropriately scope projects upfront with adequate support and manpower so as to reduce the use of overtime.  
• **Age**: Employees in relatively young age bracket 25–35 are more likely to leave. Hence, efforts should be made to clearly articulate the long-term vision of the company and young employees fit in that vision, as well as provide incentives in the form of clear paths to promotion for instance.  
• **DistanceFromHome**: Employees who live further from home are more likely to leave the company. Hence, efforts should be made to provide support in the form of company transportation for clusters of employees leaving the same area, or in the form of Transportation Allowance. Initial screening of employees based on their home location is probably not recommended as it would be regarded as a form of discrimination as long as employees make it to work on time every day.  
• **TotalWorkingYears**: The more experienced employees are less likely to leave. Employees who have between 5–8 years of experience should be identified as potentially having a higher-risk of leaving.  
• **YearsAtCompany**: Loyal companies are less likely to leave. Employees who hit their two-year anniversary should be identified as potentially having a higher-risk of leaving.  
• **YearsWithCurrManager**: A large number of leavers leave 6 months after their Current Managers. By using Line Manager details for each employee, one can determine which Manager have experienced the largest numbers of employees resigning over the past year.

Several metrics can be used here to determine whether action should be taken with a Line Manager:

• # of years the Line Manager has been in a particular position: this may indicate that the employees may need management training or be assigned a mentor (ideally an Executive) in the organisation  
• Patterns in the employees who have resigned: this may indicate recurring patterns in employees leaving in which case action may be taken accordingly.

### 6.3 Final Thoughts

A strategic retention plan can be drawn for each Risk Score group. In addition to the suggested steps for each feature listed above, face-to-face meetings between a HR representative and employees can be initiated for medium- and high-risk employees to discuss work conditions. Also, a meeting with those employee's Line Manager would allow to discuss the work environment within the team and whether steps can be taken to improve it.

I hope you enjoyed reading this article as much as I had writing it. Once again, for complete code, please refer to this [GitHub repo](https://github.com/hamzaben86/Employee-Churn-Predictive-Model) and/or the [Kaggle Kernel](https://www.kaggle.com/hamzaben/employee-churn-model-w-strategic-retention-plan).

---

*If you found this article helpful, feel free to connect with me on [LinkedIn](https://linkedin.com/in/hamzabendemra){:target="_blank" rel="noopener"} or check out my other articles on [Medium](https://medium.com/@hamzabendemra){:target="_blank" rel="noopener"}.*
