Retropie
========

Shortcuts
---------

### Exit game

Start+Select

### Quick menu (within game)

Select+X


Shaders
-------

### Creating a custom shader config

Quick menu -> shaders to select/configure shaders

My preferred shader is crt-pi with higher bloom and brightness (and tweaked gap size). Once configured, apply and safe modified filter (save w/o a name, and it is saved to the default name 'retroarch', which lets you modify with just a controller)

### Setting default shader

- RetroPie ncurses Menu
- Setup / Configuration
- Edit RetroPie/RetroArch Configurations
- Advanced configuration
- Configure libretro options
- all/retroarch.cfg (or whatever)
- Select video shader from above

Scrapers
--------

RetroPie has a built-in scraper in the UI. Don't use it.

Instead, "scraper" by sselph behaves much better (it uses hashes instead of name matching). It can be installed and used via the menus.

- Alt+F4
- sudo ~/RetroPie-Setup/retropie_setup.sh
- In 4.x Choose 'Manage Packages' then 'Optional Packages'.
- Select Scraper
- Wait for the installation to complete (it may take some time as it has to install various software libraries)
- Scrape All Systems: This will scrape all the systems the scraper supports

Wii U Controller
----------------

1. Either open the commandline on the pi by exiting emulationstation or ssh into it
2. Ensure your bluetooth adapter is setup correctly and working
3. Enter 'sudo bluetoothctl devices' to get list bluetooth devices
4. Enter 'sudo bluetoothctl' command
5. The bluetooth control input should open, now type in the following commands in this order (you can use the TAB key to auto complete the mac address for the pair and connect command)

```
power on
agent on
pairable on
<press red sync button>                                         # not a command press the button on the controller
scan on
pair <MAC of the found wiimote, use TAB for autocompletion>     # note: we do not explicitly connect, we just pair!
connect <MAC of the wiimote>                                    # there seems to be a pretty short timeout, so execute this immediately after the pairing command
trust <MAC of the wiimote>
disconnect <MAC of the wiimote>
```


Now you're done, press any button on the controller and it should connect, press the power button and it should disconnect, it also persists after a reboot

Note: WiFi interference causes a significant amount of lag. Will likely want WiFi off when using BT controllers. Test BT latench via:

```
sudo l2ping <MAC of the wiimote>
```


WiFi on/off
-----------

As mentioned in Wii U Controller section, WiFi makes BT controllers much laggier. Consider turning off WiFi on the Raspberry Pi.

```
sudo iwconfig wlan0 txpower off
```

Wifi is now permanently off until the command "sudo iwconfig wlan0 txpower on" and the system is rebooted.
