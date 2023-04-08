# Nikola-charging-assessment

Create the weather data table in RDS MySQL
To create the weather data table in MySQL, you can use the following SQL code:

CREATE TABLE weather_data (
    city_name VARCHAR(255) PRIMARY KEY,
    temperature FLOAT,
    humidity FLOAT,
    pressure FLOAT,
    wind_speed FLOAT
);

This creates a table with five columns: city_name (which is the primary key), temperature, humidity, pressure, and wind_speed.

Flask routes and functions
Here are some sample Flask routes and functions that you can use to handle weather data queries and modifications:

from flask import Flask, request, jsonify
import mysql.connector

app = Flask(__name__)

# Connect to the MySQL database
db = mysql.connector.connect(
    host="your-rds-mysql-endpoint",
    user="your-rds-mysql-username",
    password="your-rds-mysql-password",
    database="your-rds-mysql-database"
)

# Route to search for weather data by city name
@app.route('/weather_data/<city_name>', methods=['GET'])
def get_weather_data(city_name):
    cursor = db.cursor()
    cursor.execute("SELECT * FROM weather_data WHERE city_name = %s", (city_name,))
    result = cursor.fetchone()
    if result is None:
        return jsonify({'error': 'City not found'})
    else:
        return jsonify({'city_name': result[0], 'temperature': result[1], 'humidity': result[2], 'pressure': result[3], 'wind_speed': result[4]})

# Route to add new weather data to the database
@app.route('/weather_data', methods=['POST'])
def add_weather_data():
    data = request.get_json()
    cursor = db.cursor()
    try:
        cursor.execute("INSERT INTO weather_data (city_name, temperature, humidity, pressure, wind_speed) VALUES (%s, %s, %s, %s, %s)", (data['city_name'], data['temperature'], data['humidity'], data['pressure'], data['wind_speed']))
        db.commit()
        return jsonify({'message': 'Weather data added successfully'})
    except:
        db.rollback()
        return jsonify({'error': 'Failed to add weather data'})

# Route to update weather data in the database
@app.route('/weather_data/<city_name>', methods=['PUT'])
def update_weather_data(city_name):
    data = request.get_json()
    cursor = db.cursor()
    try:
        cursor.execute("UPDATE weather_data SET temperature=%s, humidity=%s, pressure=%s, wind_speed=%s WHERE city_name=%s", (data['temperature'], data['humidity'], data['pressure'], data['wind_speed'], city_name))
        db.commit()
        return jsonify({'message': 'Weather data updated successfully'})
    except:
        db.rollback()
        return jsonify({'error': 'Failed to update weather data'})

# Route to delete weather data from the database
@app.route('/weather_data/<city_name>', methods=['DELETE'])
def delete_weather_data(city_name):
    cursor = db.cursor()
    try:
        cursor.execute("DELETE FROM weather_data WHERE city_name = %s", (city_name,))
        db.commit()
        return jsonify({'message': 'Weather data deleted successfully'})
    except:
        db.rollback()
        return jsonify({'error': 'Failed to delete weather data'})

if __name__ == '__main__':
    app.run(debug=True)
