io7uNeo
======

This is a sample nodered flow project with the io7 IOT Platform.

It lets select the color and send it to the io7 device which colors the neopixels. The following is the io7 device with io7fupython library.
```python
from IO7FuPython import ConfiguredDevice
import json
import time
import uComMgr32
import neopixel
from machine import Pin

neopxl = neopixel.NeoPixel(Pin(15), 8, bpp=3)
color = {'r':0, 'g':0, 'b':0}

def setPixel(color):
    for i in range(8):
        neopxl[i] = (color['r'], color['g'], color['b'])
    neopxl.write()
    
def handleCommand(topic, msg):
    global lastPub, color
    jo = json.loads(str(msg,'utf8'))
    if ("color" in jo['d']):
        color = jo['d']['color']
        setPixel(color)
        lastPub = - device.meta['pubInterval']

nic = uComMgr32.startWiFi('io7lux')
device = ConfiguredDevice()
device.setUserCommand(handleCommand)

device.connect()

lastPub = time.ticks_ms() - device.meta['pubInterval']

setPixel(color)

while True:
    if not device.loop():
        break
    if (time.ticks_ms() - device.meta['pubInterval']) > lastPub:
        lastPub = time.ticks_ms()
        device.publishEvent('status', json.dumps({'d':{'color': color}}))
```
