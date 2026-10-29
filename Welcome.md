

---

## 1. Display Single LED Blinking Pattern

### Hardware Connection
| LED Pin | Raspberry Pi Pin | Function |
| :--- | :--- | :--- |
| Anode (+) | Physical Pin 15 | GPIO 22 |
| Cathode (-) | Physical Pin 9 | GND |

### Code: `led_single.py`
```python
import RPi.GPIO as GPIO
import time

# Set GPIO numbering mode to Physical Board Numbers
GPIO.setmode(GPIO.BOARD)

# Setup pin 15 as output
LED_PIN = 15
GPIO.setup(LED_PIN, GPIO.OUT)

try:
    for i in range(5):
        GPIO.output(LED_PIN, True)
        print("LED Status: ON")
        time.sleep(1)

        GPIO.output(LED_PIN, False)
        print("LED Status: OFF")
        time.sleep(1)

    print("Execution Completed.")
finally:
    GPIO.cleanup()
```

---

## 2. Display Three LED Blinking Pattern

### Hardware Connection
| Component | Raspberry Pi Pin | Function |
| :--- | :--- | :--- |
| LED 1 Anode | Physical Pin 11 | GPIO 17 |
| LED 2 Anode | Physical Pin 13 | GPIO 27 |
| LED 3 Anode | Physical Pin 15 | GPIO 22 |
| Cathodes (-) | Physical Pin 9 | GND |

### Code: `led_three.py`
```python
import RPi.GPIO as GPIO
import time

GPIO.setmode(GPIO.BOARD)

PINS = [11, 13, 15]
for pin in PINS:
    GPIO.setup(pin, GPIO.OUT)

try:
    for i in range(5):
        # LED 1 Sequence
        GPIO.output(11, True)
        print("LED 1 ON")
        time.sleep(0.5)
        GPIO.output(11, False)
        print("LED 1 OFF")

        # LED 2 Sequence
        GPIO.output(13, True)
        print("LED 2 ON")
        time.sleep(0.5)
        GPIO.output(13, False)
        print("LED 2 OFF")

        # LED 3 Sequence
        GPIO.output(15, True)
        print("LED 3 ON")
        time.sleep(0.5)
        GPIO.output(15, False)
        print("LED 3 OFF")

    print("Done")
finally:
    GPIO.cleanup()
```

---

## 3. Display Four LED Blinking Pattern

### Hardware Connection
| Component | Raspberry Pi Pin | Function |
| :--- | :--- | :--- |
| LED 1 Anode | Physical Pin 11 | GPIO 17 |
| LED 2 Anode | Physical Pin 13 | GPIO 27 |
| LED 3 Anode | Physical Pin 15 | GPIO 22 |
| LED 4 Anode | Physical Pin 16 | GPIO 23 |
| Cathodes (-) | Physical Pin 9 | GND |

### Code: `led_four.py`
```python
import RPi.GPIO as GPIO
import time

GPIO.setmode(GPIO.BOARD)

PINS = [11, 13, 15, 16]
for pin in PINS:
    GPIO.setup(pin, GPIO.OUT)

try:
    for i in range(5):
        GPIO.output(11, True)
        print("LED 1 ON")
        time.sleep(0.3)
        GPIO.output(11, False)
        print("LED 1 OFF")

        GPIO.output(13, True)
        print("LED 2 ON")
        time.sleep(0.3)
        GPIO.output(13, False)
        print("LED 2 OFF")

        GPIO.output(15, True)
        print("LED 3 ON")
        time.sleep(0.3)
        GPIO.output(15, False)
        print("LED 3 OFF")

        GPIO.output(16, True)
        print("LED 4 ON")
        time.sleep(0.3)
        GPIO.output(16, False)
        print("LED 4 OFF")

    print("Done")
finally:
    GPIO.cleanup()
```

---

## 4. Display Time over 4-Digit 7-Segment Display

### Hardware Connection (TM1637 Display)
| TM1637 Pin | Raspberry Pi Pin | Raspberry Function |
| :--- | :--- | :--- |
| GND | Physical Pin 14 | Ground |
| VCC | Physical Pin 4 | 5V Power |
| DIO | Physical Pin 18 | GPIO 24 |
| CLK | Physical Pin 16 | GPIO 23 |

