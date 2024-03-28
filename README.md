## IoT cloud-based system by Gitika Tirumishi Jada (SUID: 832570194)

import network
import time
import urandom
from umqtt.simple import MQTTClient as mq

# MQTT broker details
client_id = "UniqueID1234"
user = "User1234"
password = "SecretPass5678"
server = "mqtt.example.com"
port = 1883
topic_temp = "channels/123456/publish/fields/field1"
topic_hmdty = "channels/123456/publish/fields/field2"
topic_co2 = "channels/123456/publish/fields/field3"

# Wi-Fi credentials
WIFI_SSID = "MyWifiNetwork"
WIFI_PASSWORD = "MySecretPassword"

# Data storage
data_history = []


# Function to generate sensor data
def generate_data():
    temp = urandom.uniform(-50, 50)
    hmdty = urandom.uniform(0, 100)
    co2 = urandom.uniform(300, 2000)
    return temp, hmdty, co2


# Function to publish data
def publish_data(temp, hmdty, co2):
    client = mq(client_id, server, user=user, password=password)
    client.connect()
    client.publish(topic_temp, str(temp))
    client.publish(topic_hmdty, str(hmdty))
    client.publish(topic_co2, str(co2))
    client.disconnect()


# Connect to Wi-Fi
wifi_interface = network.WLAN(network.STA_IF)
wifi_interface.active(True)
wifi_interface.connect(WIFI_SSID, WIFI_PASSWORD)

# Wait for Wi-Fi connection
while not wifi_interface.isconnected():
    pass

print("Connected to Wi-Fi")

# Generate and publish sensor data
while True:
    temp, hmdty, co2 = generate_data()
    data_history.append((temp, hmdty, co2))

  # Remove oldest data point if exceeds 5 hours
  if len(data_history) > 720:
        data_history.pop(0)

  publish_data(temp, hmdty, co2)
    print("Published: Temperature={:.2f}C, Humidity={:.2f}%, CO2={:.2f}ppm".format(temp, hmdty, co2))
    time.sleep(5)
