# 🎬 Production-Ready Movie Recommendation System  
*Inglorious Masters – CMU Machine Learning in Production Capstone (Spring 2025)*

This project presents a full-stack movie recommendation system built as part of CMU’s Machine Learning in Production course. The system simulates real-world constraints of machine learning deployment — including data ingestion, model training, operational monitoring, experimentation, fairness, and feedback loops.

> **Note:** The code and architecture are currently proprietary but may be open-sourced in the future.

---

## 🧠 Overview

We developed a movie recommendation engine using collaborative filtering models trained on real-time user interaction data. The pipeline integrates continuous data ingestion, model retraining, telemetry, and fairness monitoring, all within a robust and containerized infrastructure.

---

## 📈 Model Development & Evaluation

- **Data Source:** User-movie rating data streamed via Kafka and enriched with user demographics and movie metadata.
- **Models Evaluated:**  
  - Singular Value Decomposition (SVD)  
  - Non-Negative Matrix Factorization (NMF)  
  - k-Nearest Neighbors (kNN)  
- **Chosen Model:** SVD, due to lowest RMSE (0.8948 offline; 0.6535 A/B test), fastest training, and low inference cost.

### 🧪 Offline Evaluation

- Performed time-based train-test split to simulate real-world deployment conditions.
- Evaluation metric: **Root Mean Squared Error (RMSE)**.
- Final offline RMSE: **0.71068**.

### 📡 Online Evaluation

- Real-time user ratings were streamed and evaluated in batches of 1,000.
- Continuous evaluation loop using RMSE to track predictive quality.
- A/B testing between SVD and SVD++ showed SVD performing better with **statistically significant difference (p = 0.0069)**.

---

## 🧪 Experimentation & Deployment

- **A/B Testing**: Deterministic user-based routing (odd/even user ID split) to compare model variants.
- **Statistical Testing**: Two-sample t-test used to confirm performance deltas.
- **Retraining Schedule**: Models retrained every 48 hours using latest ratings from a Kafka-backed Postgres store.

---

## 📦 Infrastructure & Monitoring

- Fully containerized deployment using Docker Compose.
- Real-time logging and metrics via Prometheus and Grafana.
- Metrics monitored:
  - Model accuracy (RMSE)
  - Service availability and request success rate
  - Average request latency
- Alerting for SLA violations (e.g., availability < 70%).

---

## 🔍 Fairness & Feedback Loops

### Fairness Audits

- Identified **age and gender** as protected attributes.
- Implemented guardrails to:
  - Prevent recommending age-inappropriate content (e.g., R-rated movies to minors).
  - Ensure genre diversity across recommendations.

- Measured fairness with metrics like:
  - **Number of unique genres** per recommendation (target ≥ 10)
  - **Violations detected**: 18/14,712 recommendations

### Feedback Loop Mitigation

- Identified and addressed two key loops:
  1. **Popularity Reinforcement**: Dominance of a few movies in recommendation output.
  2. **Rating Skew**: Inflated feedback from safe, high-confidence recommendations.

- Monitored catalog coverage and rating distribution to detect and address loop formation.

---

## 🔐 Security Analysis

- Threat modeling performed using STRIDE and DREAD frameworks.
- Identified and mitigated key risks:
  - **Model-extraction burst**: Throttled requests per IP; added noise to scores.
  - **Rating-poison spike**: JWT-authenticated rating API; daily cap on training data per movie.

- Verified via logs and telemetry that no active exploits occurred during the sampled deployment window.

---

## ✅ Summary

This project demonstrates the development and deployment of a production-grade recommendation system with real-time data ingestion, model retraining, robust experimentation, and responsible AI practices. Emphasis was placed on fairness, system monitoring, and feedback loop awareness — all deployed in a continuously integrated, containerized environment.

> *Developed by: bcurtin2, akandath, hyungwol, ml6 – Carnegie Mellon University*