### Dependencies
Download the driver script first:
```bash
wget https://raspberrytips.nl/files/tm1637.py
```

### Code: `clock.py`
```python
import sys
import time
import datetime
import RPi.GPIO as GPIO
import tm1637

# Initialize TM1637 Display (CLK=GPIO23, DIO=GPIO24)
Display = tm1637.TM1637(23, 24, tm1637.BRIGHT_TYPICAL)
Display.Clear()
Display.SetBrightness(1)

try:
    while True:
        now = datetime.datetime.now()
        hour = now.hour
        minute = now.minute
        second = now.second

        # Prepare 4-digit list format [H1, H2, M1, M2]
        currenttime = [int(hour / 10), hour % 10, int(minute / 10), minute % 10]

        Display.Show(currenttime)
        # Toggle center colon every second
        Display.ShowDoublepoint(second % 2)
        time.sleep(1)
except KeyboardInterrupt:
    print("\nProgram Interrupted.")
    Display.Clear()
```

---

## 5. Raspberry Pi Based Oscilloscope (ADS1115 / ADS1015)

### Hardware Connection
| ADS1115 Pin | Raspberry Pi Pin | Function |
| :--- | :--- | :--- |
| VDD | Physical Pin 1 | 3.3V Power |
| GND | Physical Pin 9 | Ground |
| SCL | Physical Pin 5 | GPIO 3 (SCL) |
| SDA | Physical Pin 3 | GPIO 2 (SDA) |

### Requirements
```bash
sudo pip3 install adafruit-circuitpython-ads1x15
sudo pip3 install adafruit-blinka
```

### Code: `ads1x15_simpletest.py`
```python
# SPDX-FileCopyrightText: 2021 ladyada for Adafruit Industries
# SPDX-License-Identifier: MIT
import time
import board
import busio
import adafruit_ads1x15.ads1015 as ADS
from adafruit_ads1x15.analog_in import AnalogIn

# Create the I2C bus
i2c = busio.I2C(board.SCL, board.SDA)

# Create the ADC object using the I2C bus
ads = ADS.ADS1015(i2c)

# Create single-ended input on channel 0 (P0)
chan = AnalogIn(ads, ADS.P0)

print("{:>5}\t{:>5}".format("raw", "v"))

try:
    while True:
        print("{:>5}\t{:>5.3f}".format(chan.value, chan.voltage))
        time.sleep(0.5)
except KeyboardInterrupt:
    print("\nExiting Oscilloscope Mode.")
```

---

## 6. Controlling Raspberry Pi with Telegram App

### Setup Instructions
1. Install `telepot` on Raspberry Pi:
   ```bash
   sudo pip install telepot
   ```
2. Open Telegram, search for `@BotFather`, create a new bot using `/newbot`, and retrieve your **API Token**.

### Code: `telegramled.py`
```python
import time
import datetime
import RPi.GPIO as GPIO
import telepot
import sys

LED_PIN = 11

def on(pin):
    GPIO.output(pin, GPIO.HIGH)

def off(pin):
    GPIO.output(pin, GPIO.LOW)

GPIO.setmode(GPIO.BOARD)
GPIO.setwarnings(False)
GPIO.setup(LED_PIN, GPIO.OUT)

def handle(msg):
    chat_id = msg['chat']['id']
    command = msg['text'].strip().lower()
    print('Got command: %s' % command)

    if command == 'on':
        on(LED_PIN)
        bot.sendMessage(chat_id, "LED turned ON")
    elif command == 'off':
        off(LED_PIN)
        bot.sendMessage(chat_id, "LED turned OFF")
    else:
        bot.sendMessage(chat_id, "Invalid Command! Send 'on' or 'off'.")

# Insert your actual Bot Token below
BOT_TOKEN = '5329577491:AAHHKMSOXTK-H2DMAVMwl69hoN9vWXVGKfk'

bot = telepot.Bot(BOT_TOKEN)
bot.message_loop(handle)
print("Listening for Telegram commands...")

while True:
    try:
        time.sleep(10)
    except KeyboardInterrupt:
        print("\nProgram Interrupted.")
        GPIO.cleanup()
        sys.exit()
    except Exception as e:
        print("Error:", e)
        GPIO.cleanup()
```

