# esphome_cyd
Setting up and using a Cheap Yellow Device (CYD) with ESPHome/Home Assistant

# Introduction
The CYD is a very inexpensive (aka cheap) 2.8" display with an ESP32 board attached to it.  The name refers to the fact that the PCB is yellow.  My board has the model number ESP32-2432S028.  Requiring only a USB-C power supply, it can provide a wifi-enabled display wherever you want it.  You can pick them up from AliExpress for under $10 or from Amazon for under $20 if you want it quicker.  Did I mention it has a touch screen too?  Yeah, it has a touch screen. And two physical buttons on the back. And GPIO pins if you want to attach your own sensors - but we won't go into that here.

The use case for this example is a display near my front door with date and time, temperature, and current weather.  Once you have this set up you can add code to display more data or do different things.  This is just a simple example to get you set up and working with HA.

# What is Home Assistant?
[Home Assistant](https://www.home-assistant.io/) (HA) is a software package that can communicate and control smart devices within your home.   You can run HA on a standard Linux system, docker container, etc.  I chose to support the developers by buying a Home Assistant Green that provides hardware and the ability to communicate with Zigbee and Bluetooth devices in the house.  These directions should work however you're using HA.

# Prerequisites
To start off, you'll need the following:
 - A working CYD with USB-C cable to plug into computer for inital programming, then for power
 - Laptop or PC with Chrome available - you'll use this to install the initial software via serial connection but this all gets handled by the browser.  Firefox won't work, sorry.
 - USB 5V power supply.  Since the power requirements are far lower than a Raspberry Pi, a simple cell phone charger would be fine for longer term power.  You could also use a lipo battery but that's another project.

# Set up Home Assistant
## Weather Application
You will need a weather app to provide data to display.  There's a variety of choices, I'm using [OpenWeatherMap](https://openweathermap.org/) but you can also use [met.no](https://www.met.no/).  Both are free though OWM requires you sign up for a free account and get an API key with a limit of 1000 requests per day.

## Add-ons
In Home Assistant screen, click on Settings, then Add-Ons and then click on Add-ons Store in the lower right hand corner.  From the Store, install `ESPHome Device Builder` and `File Browser`.  You'll need these later on.

## Wifi credentials
With the File Browser installed, click on that on the left side of the HA screen and then the folder logo on the upper left.  You'll see a set of directories including one called `esphome`.  Click on that and then click on "New Folder" and call it `common`.  Enter that directory by clicking on it and then create a file called `wifi.yml`.  Edit the wifi.yml file to include your WiFi access point and password.

# Inital Setup of CYD
We'll have to assume you already have HA installed and configured.  There's plenty of [documentation](https://www.home-assistant.io/installation/) on initial setup.

When you first power it on by plugging into a 5V supply you'll get a simple display that shows the processor, display, and touchscreen are working.  Power it on, make sure that's good, and then let's move on to configuring it for HA.

## Installing ESPHome firmware
Unlike standard PCs or Raspberry Pis, ESP32 devices don't have an operating system that we'd recognize on it.  Instead, it has firmwre and acts like an embedded device (since..it's an embedded device).  The nice thing is that once you have the firmware installed and connected to HA, you can update the code at any time wirelessly, also known as Over The Air (OTA).

From your computer go to the [ESPHome Web](https://web.esphome.io/) and make sure your device is plugged in and running.
<img width="528" height="560" alt="image" src="https://github.com/user-attachments/assets/dae59d61-53f1-425c-80fd-d948d241898a" />

Click on "connect" and then select the correct serial port.  Since I'm using Linux, this will be `ttyUSB0`
<img width="434" height="452" alt="image" src="https://github.com/user-attachments/assets/1ed7b31c-f3a5-4cd5-bdba-78148fb47911" />

## Connecting to Home Assistant

Now that the CYD has the firmware and online we can get it into HA and start expanding capabilities.  At this point the screen is just plain black and it appears dead.  Trust me, it's not, it's waiting.

From the HA screen click on "ESPHome Builder" 

Your device should now show up under `ESPHome Builder` with options to Visit, Edit, and Logs.  First off, you'll need to add wifi configuration to a separate file.  This will allow you to store your wifi credentials in a shared file which will allow you to share your code with the world without exposing your credentials, and prevent typos from knocking your device offline.
