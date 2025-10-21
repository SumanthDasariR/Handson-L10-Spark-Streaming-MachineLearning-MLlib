# Handson-L10-Spark-Streaming-MachineLearning-MLlib

## Description
This project implements a real-time analytics pipeline for a ride-sharing platform using **Apache Spark Structured Streaming** and **MLlib**. It processes live ride data, trains machine learning models on historical data, and makes real-time predictions for trip fares and fare trends.

---

## Repository Structure

```

├── models/                  # Directory where trained models are saved
│   ├── fare_model/
│   └── fare_trend_model_v2/
├── checkpoints/             # Directory for Spark Streaming checkpoints (optional)
├── data_generator.py        # Simulates real-time ride data
├── task4.py                 # Solution for Task 4: Real-Time Fare Prediction
├── task5.py                 # Solution for Task 5: Time-Based Fare Trend Prediction
├── training-dataset.csv     # Historical data for model training
└── README.md                # Project documentation

````

---

## Task 4: Real-Time Fare Prediction

### Description
Predicts the fare for individual trips in real-time. A **Linear Regression** model is trained offline using historical data to learn the relationship between `distance_km` and `fare_amount`. The model is then used to predict fares on a live stream of ride data.

### Approach
1. **Offline Model Training**  
   - Loads `training-dataset.csv` if no pre-trained model exists.  
   - Uses a `VectorAssembler` to prepare the `distance_km` feature.  
   - Trains a `LinearRegression` model with `fare_amount` as the label.  
   - Saves the trained model to `models/fare_model`.

2. **Real-Time Inference**  
   - Connects to a socket stream to receive live ride data.  
   - Applies the same feature transformation.  
   - Predicts fare using the trained model.  
   - Computes the absolute deviation between the predicted and actual fare.

### Output
Displays in real-time:
- `trip_id`  
- `predicted_fare`  
- `deviation`  

**Results:**  
<img width="1005" height="589" alt="Screenshot 2025-10-21 121303" src="https://github.com/user-attachments/assets/0a1e0356-e719-4d0d-887e-ddb62b9b2aeb" />


---

## Task 5: Time-Based Fare Trend Prediction

### Description
Forecasts the average fare for future time windows based on the time of day. Uses advanced feature engineering to extract cyclical time-based features from aggregated time windows.

### Approach
1. **Offline Model Training**  
   - Aggregates historical data from `training-dataset.csv` into 5-minute windows.  
   - Computes `avg_fare` for each window.  
   - Engineers `hour_of_day` and `minute_of_hour` features.  
   - Trains a `LinearRegression` model on these features.  
   - Saves the model to `models/fare_trend_model_v2`.

2. **Real-Time Inference**  
   - Processes live data using a 5-minute tumbling window with 1-minute slide.  
   - Engineers the same time-based features for each window.  
   - Predicts the average fare for the window using the trained model.

### Output
Displays in real-time:
- `window_start` and `window_end`  
- `actual_avg_fare`  
- `predicted_next_avg_fare`  

**Results:**  
<img width="781" height="563" alt="Screenshot 2025-10-21 121006" src="https://github.com/user-attachments/assets/68771813-7490-41f6-8af3-f3438a675623" />


<img width="802" height="547" alt="Screenshot 2025-10-21 121018" src="https://github.com/user-attachments/assets/4a445cff-8ac0-447c-8be9-0df21687b8d5" />


---

## How to Run the Project

### 1. Start the Data Generator
Open a terminal and run:

```bash
python data_generator.py
````

This starts a local socket server streaming ride data on port `9999`.

### 2. Run a Task Script

Open another terminal.

* **Task 4: Real-Time Fare Prediction**

```bash
python task4.py
```

* **Task 5: Time-Based Fare Trend Prediction**

```bash
python task5.py
```

> The scripts will train and save models if they do not already exist, then start processing the live stream and printing predictions.

---

## Notes

* Ensure Spark and required Python packages are installed.
* The project demonstrates both offline training and real-time inference using Spark Structured Streaming and MLlib.
