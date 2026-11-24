# Build a machine learning intrusion detector

Work through the assignment in order and write each notebook cell yourself. The goal for today is one reproducible binary classifier, two readable figures, and a short report explaining the evidence and how an organization might use it.

## Schedule for November 24, 2026

| Time (IST) | Checkpoint |
| --- | --- |
| 17:15–17:40 | Define the problem, set up a notebook, and locate the dataset |
| 17:40–18:30 | Load the data, audit it, and build preprocessing |
| 18:30–19:10 | Fit the baseline and one classifier |
| 19:10–19:50 | Evaluate predictions and save two figures |
| 19:50–20:45 | Write the report and run the notebook from a fresh kernel |

If time gets tight, keep one model and the required metrics. Skip tuning and additional plots. Start the report as you go by writing one short justification at the end of each notebook section.

## 1. Define what an alert means

Use an enterprise network as your example. A sensor summarizes each connection, and the model classifies the summary as normal or malicious. A positive prediction creates an alert that an analyst can review. A rule may flag a known signature. A supervised model can learn combinations of measured features associated with past labels, and its performance depends on how representative those examples are.

**Think it through.** A busy backup transfers far more bytes than a typical connection. What other available information would make this event worth investigating? Why would a rule based on byte count alone produce false alerts?

**Your checkpoint.** Write a paragraph defining the organization, a single prediction, the input, both labels, and who acts on an alert. Give one advantage and one limitation of each approach.

## 2. Get the notebook and data ready

Use the existing project and uv environment. In a fresh notebook, confirm the kernel uses the intended environment. You need a notebook kernel, pandas, scikit-learn, and matplotlib. The project currently declares Python 3.14 or newer, so check the kernel version if dependency resolution or imports fail.

Use NSL-KDD for the first pass. The original UNB page describes the dataset and split but says it no longer hosts the files. The benchmark mirror provides the full training and test text files. Record the distribution you actually use and cite the dataset and paper named by UNB. Keep the two files separate throughout the exercise. [UNB dataset description](https://www.unb.ca/cic/datasets/nsl.html) · [NSL-KDD file mirror](https://github.com/defcom17/NSL_KDD)

**Think it through.** Each row describes a connection using fields such as protocol and byte counts. If the label or difficulty score is a model input, what information would the deployed model need that a sensor cannot provide?

**Your checkpoint.** Read the headerless files using verified column names. Confirm each row has 41 features, a label, and a difficulty field. Inspect training shapes, data types, missing and non-finite values, duplicate counts, and class balance. Convert normal to 0 and attack categories to 1 after checking the original labels. Keep the original categories separately if you want to describe errors later.

## 3. Build preprocessing

1. Separate predictors and target. Exclude the label and benchmark difficulty field. Keep the available predictors for the initial model unless your audit gives a reason to drop one. Explain your feature selection in the report.
2. Inspect protocol, service, and flag as categorical fields. Use one-hot encoding and decide how to handle categories first seen when predicting. Explain why arbitrary integer codes would imply an ordering.
3. Check numeric values and document any cleaning you actually perform. If values are missing, learn any imputation values exclusively from training data. Standardize numeric inputs for logistic regression so variables on large scales do not dominate regularization simply because of their units.
4. Combine transformations and the classifier in one pipeline. If you need validation, split it from the training file and fit every learned transformation inside that split. Reserve the provided test file for final evaluation. [Scikit-learn guidance on data leakage](https://scikit-learn.org/stable/common_pitfalls.html)

**Think it through.** If training byte counts average 100 and test byte counts average 500, which mean should the deployed preprocessing use? What does using both reveal about the future data?

**Your checkpoint.** Explain each transformation in one sentence and verify that the feature list excludes both metadata fields. Check that the fitted pipeline can predict on held-out rows with the same raw feature schema.

## 4. Train one classifier and a baseline

Use a majority-class baseline and logistic regression. The baseline always predicts the most common training label. Logistic regression combines learned feature weights into a score and maps that score to an estimated probability. Compare how much each model learns about the positive class.

**Think it through.** Suppose 96% of connections are normal and your model always predicts normal. What accuracy and malicious recall should you expect? What does this tell you about accuracy as a headline number?

**Your checkpoint.** Fit using the training file. Keep the default decision threshold for this first pass. Record your model choice and any settings. When you finish choosing the model, generate predictions for the provided test file.

## 5. Evaluate and visualize

Report accuracy, precision, recall, and F1 for both models on the same test split and use malicious as the positive class. Add a labeled confusion matrix for logistic regression. Use a training class-count chart as the second figure. Include axes, labels, and counts readers can interpret.

| Measure | Question it answers |
| --- | --- |
| Accuracy | What fraction of all predictions were right? |
| Precision | What fraction of alerts corresponded to malicious connections? |
| Recall | What fraction of malicious connections triggered an alert? |
| F1 | What is the harmonic mean of precision and recall? |

**Think it through.** Suppose the model alerts on 12 connections, 9 of which are malicious, and misses 6 other malicious connections. Compute precision and recall by hand before using the metrics library. Then explain the staffing cost of the false positives and the security cost of the false negatives.

**Your checkpoint.** Verify the confusion-matrix counts sum to the test row count, clearly label actual and predicted axes, and recompute one metric from the counts. Describe an actual result using the measured numbers.

## 6. Explain how it could run in production

Describe the path from a network sensor to flow features, model scoring, an alert, and analyst review in a SIEM. Explain how a firewall could consume reviewed alerts or a separate response policy. Start your proposed rollout in observation mode and identify the evidence you would need before enabling automated blocking.

Cover these three challenges in the report.

- **Concept drift.** A network migration or new application changes legitimate traffic. Monitor feature distributions and reviewed alert outcomes over time.
- **Adversarial inputs.** Monitoring can be affected by deliberately manipulated inputs. Use sensor validation, compare independent evidence sources, and route uncertain alerts for review.
- **Scalability.** Measure flow volume and scoring latency, including time spent extracting features. Explain how you would handle bursts and monitor data loss.

**Your checkpoint.** Identify a benchmark feature that might be hard to reproduce from your intended sensor. Explain how the age of NSL-KDD limits conclusions about present-day networks.

## 7. Submit your own work

- [ ] **Report document.** Include the problem and organizational context, rule-based and ML tradeoffs, dataset and split, preprocessing rationale, model and training explanation, short excerpts of your own code with actual outputs, test results, and deployment discussion.
- [ ] **Jupyter notebook.** Restart the kernel and run all cells from top to bottom. Save the executed notebook with its outputs and document dataset filenames and setup.
- [ ] **Graphs.** Include the training class distribution and test confusion matrix. Reference both figures in the report and explain what each shows.
- [ ] **GitHub.** Review the files you plan to publish and make your own first commit and push. Include the data source citation and instructions for obtaining the files.
