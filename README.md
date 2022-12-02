# 365DataScience-students
Project for 365DataScience competition (Dec. 2, 2022) involving analysis of student data and activities. The task was to develop a machine learning model to predict whether a Free Plan user would convert to a paid subscriber or not.

The project is contained in 3 notebooks: DataAnalysis, FeaturesEngineering and Model.

In the first notebook, I analyzed provided databases to understand and clean the data. I wrote short summaries after each analysis. I found outliers in student_exams dataset (exam_completion_time) and student_learning dataset (minutes_watched). Countries were missing in some cases in the student data, but I decided that I would not take this data into account in the model. In addition, in student_quizzes in the answer_id field I found 229 nulls and error in the type of the answer_id identifier (float instead of int).

In the second notebook, I assigned values from 99 quantile to outliers, and 0 to nulls (which will be treated as a wrong answer). I also corrected the above mentioned variable type. Then I created datasets with new features for each user. I assumed that the datasets will cover activity up to the first payment in the case of paying students, and all data in the case of free accounts.

These datasets with data assigned to each students are:
* Courses (the number of courses of each type, time spent on each course, general and user course rankings), 
* Quizzes (average score of all quizzes)
* Engagements (number of exams, quizzes, and lessons)
* Exams (number, average time, and average percentage time compared to maximum time, by exam type)
* Questions (number of questions asked)
* Timing (intervals between payment or year-end date and the dates of: registration, first and last course, first and last quiz, first and last exam, first and last question, closest marketing campaigns conducted).

The final dataset Students contains all of the above data along with the dependent variable, i.e. whether the student has made any payment ("Paying" equal to 1 or zero).

In the third notebook, after loading the dataset with features, I found out that the data is not balanced (only 2135 out of 35230 students have class 1).

I selected 10 classifiers for the analysis, with the greatest importance given to those that firstly have a class_weight="balanced" parameter, which allows the analysis of unbalanced data by adjusting the weights in the model, and secondly enable the extraction of important features that affect the model results. I also selected some other classifiers for comparison.

I assumed that the key metric would be "recall", because we care about finding all students who have actually started paying. We are less concerned about minimizing false positives, as this will be the group on which we can focus marketing efforts.

The "Recall" of the various models are as follows:
* KNeighborsClassifier: 0,9902
* LogisticRegression: 0,9951
* RidgeClassifier: 0,9975
* SVC: 0,9975
* LinearSVC: 0,9951
* GaussianNB: 1,0000
* MultinomialNB: 0,8284
* BernoulliNB: 0,7475
* RandomForestClassifier: 0,9951
* DecisionTreeClassifier: 1,0000

"Recall" in GaussianNB was equal to one, but Precision was very low (0.1). In contrast, with DecisionTreeClassifier, both metrics were equal to 1.

However, a feature importance analysis showed that DecisionTreeClassifier relied on only one variable, concerning the period of time between the last advertising campaign and the payment date, or the end of the year. Many non-paying students had this period similarly long, which falsified the result. Therefore, classifiers that relied on more features are more reliable. These included:
* LogisticRegression: 38 features
* RidgeClassifier: 54 features
* LinearSVC: 51 features
* RandomForestClassifier: 16 features

Then, among all the features relevant to the above classifiers, I selected those that were repeated at least 3 times. For these, I conducted a difference analysis between the set of paying students and the set of all students. The goal was to find characteristics that distinguish students who paid.

**An interesting observation is that paying students generally rate the courses more harshly. They are also slightly less likely to participate in them. They stand out the most for 2 features: engagement to quizzes (almost twice the average) and quizzes results (almost six times). It can also be noted that, on average, they make their first payment 3 weeks after the start of the last marketing campaign and relatively soon after the last course and last quiz.**
