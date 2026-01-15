# esphome_cyd
Setting up and using a Cheap Yellow Device (CYD) with ESPHome/Home Assistant

# Introduction
The CYD is a very inexpensive (aka cheap) 2.8" display with an ESP32 board attached to it.  The name refers to the fact that the PCB is yellow.  My board has the model number ESP32-2432S028.  Requiring only a USB-C power supply, it can provide a wifi-enabled display wherever you want it.  You can pick them up from AliExpress for under $10 or from Amazon for under $20 if you want it quicker.  Did I mention it has a touch screen too?  Yeah, it has a touch screen. And two physical buttons on the back. And GPIO pins if you want to attach your own sensors - but we won't go into that here.
![PXL_20260114_192745171](https://github.com/user-attachments/assets/74834e1d-38f9-47ab-8736-58f110fb12f1)
*Case as it arrived.  Some might have an acrylic case with it for a little extra cost*

![PXL_20260114_192821128](https://github.com/user-attachments/assets/2ab31e10-d0ca-4cc2-8cfa-7f82bdbf5b96)
*Back showing the model number*

The use case for this example is a display near my front door with date and time, temperature, and current weather.  Once you have this set up you can add code to display more data or do different things.  This is just a simple example to get you set up and working with HA.

# What is Home Assistant?
[Home Assistant](https://www.home-assistant.io/) (HA) is a software package that can communicate and control smart devices within your home.   You can run HA on a standard Linux system, docker container, etc.  I chose to support the developers by buying a Home Assistant Green that provides hardware and the ability to communicate with Zigbee and Bluetooth devices in the house.  These directions should work however you're using HA.

# Prerequisites
To start off, you'll need the following:
 - A working CYD with USB-C cable to plug into computer for inital programming, then for power
 - Laptop or PC with Chrome available - you'll use this to install the initial software via serial connection but this all gets handled by the browser.  Firefox won't work, sorry.
 - USB 5V power supply.  Since the power requirements are far lower than a Raspberry Pi, a simple cell phone charger would be fine for longer term power.  You could also use a lipo battery but that's another project.

# Set up Home Assistant
We'll have to assume you already have HA installed and configured.  There's plenty of [documentation](https://www.home-assistant.io/installation/) on initial setup.

## Weather Application
You will need a weather app to provide data to display.  There's a variety of choices, I'm using [OpenWeatherMap](https://openweathermap.org/) but you can also use [met.no](https://www.met.no/).  Both are free though OWM requires you sign up for a free account and get an API key with a limit of 1000 requests per day.

## Add-ons
In Home Assistant screen, click on Settings, then Add-Ons and then click on Add-ons Store in the lower right hand corner.  From the Store, install `ESPHome Device Builder` and `File Browser`.  You'll need these later on.

## Wifi credentials
With the File Browser installed, click on that on the left side of the HA screen and then the folder logo on the upper left.  You'll see a set of directories including one called `esphome`.  Click on that and then click on "New Folder" and call it `common`.  Enter that directory by clicking on it and then create a file called `wifi.yml`.  Edit the wifi.yml file to include your WiFi access point and password.

# Inital Setup of CYD
When you first power it on by plugging into a 5V supply you may get a simple display that shows the processor, display, and touchscreen are working.  Power it on, make sure that's good, and then let's move on to configuring it for HA.  If you don't, check that the LED on the back is powered on.

## Installing ESPHome firmware
Unlike standard PCs or Raspberry Pis, ESP32 devices don't have an operating system that we'd recognize on it.  Instead, it has firmwre and acts like an embedded device (since..it's an embedded device).  The nice thing is that once you have the firmware installed and connected to HA, you can update the code at any time wirelessly, also known as Over The Air (OTA).

From your computer go to the [ESPHome Web](https://web.esphome.io/) and make sure your device is plugged in and running.

<img width="528" height="560" alt="Click on Connect" src="https://github.com/user-attachments/assets/dae59d61-53f1-425c-80fd-d948d241898a" />

Click on "connect" and then select the correct serial port.  Since I'm using Linux, this will be `ttyUSB0`

<img width="434" height="452" alt="Select the correct serial port" src="https://github.com/user-attachments/assets/1ed7b31c-f3a5-4cd5-bdba-78148fb47911" />

First off, you'll want to get the basic firmware installed.  Click on "Prepare for first use"

<img width="474" height="147" alt="Click on prepare for first use" src="https://github.com/user-attachments/assets/eef7a2b4-c417-44fe-b832-b3d423b28fba" />

Then click "Install"

<img width="576" height="281" alt="Click on Install" src="https://github.com/user-attachments/assets/1908a85f-5837-454b-8a06-983f478e4e86" />

It's going to take a minute or two for this to upload so keep the system from going to sleep.

<img width="386" height="245" alt="Upload in progress" src="https://github.com/user-attachments/assets/7465e73e-8c21-4ed7-ab1b-c229720ff6c2" />

With the firmware installed you can now connect it to your wifi

<img width="322" height="320" alt="Click Change Wi-fi" src="https://github.com/user-attachments/assets/6a87b96f-a503-4f2c-847d-f920b21e857e" />

From the ESPHome Web screen you can also click on the three pips on the right side and click "Configure Wi-Fi"

<img width="333" height="264" alt="Configure Wi-Fi" src="https://github.com/user-attachments/assets/04e24148-85cb-42a5-a291-a0df58a88630" />

Enter the wifi name and password and it should now be online and visibile to Home Assistant.  Stupid me let me laptop go to sleep while the firmware was being loaded.  You can repeat the steps to get it working.

## Connecting to Home Assistant

Now that the CYD has the firmware and online we can get it into HA and start expanding capabilities.  At this point the screen is just plain black and it appears dead.  Trust me, it's not, it's waiting.

Go to Settings -> Devices & Services and the new device should be there (along with any other devices that HA might have found.  Click Add to have the device added.

<img width="318" height="279" alt="Click Add to have device added to Home Assistant" src="https://github.com/user-attachments/assets/2f8c29ab-2b7f-4aef-958b-5a54a66a2772" />

From the HA screen click on "ESPHome Builder" 

Since the device isn't controlled by ESPHome Builder yet, it won't appear on the list and your screen may be blank.  Click on the Show in the upper right corner.

<img width="318" height="279" alt="Click on Show" src="https://github.com/user-attachments/assets/379f0e8d-0464-4bff-82af-58f91f54808b" />

You should now see at least one device that says "ESPHome Web" and an option to Take control.

<img width="392" height="183" alt="Take Control" src="https://github.com/user-attachments/assets/7bd16d48-7852-4705-bb58-48e6992b07fc" />

You may be asked to re-enter the Wi-Fi credentias

<img width="400" height="756" alt="Re-enter Wifi credentials" src="https://github.com/user-attachments/assets/5fc44e86-8dae-4fee-8438-bbd0208061eb" />

Click Install to put on a base ESPHome 

<img width="393" height="202" alt="Click install" src="https://github.com/user-attachments/assets/5c8eb03e-9e90-4015-9ee1-2efa309ee610" />

# Uploading code
Your device should now show up under `ESPHome Builder` with options to Visit, Edit, and Logs.  First off, you'll need to add wifi configuration to a separate file.  This will allow you to store your wifi credentials in a shared file which will allow you to share your code with the world without exposing your credentials, and prevent typos from knocking your device offline.

Click on "File editor" then the Folder icon at the top.  Click on "esphome" to enter that directory.  Click on the folder with a plus sign on it to create a new directory.  Call it "common".  Click on the directory and create a new file called "wifi.yml" and copy the contents of the wifi.yml file there and make sure you change the contents to match your individual wifi.

Almost there.

Now click on "ESPHome Builder" again and click on your ESPHome device that we just set up.  Click on Edit and remove the wifi configuration.  Copy in the contents of "weather.yml".  The code is pretty well documented and some of it it just boilerplate stuff to set up the display and draw on the screen. There's really two sets of sensors we're reading to start.  First is the time and date which we're getting directly from Home Assistant.  The other set is from OpenWeatherMap and gives us the current temperature and weather state.  The temperature is a numerical value and thus can just be a sensor.  The weather state will be things like "cloudy", "sunny", "rain", etc.  For that we'll need to use a text_sensor to let our code know to not expect a number.

We'll also set up the touchscreen so that if you touch it, the backlight toggles so you can easily turn it off and back on.

Once the code is in, click on "Install" and you can select "Wirelessly" to have the code compiled and updated over the air.  
