# Real-Time-Environment-Monitoring-System
Develop a system to monitor air quality in a city in real-time, Air quality sensors  are deployed across the city, continuously measuring pollutants.  

Flask – Real-Time API Server
Building lightweight REST API endpoints to serve sensor data to clients (web, dashboards).

from flask import Flask, jsonify, request
app = Flask(__name__)

sensor_data = []

@app.route('/submit_data', methods=['POST'])
def submit_data():
    data = request.json
    sensor_data.append(data)
    return jsonify({"status": "success", "data": data})

@app.route('/get_data')
def get_data():
    return jsonify(sensor_data)

if __name__ == '__main__':
    app.run(debug=True)

PostgreSQL + PostGIS – Sensor Data Storage with Spatial Support
Store real-time geolocated environmental sensor data

CREATE EXTENSION postgis;
CREATE TABLE env_data (
    id SERIAL PRIMARY KEY,
    timestamp TIMESTAMP,
    temperature FLOAT,
    humidity FLOAT,
    geom GEOGRAPHY(Point, 4326)
);

INSERT INTO env_data (timestamp, temperature, humidity, geom)
VALUES (NOW(), 31.2, 74.6, ST_GeogFromText('SRID=4326;POINT(72.856 19.076)'));

NumPy – Real-Time Statistical Analysis
Compute environmental statistics, trend detection, and filtering

import numpy as np

# Simulated real-time temp readings
temps = np.array([28.5, 29.1, 28.9, 30.2, 31.0])
mean_temp = np.mean(temps)
print("Mean Temperature:", mean_temp)

GeoPandas + Shapely – Spatial Processing
Spatial filtering, buffer zones around pollution sources, merging with shapefiles.

import geopandas as gpd
from shapely.geometry import Point

point = Point(77.2, 28.6)
gdf = gpd.GeoDataFrame([{'temperature': 33.2, 'geometry': point}], crs="EPSG:4326")

# Buffer for pollution impact area
gdf['buffer'] = gdf.geometry.buffer(0.01)
print(gdf)

TensorFlow – Anomaly Detection Model
Detect environmental anomalies (e.g., sudden rise in CO₂, noise, etc.)

import tensorflow as tf
import numpy as np

model = tf.keras.Sequential([
    tf.keras.layers.Dense(64, activation='relu', input_shape=(3,)),
    tf.keras.layers.Dense(1)
])

model.compile(optimizer='adam', loss='mse')

# Simulated sensor data: [temperature, humidity, AQI]
X = np.array([[30.5, 70, 85], [29.1, 68, 90], [35, 60, 300]])
y = np.array([0, 0, 1])  # 1 = anomaly

model.fit(X, y, epochs=10)

ArcGIS API for Python – Publish, Query & Monitor Layers
Push data to ArcGIS Online, query FeatureLayers, automate workflows

from arcgis.gis import GIS
from arcgis.features import FeatureLayer

gis = GIS("https://www.arcgis.com", "your_username", "your_password")
fl = FeatureLayer('https://services.arcgis.com/yourlayer/FeatureServer/0')

new_feature = {
    "attributes": {"temperature": 31.0},
    "geometry": {"x": 72.8, "y": 19.07}
}

fl.edit_features(adds=[new_feature])

