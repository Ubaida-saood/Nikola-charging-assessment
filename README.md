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