---

## 7. Fingerprint Sensor Interfacing

### Hardware Connection (R307 Module + USB Serial Adapter)
| Fingerprint Sensor Pin | USB Serial Converter Pin |
| :--- | :--- |
| 5V | 5V |
| GND | GND |
| TXD | RXD |
| RXD | TXD |

### Package Installation Commands
```bash
sudo bash
wget -O - http://apt.pm-codeworks.de/pm-codeworks.de.gpg | apt-key add -
wget http://apt.pm-codeworks.de/pm-codeworks.list -P /etc/apt/sources.list.d/
apt-get update
apt-get install python-fingerprint
exit
```

### Code 1: Enroll Fingerprint (`fingerprint_enroll.py`)
```python
import time
from pyfingerprint.pyfingerprint import PyFingerprint

try:
    # Initialize fingerprint sensor on UART/USB-Serial port
    f = PyFingerprint('/dev/ttyUSB0', 57600, 0xFFFFFFFF, 0x0)

    if not f.verifyPassword():
        raise Exception('Fingerprint sensor password incorrect!')

    print('Currently used templates: ' + str(f.getTemplateCount()) + '/' + str(f.getStorageCapacity()))
    print('Place finger on sensor to enroll...')

    # Wait for finger press
    while not f.readImage():
        time.sleep(0.1)

    f.convertImage(0x01)
    result = f.searchTemplate()

    if result[0] >= 0:
        print('Fingerprint already exists at position #' + str(result[0]))
        exit(0)

    print('Remove finger...')
    time.sleep(2)

    print('Place same finger again for verification...')
    while not f.readImage():
        time.sleep(0.1)

    f.convertImage(0x02)

    if f.compareCharacteristics() == 0:
        raise Exception('Fingers do not match!')

    f.createTemplate()
    positionNumber = f.storeTemplate()
    print('Fingerprint enrolled successfully at position #' + str(positionNumber))

except Exception as e:
    print('Enrollment failed: ' + str(e))
```

### Code 2: Search Fingerprint (`fingerprint_search.py`)
```python
import time
from pyfingerprint.pyfingerprint import PyFingerprint

try:
    # Initialize sensor
    f = PyFingerprint('/dev/ttyUSB0', 57600, 0xFFFFFFFF, 0x0)

    if not f.verifyPassword():
        raise Exception('Fingerprint sensor password incorrect!')

    print('Waiting for finger...')

    # Read image from sensor
    while not f.readImage():
        time.sleep(0.1)

    f.convertImage(0x01)
    result = f.searchTemplate()
    positionNumber = result[0]
    accuracyScore = result[1]

    if positionNumber == -1:
        print('No match found!')
    else:
        print('Found template at position #' + str(positionNumber))
        print('Accuracy score: ' + str(accuracyScore))

except Exception as e:
    print('Operation failed: ' + str(e))
```

---

## 8. Interfacing GPS Module

### Hardware Connection (NEO-6M GPS)
| GPS Module Pin | Raspberry Pi Physical Pin | Raspberry Function |
| :--- | :--- | :--- |
| VCC | Pin 2 | 3.3V Power |
| GND | Pin 6 | Ground |
| TXD | Pin 10 | GPIO 15 (UART_RXD0) |
| RXD | Not Required | GPIO 14 (UART_TXD0) |

### System Configuration
1. Edit config file:
   ```bash
   sudo nano /boot/config.txt
   ```
   Add lines:
   ```text
   enable_uart=1
   dtoverlay=pi3-disable-bt
   ```
2. Stop system serial console services:
   ```bash
   sudo systemctl stop serial-getty@ttyS0.service
   sudo systemctl disable serial-getty@ttyS0.service
   sudo systemctl enable serial-getty@ttyAMA0.service
   ```
3. Reboot:
   ```bash
   sudo reboot
   ```

