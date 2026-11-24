# Machine learning intrusion detection on NSL-KDD

## Problem

Enterprise security analysts need to identify suspicious connections in a stream of routine traffic. A fixed byte threshold can flag a legitimate backup and miss a smaller malicious connection. Rules remain useful for known signatures and explicit policies. A supervised classifier can combine multiple features learned from labeled examples. The classifier in this experiment assigns a normal or malicious label to each connection summary. An analyst would investigate connections flagged as malicious.

## Data and preprocessing

NSL-KDD provides 125,973 training rows and 22,544 test rows. The training split contains 67,343 normal and 58,630 malicious connections. The notebook found zero duplicate full rows and no missing or non-finite values, so it kept the supplied partitions without imputation or row removal. It removed the target label and the benchmark difficulty score from the inputs and kept all 41 measured features. It mapped normal to 0 and every attack category to 1.

Protocol, service, and flag are unordered categories, so the notebook one-hot encoded them. It standardized the remaining features to make their scales comparable during fitting. It learned the encoding and scaling exclusively on the training split. [Dataset and paper attribution](../README.md#dataset-attribution)

![Training class distribution](figures/training-class-distribution.png)

Normal is the majority training class, which explains why the baseline always predicts normal.

## Models

The baseline predicts the most frequent training class. Logistic regression learns a weighted combination of the transformed features. Its sigmoid converts that score to a probability estimate. It was selected as a compact, interpretable starting point for binary tabular classification.

The scikit-learn model was fit once using the supplied training split. The CPU PyTorch comparison uses the same training-fitted transformations and a single linear output trained for ten epochs with binary cross-entropy and AdamW. Both classify at a probability cutoff of 0.5. [Executed notebook](../train.ipynb)

```python
logistic.fit(X_train, y_train)
logistic_predictions = logistic.predict(X_test)
matrix = confusion_matrix(y_test, logistic_predictions, labels=[0, 1])
```

The scikit-learn solver used 159 iterations. PyTorch mean training loss fell from 0.1840 in epoch one to 0.0762 in epoch ten.

## Test results

| Model | Accuracy | Precision | Recall | F1 |
| --- | ---: | ---: | ---: | ---: |
| Majority baseline | 43.08% | 0.00% | 0.00% | 0.00% |
| Scikit-learn logistic regression | 75.37% | 91.75% | 62.33% | 74.23% |
| PyTorch linear classifier | 75.23% | 91.80% | 62.03% | 74.03% |

The majority baseline issued zero alerts, so its precision is reported as zero by convention. Scikit-learn correctly flagged 7,999 malicious connections and generated 719 false positives. The false positives would consume analyst time. The 4,834 false negatives represent malicious connections that would receive no alert from this classifier.

![Scikit-learn test confusion matrix](figures/test-confusion-matrix.png)

Recall is 7,999 divided by the 12,833 malicious test connections. Precision uses the 8,718 flagged connections as its denominator. The two linear models achieved similar scores. The observed CPU times were 4.13 seconds for scikit-learn fitting with preprocessing and 4.99 seconds for PyTorch training after preprocessing. These timings cover different work and describe one run.

## Deployment and limitations

A network sensor would compute connection summaries and hand them to the scoring service. The service would send alerts and supporting fields to a SIEM, where analysts can review the evidence. Start in observation mode and measure alert quality before using a firewall to act on reviewed alerts. Confirm that production instrumentation can actually compute the training features. A flow-only sensor, for example, may lack login outcome information.

New applications and network migrations can cause concept drift. Track feature distributions and analyst-reviewed outcomes. Validate sensor inputs and corroborate uncertain alerts across independent sources to reduce risk from intentionally manipulated inputs. For scalability, measure feature extraction and scoring latency under peak throughput and track queues and dropped records.

The historical NSL-KDD split supports a reproducible model comparison. Production adoption would require evaluation on current, representative traffic, including how often malicious connections occur and how many alerts analysts can review.
