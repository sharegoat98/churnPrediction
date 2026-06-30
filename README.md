# Churn Prediction

**Project Overview**

The goal of application is to develop solutions that will identify players at risk of leaving live betting.


**EDA and Data Preprocessing**

EDA, data preprocessing and feature engineering is implamented in `churn-eda.ipynb`
Updated dataset is exported as `updated_churn_dataset.csv`. This dataset is used for training ML models.

---

**Machine Learning Part**

Training, evaluation and reserach is implemented in `training_model_research.ipynb`

Tested models:
    - Logistic Regression
    - Logistic Regression (`class_weight="balanced"`)
    - Random Forest
    - XGBoost


**Results**

| Model | Accuracy | Precision | Recall | F1 | ROC AUC |
|-------|---------:|----------:|-------:|---:|--------:|
| Logistic Regression |0.85 |0.65 |0.28 |0.32 |0.84 |
| Logistic Regression (Balanced) |0.72 |0.36 |0.82 |0.50 |0.84 |
| Random Forest |0.84 |0.57 |0.29 |0.39 |0.83 |
| XGBoost |0.84 |0.56 |0.29 |0.39 |0.83 |

---

**Conclusion**

Conclusion based on the results of the trained ML models (Logistic Regression, Random Forest, and XGBoost) for churn prediction:
- All models achieved a similar ROC AUC score, indicating that they perform similarly in ranking users according to their churn risk.
- In this case, the more complex Random Forest and XGBoost models do not provide better results because the engineered features are already highly informative, so the additional model complexity does not lead to significant performance improvements.
- Logistic Regression without the class_weight parameter produces a model with higher precision and lower recall, making it a good choice when minimizing false positives is important (e.g., when incorrectly classifying a user as churn is costly).
- When the `class_weight="balanced"` parameter is applied to Logistic Regression, the model becomes more aggressive in detecting churn. This significantly increases recall, while reducing precision and overall accuracy.
- For practical use, the best choice is Logistic Regression with class_weight="balanced" as the baseline churn prediction model, since it provides the best coverage of churning users (`lr_churn_trained_model.pkl`).
- In a real production environment, it is generally preferable not to rely on a fixed decision threshold. Instead, the classification threshold should be adjusted according to the current business objective, allowing the balance between precision and recall to be optimized for different use cases.
---

**Potential improvements for a production-ready solution:**

- Implement a Flask API through which each model can be invoked. The API would also support model training and retraining.
- Additionally, unit tests and API tests should be added to ensure reliability and correctness.
- Implement logging for every API request and the corresponding model output.
- Deploy the entire application on an AWS SageMaker environment. This would allow centralized management of model training, retraining, and inference. It would also enable more advanced logging, performance monitoring, and model tracking, making it easier to scale the application and manage different model versions in production. Furthermore, it would allow seamless model version switching without any production downtime.
- Conduct a more detailed comparison of models by tuning thresholds and hyperparameters to optimize performance for a specific business use case.
- Implement a more robust model performance tracking system such as MLflow. This would make it clear which thresholds and model configurations are most suitable for different business scenarios.
- Automate dataset updates and model retraining using Apache Airflow.

**How to Run**

1. Clone the repository.

```bash
git clone git@github.com:sharegoat98/churnPrediction.git
```

2. Install dependencies.

```bash
pip install -r requirements.txt
```

3. Run the notebook.

---

**Asnwers on additional architecture and business questions**


**3. On-Prem Architecture
Propose a conceptual architecture (tools and basic steps) for deploying the pipeline and model into production on the company's On-Premise infrastructure. How would you handle the regular updating (retraining) of the model?**

For the application itself, I would initially package it into a Docker image and store it in a storage service. I would manage the infrastructure using Terraform, while the model deployment pipeline would be implemented using Jenkins. The deployment pipeline would include building the Docker image, optionally training/retraining the model, as well as running the necessary tests to ensure that the new version is functional and stable. I would expose application through Flask API.
I would use the Pydantic library to handle potential Python cold-start issues, if this is not already managed by the server hosting the application.
Model performance tracking (including hyperparameter tuning and threshold optimization) would be handled using MLflow.
For regular, automated data collection and model retraining, I would use an Apache Airflow pipeline that gathers fresh data from designated sources and triggers model retraining based on that data.

**4. Utilizing Text Data from the Contact Center
In addition to the structured data from the CSV, the company also possesses the history of text correspondences that dissatisfied users had with our contact center. How would you leverage this text data to improve the existing Churn system? Conceptually describe how you see the application of AI / RAG systems in this scenario and what the technical steps for implementation would be.**

I would use the text correspondences with users to build a knowledge base on top of the existing structured features. These texts would be incorporated into a RAG framework, where they would be used in the retrieval stage to provide additional context and validation of the results produced by the LLM.
I would choose an appropriate Python framework for building RAG applications, or depending on the frequency of use and business needs, potentially implement a custom reusable internal framework that could be used across similar applications within the company.
Experimentally, I would determine which embedding model to use for generating vector representations of the text data, which would then be stored in a vector database. I would also enable support for multiple LLM providers for generating responses within the RAG system, allowing flexibility in model selection depending on performance, cost, or latency requirements.
The deployment pipeline would be similar to the one described in the previous question.

**5. Business Alignment
Imagine a situation where your model flags certain players as high-risk, but a business manager states in a meeting that the results do not align with their expectations from the field and doubts the model's accuracy. How would you approach this conversation and guide the communication with them?**

First, I would ask the business stakeholder to clarify what exactly does not align with their expectations and try to identify specific examples or segments where the model seems incorrect.
Based on that, I would perform a targeted error analysis and re-evaluate the model performance on those cases, checking whether the issue comes from data, features, or model assumptions.
I would also apply additional validation and, if needed, run controlled experiments to better understand the discrepancy.
After that, I would walk the stakeholder through the model’s decision process using interpretability techniques to explain how the model arrives at its predictions.
Finally, we would combine domain expertise and model insights to agree on the best course of action.

**6. Truth Verification
Using data and analytics, how would you check whether the business manager is right or if your model made an error? What experiment or metric would you propose to the business team to evaluate the situation based on facts rather than subjective feelings?**

I would first define a clear ground truth, such as churn within a fixed time horizon. Then I would evaluate the model on historical data using appropriate metrics like precision, recall, and ROC-AUC, with a particular focus on the specific segment where the disagreement occurs.
Next, would perform a detailed error analysis on the specific users where the business manager disagrees with the model to determine whether there is a systematic pattern, such as certain customer segments or missing features.
To make the evaluation business-relevant, I would translate model performance into cost-based metrics, considering the impact of false positives and false negatives.

**Author**
Aleksa Sarsanski