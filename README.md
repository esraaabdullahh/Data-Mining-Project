# Data-Mining-Project
# Fake Account Detection Using Data Mining Algorithms

**By: Esraa Abdullah**

## 1. Project Description

This project uses data mining techniques to classify Instagram profiles based on their numerical features.

The goal is to predict one of two labels:

* Genuine account
* Fake account

Three classification algorithms are compared:

* Logistic Regression
* Decision Tree
* Random Forest

A majority baseline that always predicts genuine is also included for comparison.

---

## 2. Dataset

The project uses the **InstaFake dataset** published by Fatih Cagatay Akyon and Esat Kalfaoglu.

The selected files contain **1,194 records**:

* 994 genuine-labelled profiles
* 200 fake-labelled profiles

Each record contains eight input features:

| Feature | Description |
| --- | --- |
| `followers` | Number of followers |
| `following` | Number of accounts followed |
| `bio_length` | Number of characters in the biography |
| `posts` | Number of posts |
| `has_profile_pic` | Profile picture present: 0 = no, 1 = yes |
| `is_private` | Private account: 0 = no, 1 = yes |
| `username_digits` | Number of digits in the username |
| `username_length` | Total username length |

The target is `is_fake`: **0 = genuine** and **1 = fake**. The original source calls this field `isFake`.

Only the `fake-v1.0` subset is used. The paper reports 1,203 records for the fake-account task, but the released files used in this project contain 1,194.

---

## 3. Data Preprocessing

The following steps are applied before model evaluation:

* Check numeric values, binary flags and username lengths.
* Remove 29 exact repeated feature-and-label rows before splitting.
* Retain 1,165 records: 965 genuine and 200 fake.
* Apply `log1p` to followers, following, biography length and posts.
* Add three features: relative following activity, username digit fraction and a no-posts indicator.
* Standardize features for Logistic Regression only.

The final feature set contains **11 features**. Median imputation is included in the training pipeline, although no missing values were found. Imputation and scaling are fitted within training folds to reduce data leakage.

There are no original account IDs, so identical measurements could belong to different accounts. Deduplication prevents exact recorded vectors from appearing in both training and testing.

---

## 4. Train/Test Split

The cleaned dataset is divided into:

* **80% training data:** 932 records
* **20% testing data:** 233 records

A stratified split preserves the class proportions approximately. The random seed is fixed at 42.

Five-fold cross-validation and GridSearchCV are used on the training set to select model settings. The final model is chosen using the highest mean F1 for the fake class before evaluating the test set.

---

## 5. Classification Algorithms

### Logistic Regression

Logistic Regression learns a linear relationship between the input features and the class score. It provides a simple comparison model.

### Decision Tree

Decision Tree classifies profiles using a sequence of feature-based conditions.

### Random Forest

Random Forest combines 200 decision trees to capture feature interactions and reduce the instability of a single tree.

---

## 6. Evaluation Metrics

The main evaluation metrics are:

* **Accuracy:** the proportion of all profiles classified correctly.
* **Precision:** the proportion of predicted fake profiles that have a fake label.
* **Recall:** the proportion of fake-labelled profiles that are detected.
* **F1-score:** the harmonic mean of precision and recall.

A confusion matrix is used to examine errors. ROC-AUC and average precision are also calculated.

Fake-class F1 is used for model selection because most profiles are genuine, so accuracy alone can be misleading. The score threshold is fixed at 0.50.

---

## 7. Results

The following values are from the reference run. Precision, recall and F1 refer to the fake class.

| Algorithm | Accuracy | Precision | Recall | F1-score |
| --- | ---: | ---: | ---: | ---: |
| Majority baseline | 82.83% | 0.00% | 0.00% | 0.00% |
| Logistic Regression | 95.71% | 94.12% | 80.00% | 86.49% |
| Decision Tree | 95.71% | 91.67% | 82.50% | 86.84% |
| Random Forest | 97.00% | 97.14% | 85.00% | 90.67% |

Random Forest achieved the highest mean training cross-validation F1, approximately **0.9190**, and was selected.

On the test set, it:

* Correctly classified 226 of 233 profiles.
* Detected 34 of 40 fake-labelled profiles.
* Missed six fake-labelled profiles.
* Incorrectly flagged one genuine-labelled profile.

Results may vary slightly with package versions. The notebook displays the values calculated in the current run.

---

## 8. Conclusion

The project demonstrates how numerical profile features can be used for fake-account classification.

All three learned models detected fake-labelled profiles more effectively than the majority baseline. Random Forest performed best under the selected evaluation procedure.

The dataset is small, historical and manually labelled. The results do not establish performance on current Instagram accounts. A model prediction is not proof of account identity, and its score is not a calibrated probability.

---

## 9. Future Work

Possible improvements include:

* Using a larger and more recent dataset.
* Evaluating on independently labelled profiles.
* Splitting by time or account groups when this information is available.
* Comparing feature sets within the training data.
* Calibrating model scores and selecting a threshold using separate validation data.

---

## 10. Tools

* Python
* NumPy and pandas
* Scikit-learn
* Matplotlib
* Joblib
* Ipywidgets
* Google Colab
* GitHub for project submission

---

## 11. How to Run

1. Open [Google Colab](https://colab.research.google.com/).
2. Select **File → Upload notebook**.
3. Upload `Fake_Account_Detection_DM.ipynb`.
4. Use a Python 3 runtime. CPU is sufficient.
5. Select **Runtime → Run all** and wait for training to finish.
6. Review the model comparison and charts.
7. In **Interactive demo**, enter the eight original feature values and click **Analyze**.
8. Use **Download project ZIP** to save the generated files. Save the notebook separately from the File menu.

An internet connection is required to download the dataset and any missing dependencies. No local installation or compilation is needed.

For CSV predictions, include exactly the eight input columns listed in Section 2, without labels or ID columns. The interface applies feature transformations automatically. Its example buttons use existing test-set profiles for demonstration.

---

## 12. Dataset References

* **Authors:** Fatih Cagatay Akyon and Esat Kalfaoglu.
* **Dataset:** [InstaFake repository](https://github.com/fcakyon/instafake-dataset).
* **Research paper:** [Instagram Fake and Automated Account Detection](https://arxiv.org/abs/1910.03090).
* **Subset:** `data/fake-v1.0`.
* **Pinned revision:** `cf669b27283aa3bc0fa6b8d185d4e611e9061ac1`.
* **License:** [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/). Retain attribution and follow the noncommercial terms.

The notebook records the source URLs and downloaded file hashes in `data/provenance.json`.
