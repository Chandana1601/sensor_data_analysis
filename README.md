# sensor_data_analysis
This Python script connects to a MySQL database (sensor_data) and analyzes sensor readings from the sensor_data_analysis table. It performs three main tasks:  Calculates statistical averages for proximity, ambient light, microphone, and WiFi signal strength. The script prints the results of each analysis to the console, helping you
import mysql.connector

mydb = mysql.connector.connect(
    host='************',
    user='root',
    password='************',
    database='sensor_data'
)

cursor = mydb.cursor()

# Query 1: Statistical Averages
cursor.execute("""
    SELECT
        AVG(proximity) AS avg_proximity,
        AVG(ambient_light) AS avg_ambient_light,
        AVG(microphone) AS avg_microphone,
        AVG(wifi_signal_strength) AS avg_wifi_signal_strength
    FROM sensor_data_analysis;
""")
print("Statistical Averages:", cursor.fetchone())

# Query 2: View all sensor readings ordered by proximity
cursor.execute("""
    SELECT
        touch, proximity, ambient_light, microphone, camera, bluetooth_status, usb_status, wifi_status, bluetooth_devices, usb_devices, wifi_signal_strength
    FROM sensor_data_analysis
    ORDER BY proximity;
""")
print("All Sensor Readings:")
for row in cursor.fetchall():
    print(row)

# Query 3: Detect proximity outliers (anomaly detection)
cursor.execute("""
    SELECT *
    FROM sensor_data_analysis
    WHERE proximity > (SELECT AVG(proximity) + 2 * STDDEV(proximity) FROM sensor_data_analysis)
       OR proximity < (SELECT AVG(proximity) - 2 * STDDEV(proximity) FROM sensor_data_analysis);
""")
print("Proximity Outliers:")
for row in cursor.fetchall():
    print(row)

cursor.close()
mydb.close()