### Code: Parse NMEA GPS Data (`gps_reader.py`)
```python
import serial
import time
import sys

# Define serial port for UART communication
SERIAL_PORT = "/dev/ttyAMA0"
BAUD_RATE = 9600

def parse_gpgga(nmea_sentence):
    """Parse $GPGGA NMEA sentence to extract latitude and longitude."""
    parts = nmea_sentence.split(',')
    if len(parts) >= 6 and parts[2] and parts[4]:
        time_utc = parts[1]
        lat = parts[2]
        lat_dir = parts[3]
        lon = parts[4]
        lon_dir = parts[5]
        
        print("-" * 40)
        print(f"UTC Time : {time_utc[:2]}:{time_utc[2:4]}:{time_utc[4:6]}")
        print(f"Latitude : {lat} {lat_dir}")
        print(f"Longitude: {lon} {lon_dir}")
        print("-" * 40)

def main():
    try:
        ser = serial.Serial(SERIAL_PORT, baudrate=BAUD_RATE, timeout=1)
        print("Reading data from GPS Module... Press Ctrl+C to stop.")
        
        while True:
            line = ser.readline().decode('utf-8', errors='ignore').strip()
            if line.startswith('$GPGGA'):
                parse_gpgga(line)

    except KeyboardInterrupt:
        print("\nExiting GPS reader.")
        ser.close()
        sys.exit()
    except Exception as e:
        print(f"Serial Error: {e}")

if __name__ == "__main__":
    main()
```

---

## 9. Interfacing Pi Camera

### Setup Instructions
1. Enable camera interface using system configuration tool:
   ```bash
   sudo raspi-config
   ```
   *(Navigate to Interfacing Options $\rightarrow$ Camera $\rightarrow$ Enable, then reboot)*.
2. Install Python Camera library:
   ```bash
   sudo apt-get install python3-picamera
   ```

### Code 1: Capture Photo (`capture_image.py`)
```python
import time
import picamera

# Initialize Pi Camera
camera = picamera.PiCamera()

# Configure camera settings
camera.resolution = (1024, 768)

# Start live preview window
camera.start_preview()
time.sleep(2)  # Warm-up delay

# Capture photo
camera.capture('test.jpg')
camera.stop_preview()

print("Photo captured successfully as test.jpg")
```

### Code 2: Record Video (`record_video.py`)
```python
import time
import picamera

# Initialize Pi Camera
camera = picamera.PiCamera()

# Configure resolution
camera.resolution = (640, 480)

# Start preview window
camera.start_preview()
time.sleep(2)

# Start recording video file (.h264 format)
print("Recording video for 5 seconds...")
camera.start_recording('video.h264')

# Record for 5 seconds
camera.wait_recording(5)

# Stop recording and close preview
camera.stop_recording()
camera.stop_preview()

print("Video recorded successfully as video.h264")
```

---

## 10. Interfacing RFID Module (Read & Write)

### Hardware Connection (RC522 RFID)
| RC522 Pin | Raspberry Physical Pin | Raspberry Function |
| :--- | :--- | :--- |
| SDA | Pin 24 | GPIO 8 (SPI_CE0_N) |
| SCK | Pin 23 | GPIO 11 (SPI0_SCLK) |
| MOSI | Pin 19 | GPIO 10 (SPI0_MOSI) |
| MISO | Pin 21 | GPIO 9 (SPI0_MISO) |
| GND | Pin 6 | Ground |
| RST | Pin 22 | GPIO 25 |
| 3.3V | Pin 1 | 3.3V Power |

### Setup Commands
```bash
sudo apt-get update
sudo apt-get install python3-dev python3-pip
sudo pip3 install spidev
sudo pip3 install mfrc522
```

### Write Script: `write.py`
```python
import RPi.GPIO as GPIO
from mfrc522 import SimpleMFRC522

reader = SimpleMFRC522()

print("Looking for RFID cards...")
print("Press Ctrl+C to stop.")

try:
    text = input("Enter New Data to write: ")
    print("Now place your tag near the RC522 reader...")
    reader.write(text)
    print("Data Written Successfully!")
finally:
    GPIO.cleanup()
```

### Read Script: `read.py`
```python
import RPi.GPIO as GPIO
from mfrc522 import SimpleMFRC522

reader = SimpleMFRC522()

print("Looking for RFID cards...")
print("Press Ctrl+C to stop.")

try:
    id, text = reader.read()
    print("Card Unique ID:", id)
    print("Card Payload Data:", text)
finally:
    GPIO.cleanup()
```
