# Anime Recommendation System (MyAnimeList)

This is an anime recommendation system based on **MyAnimeList**. Thus, it requires an account on this website with previously watched/selected anime in order to make recommendations.

This application aims to reduce the time needed to scroll through [myanimelist.net](https://myanimelist.net) to find an anime to watch. Instead, it provides a **machine learning–based recommendation** based on shows the user has previously watched.

## How It Works

1. The user enters their **MyAnimeList user ID**
2. The app shows **5 random anime from the top 20 generated recommendations** based on shows the user has previously watched.

---

# System Architecture

The project consists of **five major components**:

1. Crawl Scheduler  
2. Crawler  
3. Data Ingestion + ETL  
4. Machine Learning Pipelines  
5. Web App (Frontend)

---

# 1. Crawl Scheduler

A job that fetches a list of **anime/profile URLs** from a database and pushes them to a **message queue**.

**Infrastructure:**

- Crawl schedulers are deployed as **Google Cloud Functions**
- Database is a **Postgres Cloud SQL database**
- Message queue is a **Google Pub/Sub topic**

---

# 2. Crawler

**Scrapy jobs** pull message URLs from the scheduler message queue and crawl them.

The crawled data items are pushed to a **data ingestion message queue**.  
Crawler jobs also connect to the scheduler database to update it.

**Infrastructure:**

- Crawlers deployed on **Google Kubernetes Engine (GKE)**
- Data ingestion queue implemented with **Google Pub/Sub**

---

# 3. Data Ingestion + ETL

An **Apache Beam streaming job** pulls data items from the ingestion queue and pushes them into **BigQuery** as a landing area.

An **Apache Airflow pipeline** then:

- Aggregates the data
- Cleans the data
- Validates the data
- Transforms it into structured datasets

The processed datasets are saved to **BigQuery** and **Cloud Storage**.

**Infrastructure:**

- Beam job deployed as a **Google Dataflow job**
- Airflow pipeline runs in **Google Cloud Composer**

---

# 4. Machine Learning Pipelines

Each pipeline handles both **retrieval** and **ranking** stages.

## Retrieval Step

1. Generate **train / validation / test datasets**
2. Train a **retrieval model**
3. Run **batch inference**
4. Save retrieval results

## Ranking Step

1. Generate **train / validation / test datasets for ranking**
2. Train a **ranking model**
3. Run **batch inference on retrieved results**
4. Save the **final ranked recommendations**

**Infrastructure:**

- Pipelines built with **Kubeflow Pipelines**
- Executed using **Google Vertex AI Pipelines**
- Data fetched from and stored in:
  - **BigQuery**
  - **Cloud Storage**

---

# 5. Web App (Frontend)

Generated recommendations are ingested into a **Redis database**.

A lightweight **Flask web application**:

- Fetches recommendations from Redis
- Returns recommendations for each user request

---

# Notes

- The current **UI is scrappy and needs improvement**.
- Feedback and contributions are welcome.

Hope you enjoy!
