# AI for Medical Diagnosis

## Module 01

### Define Prognosis

Prognosis: A medical term that refers to predicting the risk of a failure event. Events can include outcomes such as death, heart failure, or a stroke. 

Prognosis is useful for informing patients, their risk of developing illness and useful for prediction of survival time with a illness. It is also useful for guiding treatment.

### Prognostic Models in Practice

A prognosis model can take input the patient profile which may include the clinical history, physical examination, labs and imaging.  
It output the risk scores, which can be arbitrary numbers or probabilities.

Eg: Atrial Fibrillation, a risk calculator $CHA_2DS_2-VASc$ (chads vasc) which predicts 1-year risk of stroke.

### Representing Feature Interaction

Risk Equation:

$$
score = (co\_eff)_A * A + (co\_eff)_B * ln(B) + ... + (co\_eff)_{KL} * ln(K) * L + ... + log(co\_eff)_M * M + (co\_eff)_N * N
$$

The risk equation doesn't need to be linear in the features themselves, it can be linear in $ln$ or $log$ of the features. Risk equation can also include interatction term like $KL$ above.

Effect of Interaction terms:

Without interaction terms, two features when added and plotted on a graph, we can say that when one feature is fixed, and the other feature is increased in the value, the overall score increases (score: Yellow > Green > Blue), irrespective of where we fixed the value in the first feature.

![](./images/WithoutInteraction.png)

With interaction terms, both the features are multiplied, now the relationship between both features depends, like in the image below when the age is 3.8, we can se as BP increase the risk score increase, but whereas at age 4.5, even with increase in BP the risk score remains in yellow.
 
![](./images/WithInteraction.png)

### Evaluating Prognostic Models

Basic idea of evaluation of prognostic models is to see how well it performs on pair of patients.

Compare risk scores it assign to pair of individuals and to evaluate, whether the patients had the event. 

Patient with worse outcome have a higher risk score, pair is called `concordant`, and if the patient doesn't have a higher risk score, it is called `not concordant`.

We can't use the pair of patients who have the same outcome (pair is called `not permissible`).

To evaluate the prognostic model, we give

+1, for a permissible pair that is concordant.  
+0.5 for a permissible pair for risk tie.

$$
C-index = \frac{no.\ of\ \ concordant\ pairs + 0.5 * no.\ of\ risk\ ties}{no.\ of\ permissible\ pairs}
$$

C-index interpretation

$P(score(A) > score(B) | Y_A > Y_B)$

Random Model score = 0.5  
Perfect Model score = 1.0

## Module 2

Decision Trees:  
They divide the input space into high-risk and low-risk using vertical and horizontal boundaries. They can be viewed as partioning the input feature space into regions and can be represented as tree of if-else.

Can model non-linear associations, which can't be modelled by linear models.

After partioning, the risk-score is the fraction of patients affected (dead/had postive outcome of an event). We can binarise the output by using a threshold over the risk-score.

### Fixing Overfitting

If we don't stop growing the decision trees, they continue to create more and more partitions and get overly complex. Decision tree models can create overly complex trees that fit the training data almost perfectly.

Can combat overfitting, by fixing the max depth a tree can grow to.

Another way to combat is by using **Random Forest**.

Random forests construct multiple decision trees and average their risk predictions.  
Each tree in the forest is constructed using a random sample (sampling with replacement) of the patients.  
Random Forest algorithm also modifies the splitting procedure in the construction of decision trees such that it uses a subset of features when creating decision boundaries

other popular algorithms that use ensembles including **Gradient Boosting**, **XGBoost**, and **LightGBM**

### Identifying Missing Data

Naive method is to exclude the data points, having any missing data. But this leads a major issue of change in distribution.

Even if we drop data points after splitting into train and test set. The model trained on train set, may perform good on test set, but when a new test set arrives, the model may perform poorly, since the new dataset may not have any missing value and the distribution without dropping is much different from the initial train or test set.

