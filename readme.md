NOTE: This is only for Elegoo Printers who are using a single hotend. If you are using more than one hotend, do not use this configuration. 

Enter into Fluidd (the UI for your printer/klipper) so you can access klipper and your config files. 

To do this, you would go to your computer's browser and enter the ip address for your 3d printer. Your printer's ip address can be found by looking at the printer's screen in a config section somewhere. 

ex. 192.168.xxx.xxx/#


After logging in, go to your printer's configuration tab. 

Find your "printer.cfg". right click the file and rename it. You should rename to something like: "printer-elegoo.cfg". we do not want to delete this because you may want to go back to this if you dont like my printer.cfg. 

download the included printer.cfg from this github and upload it to fluidd. This will act as your new config file. 
download the orca_variables.cfg from this github and upload it to fluidd.

if everything is uploaded, restart klipper from the Fluid UI screen. 

Now configure your orca slicer's printer profile. 



