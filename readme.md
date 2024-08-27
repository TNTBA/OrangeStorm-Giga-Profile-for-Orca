# System profile for Orca Slicer
![orca-screenshot](https://github.com/user-attachments/assets/c6cfe1cd-7b1f-45fe-b8c2-be0092766612)

**Currently this profile works ONLY after making changes to your printer.cfg file in klipper.**
**This is only for Elegoo Printers who are using a single hotend. If you are using more than one hotend (IDEX configuration), do not use this configuration.** 

Enter into Fluidd (the UI for your printer/klipper) so you can access klipper and your config files. 

To do this, you would go to your computer's browser and enter the ip address for your 3d printer. Your printer's ip address can be found by looking at the printer's screen in a config section somewhere. 

ex. 192.168.xxx.xxx/#

After logging in, go to your printer's configuration tab. 

Find your "printer.cfg". right click the file and rename it. You should rename to something like: "printer-elegoo.cfg". we do not want to delete this because you may want to go back to this if you dont like my printer.cfg. 

download the included printer.cfg from this github and upload it to fluidd. This will act as your new config file. 
download the orca_variables.cfg from this github and upload it to fluidd.

if everything is uploaded, restart klipper from the Fluid UI screen. 


# ORCA SLICER PROFILE INSTALLATION INSTRUCTIONS
You will need the following files to make this work:

## Main Manufacturer Config file:
### 1. orca_slicer_config\profiles\Elegoo.json

On windows:You will require admin privileges in order to do this.  Go to "C:\Program Files\OrcaSlicer\resources\profiles" from windows explorer. You will see there is already an "Elegoo.json". Rename this file as "Elegoo original.json" or save it in another location on your computer in case you wish to revert back one day. Save the Elegoo.json file from this github to that location. 

## Build Plate SVG & STL
### 1. orca_slicer_config\profiles\Elegoo\elegoo_orangestorm_giga_buildplate_model.stl
### 2. orca_slicer_config\profiles\Elegoo\giga_plate-02.svg

On Windows: Go to "C:\Program Files\OrcaSlicer\resources\profiles\Elegoo"
You will save the two files in this directory. You will not overwrite anything. 

Save these two files in the following location: 
C:\Program Files\OrcaSlicer\resources\profiles\Elegoo

## Process files:
### 1. orca_slicer_config\profiles\Elegoo\process\fdm_process_orangestorm_common.json
### 2. orca_slicer_config\profiles\Elegoo\process\0.20mm Standard @Elegoo Orangestorm Giga (0.4 nozzle).json
### 3. orca_slicer_config\profiles\Elegoo\process\0.20mm Standard @Elegoo Orangestorm Giga (0.6 nozzle).json
### 4. orca_slicer_config\profiles\Elegoo\process\0.20mm Standard @Elegoo Orangestorm Giga (0.8 nozzle).json
### 5. orca_slicer_config\profiles\Elegoo\process\0.20mm Standard @Elegoo Orangestorm Giga (1.0 nozzle).json

On Windows: Go to "C:\Program Files\OrcaSlicer\resources\profiles\Elegoo\process" and save the above github files to this location. 

## Machine Profiles:
### 1. orca_slicer_config\profiles\Elegoo\machine\fdm_orangestorm_giga_common.json
### 2. orca_slicer_config\profiles\Elegoo\machine\Elegoo Orangestorm Giga (0.4 nozzle).json
### 3. orca_slicer_config\profiles\Elegoo\machine\Elegoo Orangestorm Giga (0.6 nozzle).json
### 4. orca_slicer_config\profiles\Elegoo\machine\Elegoo Orangestorm Giga (0.8 nozzle).json
### 5. orca_slicer_config\profiles\Elegoo\machine\Elegoo Orangestorm Giga (1.0 nozzle).json

On Windows: Go to "C:\Program Files\OrcaSlicer\resources\profiles\Elegoo\machine" and save the above github files to this location. 

## Filament Profiles:
### 1. orca_slicer_config\profiles\Elegoo\filament\Elegoo Generic ABS.json
### 2. orca_slicer_config\profiles\Elegoo\filament\Elegoo Generic PETG.json
### 3. orca_slicer_config\profiles\Elegoo\filament\Elegoo Generic PLA.json


**Windows**

On Windows: Go to "C:\Program Files\OrcaSlicer\resources\profiles\Elegoo\filament" and save the above github files to this location. 
You may want to save the Elegoo Generic profiles located in the folder before overwriting them with mine - its up to you. 

### Final steps:
With those files in their appropriate directories, close Orcaslicer. Then go you will then need to go to "C:\Users\YOUR_USER_NAME\AppData\Roaming\OrcaSlicer\system" and delete the "C:\Users\YOUR_USER_NAME\AppData\Roaming\OrcaSlicer\system\Elegoo.json" file in that location. 

Try loading up Orca Slicer and go to printer selection, search for Elegoo, scroll toward the bottom to see Orangestorm Giga and the 4 nozzle profiles. Select whichever one you intend on using and enjoy!

If this does not work, close Orca Slicer and go back to "C:\Users\YOUR_USER_NAME\AppData\Roaming\OrcaSlicer\system" and delete:
Elegoo.json & the entire Elegoo directory.

**Mac OSX**

On Mac, open a Finder window and navigation to Applications. Right click on OrcaSlicer and click on Show Package Contents. You will be presented with Contents, double click on that folder, then double click on Resources, then double click on profiles. From here you can save the above files. Your 'orca_slicer_config' path should be this be default: "/Applications/OrcaSlicer.app/Contents/Resources/".

Relaunch, if OrcaSlicer is open, otherwise open OrcaSlicer and you will now have the Orangestorm Giga profiles. 

**Please note that by deleting the Elegoo directory, you may lose custom print profiles you have created with your other elegoo machines. I have not tested this to see whether that would be the case since the Orangestorm Giga is my first Elegoo printer.** 