#### Missing Completely at Random
Missingness not dependent on anything is the missing value is a complete random event, which can happend to any datapoint.

$$
p(missing) = constant
$$

A complete-case analysis (where we drop missing value) will **NOT** lead to a biased model

#### Missing at Random

Missingness depends only on available information. The missing values is due to a variable, like some fixed value of randomness when the value of a variable is something and some other randomness when value of variable is something else.

$$
p(missing) \ne constant
$$
$$
p(missing|variable < x) = some\_value \ne p(missing|variable>x)
$$

#### Missing Not at Random

Missingness depends on unavailable information. The values are missing due to some variable which is not available in the dataset and the missigness can't be determined.

$$
p(missing) \ne constant
$$

### Imputation

Mean Imputation:

Fill all the missing values with the mean of the column apart from those missing values.

For test data too, use the mean of the column from the train dataset to impute the missing values.

Regression Imputation:

Learn a linear model for a column with missing values with the other columns(features).

## Module 3

### Survival Models

A survival model outputs the survival function which gives the probability that the time to event (death/risk) is is greather than some time $t$.

$$
S(t) = Pr(T > t)
$$

This is 1 - Prognosis modelling which we did earlier, but the extra addition is for $t$ 2 or 5 or 10 years, we had to create separate models, but a single survival model can do for any time $t$.

#### Properties
* $S(u) \le S(v)\ if\ u \ge v$ - As time goes by, the survival probability should never go up (stay constant or go down)
* (typically) $S(t)\ is\ 1\ if\ t = 0,\ else\ 0\ if\ t = \infty$

Censoring (in data), is a case where we want to see time for a particular event to happen, but might not see the event for several reasons like end of study, patient withdrawal etc.

In Survival data, we change the data when compared to prognosis model where the label was binary (0/1 for the event), to a label which is time taken for the event (in months, years etc.). 

For ex:
|i|Yi|
|-|-|
|1|12|
|2|48+|
|3|24+|

Where the second patient is a censor data, as the study was done by 4 years and the patient didn't had the event during study and last patient is another censor data where the patient dropped after 2 years, so the value is $24+$

### Estimate Survival

$$
S(t) = Pr(T > t) = \frac{no.\ of\ patients survived to t months}{no.\ of\ patients}
$$

#### Example Survival Estimation where t = 25 and with censored observation.

$$
S(25) = P(T > 25)
\newline
= P(T \ge 26)
\newline
= P(T \ge 26, T \ge 25, ..., T \ge 0)
\newline
= P(T \ge 26 | T \ge 25)P(T \ge 25 | T \ge 24) ... P(T \ge 1 | T \ge 0) P(T \ge 0)
\newline
\because P(T \ge 0) = 1
\newline
= P(T \ge 26 | T \ge 25)P(T \ge 25 | T \ge 24) ... P(T \ge 1 | T \ge 0)
\newline
P(T \ge 26 | T \ge 25) = P(T > 25 | T \ge 25) = 1 - P(T = 25 | T \ge 25)
\newline
\implies S(25) = (1 - P(T = 25 | T \ge 25)) (1 - P(T = 24 | T \ge 24)) ... 1 - P(T = 0 | T \ge 0)
\newline
P(T = 25 | T \ge 25) = \frac{no.\ of\ died\ at\ exactly\ 25}{no.\ of\ known\ to\ survive\ to\ 25}
\newline
$$

Generalization the above for any time $t$.
$$
S(t) = \prod_{i=0}^{t} 1 - P(T = i | T \ge i)
\newline
\implies \frac{no.\ of\ died\ at\ exactly\ at\ i}{no.\ of\ known\ survive\ to\ i}
\newline
\implies S(t) = \prod_{i=0}^{t} 1 -\frac{d_i}{n_i}
$$

This is known as Kaplan Meier Estimate and is applied to all the patients in the data and isn't specific to a particular patient.