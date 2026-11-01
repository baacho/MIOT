**connection**

DHT11 pin	Raspberry Pi 4B

VCC (+)	3.3V – Physical Pin 1

DATA (OUT)	GPIO 4 – Physical Pin 7

GND (-)	GND – Physical Pin 6







**5. Install the DHT11 Python libraries**

We'll use the Adafruit CircuitPython DHT library. It supports DHT11 and DHT22 sensors on Raspberry Pi. 

GitHub+1





**Open your Raspberry Pi terminal.**



***Step 1: Update Raspberry Pi***

sudo apt update

sudo apt upgrade -y





***Step 2: Install dependencies***

sudo apt install -y python3-venv libgpiod-dev





***Step 3: Create a project folder***

mkdir \~/dht11\_project

cd \~/dht11\_project





***Step 4: Create a Python virtual environment***



This is recommended on newer Raspberry Pi OS versions.

python3 -m venv --system-site-packages venv





***Step 5: Activate the environment***

source venv/bin/activate





Your terminal should now show (venv).



***Step 6: Install the libraries***

pip install adafruit-circuitpython-dht

pip install paho-mqtt









**code for testing**



import time

import board

import adafruit\_dht



\# Initialize DHT11 on GPIO4

dht = adafruit\_dht.DHT11(

&#x20;   board.D4,

&#x20;   use\_pulseio=False

)



try:

&#x20;   while True:

&#x20;       try:

&#x20;           temperature = dht.temperature

&#x20;           humidity = dht.humidity



&#x20;           print("Temperature:", temperature, "°C")

&#x20;           print("Humidity:", humidity, "%")

&#x20;           print("----------------------")



&#x20;       except RuntimeError as error:

&#x20;           print("Reading error:", error)



&#x20;       time.sleep(2)



except KeyboardInterrupt:

&#x20;   print("Program stopped")



finally:

&#x20;   dht.exit()







* **Create a new Python file:**

nano dht11\_mqtt.py





###### **Paste this complete code (main):**



import time

import board

import adafruit\_dht

from paho.mqtt import publish



\# --------------------------------

\# DHT11 SENSOR CONFIGURATION

\# --------------------------------



dht = adafruit\_dht.DHT11(

&#x20;   board.D4,

&#x20;   use\_pulseio=False

)



\# --------------------------------

\# THINGSPEAK MQTT CONFIGURATION

\# --------------------------------



CHANNEL\_ID = "YOUR\_CHANNEL\_ID"



MQTT\_CLIENT\_ID = "YOUR\_MQTT\_CLIENT\_ID"

MQTT\_USERNAME = "YOUR\_MQTT\_USERNAME"

MQTT\_PASSWORD = "YOUR\_MQTT\_PASSWORD"



MQTT\_HOST = "mqtt3.thingspeak.com"

MQTT\_PORT = 1883



TOPIC = f"channels/{CHANNEL\_ID}/publish"



\# --------------------------------

\# MAIN PROGRAM

\# --------------------------------



try:



&#x20;   print("DHT11 MQTT Project Started")

&#x20;   print("Connecting to ThingSpeak...")



&#x20;   while True:



&#x20;       try:



&#x20;           # Read sensor values

&#x20;           temperature = dht.temperature

&#x20;           humidity = dht.humidity



&#x20;           print("--------------------------------")

&#x20;           print("Temperature:", temperature, "°C")

&#x20;           print("Humidity:", humidity, "%")



&#x20;           # Prepare MQTT message

&#x20;           payload = (

&#x20;               f"field1={temperature}"

&#x20;               f"\&field2={humidity}"

&#x20;           )



&#x20;           # Publish data to ThingSpeak

&#x20;           publish.single(

&#x20;               TOPIC,

&#x20;               payload,

&#x20;               hostname=MQTT\_HOST,

&#x20;               port=MQTT\_PORT,

&#x20;               client\_id=MQTT\_CLIENT\_ID,

&#x20;               auth={

&#x20;                   "username": MQTT\_USERNAME,

&#x20;                   "password": MQTT\_PASSWORD

&#x20;               },

&#x20;               qos=0

&#x20;           )



&#x20;           print("Data sent to ThingSpeak successfully!")



&#x20;       except RuntimeError as error:



&#x20;           # DHT11 sometimes produces temporary read errors

&#x20;           print("Sensor reading error:", error)



&#x20;       except Exception as error:



&#x20;           print("MQTT or connection error:", error)



&#x20;       # ThingSpeak free channels require an interval

&#x20;       # of at least 15 seconds between updates.

&#x20;       time.sleep(20)



except KeyboardInterrupt:



&#x20;   print("\\nProgram stopped by user.")



finally:



&#x20;   dht.exit()

&#x20;   print("Sensor GPIO released.")









**10. Run the complete experiment**

Make sure your virtual environment is activated.



source \~/dht11\_project/venv/bin/activate





**Navigate to your project:**



cd \~/dht11\_project





**Run:**



python dht11\_mqtt.py	

