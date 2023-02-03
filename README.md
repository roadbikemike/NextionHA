# Weather Station using Home Assistant (HA), Nextion, ESPHome and Wemos D1 Mini
(_Version 0.2_) 

Updates:
0.1: Initial release
0.2: Minor update to pressure.  If pressure exceeded three digits, the display was truncating.

The intention of this project was to replace a basic home weather station which recently failed.  My wife wanted to replace the station with another [cheap unit from Amazon](https://www.amazon.com/AcuRite-00424CA-Digital-Thermometer-Temperature/dp/B00WXIR94M/ref=sr_1_33?crid=2560J0F63KQT8&keywords=acurite+weather+station&qid=1673193466&sprefix=accurite+weather+station%2Caps%2C115&sr=8-33).  Instead, I took this as an opportunity to build my own.  I think she had some doubts with my intent and perhaps I did as well, but lets dive in.  In all actuality, the project turned out much better than anticipated so its worth sharing.

![Weather Project](/images/project.jpg "Weather Project Photo")

# Required Components

The backbone of the entire project is [Home Assistant](https://www.home-assistant.io/) and [ESPHome](https://esphome.io/).  Obviously this could be done without HA, but HA makes things much easier and is highly recommended.

* [Home Assistant](https://www.home-assistant.io/) 
* [ESPHome](https://esphome.io/)
* [Wemos D1 Mini (or clone)](https://www.amazon.com/dp/B081PX9YFV?psc=1&ref=ppx_yo2ov_dt_b_product_details)
* [Nextion 2.8″ HMI Display Discovery Series NX3224F028](https://www.amazon.com/dp/B0BBL3GM3S)
* [HiLetgo BME280 3.3V Atmospheric Pressure Sensor](https://www.amazon.com/dp/B01N47LZ4P)
* [3D Printer](https://www.creality.com/products/ender-3-v2-neo-3d-printer)
* Jumper wires (for testing)
* Wires for connecting the Wemos and BME280
* Good soldering iron and some soldering skills
* Micro USB cables (to power the Wemos)
* CAT-5 Cable (not ideal)
* Shrink tubing (to keep things tidy)
* M3-0.5 x 8mm screws (for mounting the Nextion and case back)
* USB to TTL Serial Programmer (not entirely required, but makes testing the Nextion easier with the editor)

# Nextion Display

Programming and designing the Nextion display is relatively straight forward if you have experience with other programming IDE's.  If not, it may take longer to grasp some of the concepts.  Regardless, if you load my HMI file into the [Nextion Editor](https://nextion.tech/nextion-editor/), this will show you how the UI was built, etc.

![IDE](/images/nextion_ide.png "IDE")

I used [Paint.NET](https://www.getpaint.net/) for building the UI background, so the PDN file is included in the Nextion folder along with the other images required by the Nextion Editor.  Also, there is a lightbulb image which I used to indicate if our landscape lights were on.  You could obviously remove or replace with something else.

Note:  The HMI file has two pages which I did not show.  The second page shows inside temperature data from my Nest.  I attempted to implement a waveform to show historical outside temperature data but later removed.  Updating a waveform object from another page and keeping its state seems very challenging.  The second page could easily be removed using the Nextion Editor.  Historical graphs for temperature, humidity and pressure can be found in HA, so having them in the display would likely not be looked at anyhow.

# Outside Sensor

The outside sensor uses two components.  The BME280 (Temperature, Humidity and Pressure) sensor itself, some wires and a Wemos D1 Mini running ESPHome.  Something to keep in mind when connecting the BME280 to the Wemos.  The i2c protocol was not designed for long distances between components.  It was actually designed for components on the same board or components very close to each other.  If the BME280 is too far from the Wemos, you will have reliability and communication issues.  When I originally connected the two, they were separated by about two feet.  This was too long which yielded unreliable data and often times caused the sensor to entirely disconnect.  After learning more about the i2c protocol and limitations, I shortened the wires to less than 12" and no longer having issues.  So, keep this in mind should you decide to build this project.  Using shielded wires may help if a longer run is needed, but stay away from ribbon cable.  From what I understand, the wires in ribbon cable are too close together which can lead to i2c interference.

In regards to connecting the BME280 outside sensor and Wemos, I went with wires from the inside of a CAT-5 cable.  This is not ideal and something I plan to replace in the near future.  I also did some unnecessary soldering as well.  Jumper wires should probably be used here.  The CAT-5 wires were attractive because they are small in diameter and fit through the seals on our windows and I had them on hand.


![BME280](/images/outside_sensor.jpg "BME280 and Case")

# Inside Display

The inside unit also uses a Wemos to drive the Nextion display and communicate with HA over Wifi.  Updates to the display are brokered through [ESPHome Nextion display platform](https://esphome.io/components/display/nextion.html).  The source of the display data comes from HA and feeds from the outdoor sensor.

Updating the Nextion display can be done over the air as well when integrated with HA. This is much easier than pulling everything apart and attaching the Nextion display to the USB programmer.

1. From the Nextion Editor, select File, then "TFT file output".  Select a path and save the file.
2. Move this file to Home Assistant in the path /config/www/tft.
3. Add a `tft_url` parameter to `display` in the Nextion ESPHome YAML file.  Also add a `service` to the `api` section.  Take a look at my YAML file for an example.
4. When your ready to update the display, go into HA Developer Tools.  Select SERVICES and search for ESPHome.  You should see something like `ESPHome: nextion_update_nextion`.  Click CALL SERVICE to initiate the update.


# 3D Printed Cases

All components are housed in custom 3D printed cases.  Well, I should clarify that.  The base designs came from [Thingiverse](https://www.thingiverse.com/), then modified using [Tinkercad](https://www.tinkercad.com/).  The clone Wemos were slightly larger than the case I pulled from Thingiverse so they had to be expanded.  Also the housing for the exterior sensor did not have covers over the openings to prevent element intrusion.  So, I added those along with a few minor tweaks.  

The Nextion case needed to be larger to account for the Wemos and a hole added for the USB power cable.  The original design had the screw holes for the case back too close to the Nextion display screws.  They were so close you could not get a tool in there to affix the display to the case.  I also added some ventilation slits to keep things cool.

**Looks like I made some modifications to the BME280 case which did not save, so they are not what I have.  You may have to tinker with this one to get it correct or grab the original mentioned below.**

Need to thank my kids as well for the Christmas present 3D printer!

I should give credit to those that provided the original designs:

* [BME280 Case](https://www.thingiverse.com/thing:3809818)
* [Wemos D1 Case](https://www.thingiverse.com/thing:1768820)
* [Nextion Case](https://www.thingiverse.com/thing:1497062)

![Outside Case](/images/case_outside.jpg "Outside Case")
![BME280 Wemos Case](/images/case_wemos.jpg "BME280 Wemos Case")
![Nextion Case](/images/case_inside.jpg "Nextion Case")
![Nextion Case Back](/images/case_back.jpg "Nextion Case Back")

# Challenges

The entire project was a challenge, but a lot of fun.  Proper planning and forethought were key, then ironing out the details as the project came together made things much simpler.  Some things to note:

* Soldering
    * I am by no means an expert at soldering and had some challenges with this at times.  Probably need to break down and by a new station.
* Nextion baud rate
    * Getting the Nextion display to operate at 115200 was tricky.  Apparently this has to be defined in both the Program.s file and in the ESPHome configuration.  Operating at 9600 is way too slow for over the air (OTA) updates.  The slower speed caused one of my updates to fail and the only way I could reprogram the Nextion was to use the USB programmer.
* Nextion data delay
    * Getting data from the HA API to the Nextion takes time.  If your using DHCP, the Wemos has to wake up, get on WiFi, get an address, then communicate to HA which is not instantaneous.  I was being impatient and could not figure out why I was not getting data.  You can speed up the display time by using `fast_connect` on the ESPHome `wifi` settings and using a static address.
* Wiring
    * As mentioned previously, the wires from inside a CAT-5 cable are not ideal and length between the Wemos and BME280 can be an issue.  Keep them as short as possible.

