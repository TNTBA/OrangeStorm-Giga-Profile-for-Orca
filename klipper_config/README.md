WHY DID I DO THIS???
# Orca does not have any available configurations that enables the use of multiple heatbeds.
# If it does, then i'm an idiot. If I'm right, then what this code will do is figure out where
# the models were placed on your slicer and send that information to klipper to figure out which
# heatbeds to turn on and off. 
#                                                          
# Updated Macros:
# 1. M140 - Now handles multiple heatbeds. If a T# parameter is provided, it controls
#    the specified bed heater. Otherwise, it uses min/max coordinates to determine 
#    which beds to heat.
# 2. M190 - Updated to correctly reference sensors for each heater bed and wait 
#    appropriately based on bed coordinates or the T# parameter.
# 3. PRINT_END - Enhanced to perform comprehensive shutdown procedures, including clearing 
#    min/max XYZ variables, turning off all heaters, fans, and steppers.
# 4. PAUSE - Modified to save the current print state, including extruder position and 
#    temperatures, to enable proper resumption.
# 5. RESUME - Updated to restore the printer state after a pause, including extruder 
#    and bed temperatures.
# 6. CANCEL_PRINT - Improved to clear the print state, stop all movements, and turn off 
#    heaters, fans, and steppers as necessary.

# Notes:
# - Make sure to include the orca_variables.cfg file as it contains the necessary 
#   variable definitions for the M140, M190, and PRINT_END macros.
#
# It took a lot of time and patience to get this working thanks to ChatGPT.
# Follow me for other 3D printer related stuff www.tntba.com
# TryNotToBreakAnything     

# I've placed the macros you will need to overwrite or copy and paste in your printer.cfg. You can keep old macros in the same file if you want by including a semicolon ";" before each line of code - which you will see in my printer.cfg


[include orca_variables.cfg]

[gcode_macro RESUME]
rename_existing: BASE_RESUME
variable_flag_print_status: 1
variable_zhop: 0
variable_etemp: 0
variable_bed_temp: 0
variable_bed_temp1: 0
variable_bed_temp2: 0
variable_bed_temp3: 0
variable_saved_x: 0.0
variable_saved_y: 0.0
variable_saved_z: 0.0
variable_saved_e: 0.0
gcode:
    {% if flag_print_status > 0.5 %}
    M25
    {% set e = params.E|default(2)|int %}   
    SET_IDLE_TIMEOUT TIMEOUT={printer.configfile.settings.idle_timeout.timeout}
    SET_HEATER_TEMPERATURE HEATER=extruder TARGET={etemp}
    SET_HEATER_TEMPERATURE HEATER=heater_bed TARGET={bed_temp}
    SET_HEATER_TEMPERATURE HEATER=heater_bed1 TARGET={bed_temp1}
    SET_HEATER_TEMPERATURE HEATER=heater_bed2 TARGET={bed_temp2}
    SET_HEATER_TEMPERATURE HEATER=heater_bed3 TARGET={bed_temp3}
    {% if printer[printer.toolhead.extruder].temperature < etemp-4 %}
      TEMPERATURE_WAIT SENSOR=extruder MINIMUM={etemp-4} MAXIMUM={etemp+50}
    {% endif %}
    M400
    G91
    M83
    G1 E100 F200	
    G4 P2000	
    G1 X-20 F15000
    G1 X20
    G1 X-20
    G1 X20
    G1 X-20
    G1 X20
    G90
    G92 E{saved_e}
    M400
    SET_GCODE_VARIABLE MACRO=RESUME VARIABLE=flag_print_status VALUE="{0}"
    M400
    SET_GCODE_VARIABLE MACRO=PAUSE VARIABLE=flag_print_status VALUE="{1}"
    M400
    RESTORE_GCODE_STATE NAME=timelapse_state_b MOVE=1 MOVE_SPEED=250
    M400
    RESTORE_GCODE_STATE NAME=timelapse_state_a MOVE=1 MOVE_SPEED=50	
    M400
    M24
    {% else %}
    M400
    {% endif %}


[gcode_macro PRINT_START]         
gcode:
    SET_GCODE_VARIABLE MACRO=RESUME VARIABLE=flag_print_status VALUE="{0}"
    M400
    SET_GCODE_VARIABLE MACRO=PAUSE VARIABLE=flag_print_status VALUE="{1}"
    M400
    SAVE_VARIABLE VARIABLE=was_interrupted VALUE=True
	G92 E0
    BED_MESH_CLEAR                                           
	G90
    BED_MESH_PROFILE LOAD=default   
    CLEAR_PAUSE
    # C1  
    M117 Printing



[gcode_macro PRINT_END]
gcode:
    SAVE_VARIABLE VARIABLE=was_interrupted VALUE=False
    RUN_SHELL_COMMAND CMD=clear_plr
    clear_last_file
    # Clear min/max XYZ variables
    SET_GCODE_VARIABLE MACRO=HEATBED_CONTROL VARIABLE=min_x VALUE="0.0"
    SET_GCODE_VARIABLE MACRO=HEATBED_CONTROL VARIABLE=min_y VALUE="0.0"
    SET_GCODE_VARIABLE MACRO=HEATBED_CONTROL VARIABLE=min_z VALUE="0.0"
    SET_GCODE_VARIABLE MACRO=HEATBED_CONTROL VARIABLE=max_x VALUE="0.0"
    SET_GCODE_VARIABLE MACRO=HEATBED_CONTROL VARIABLE=max_y VALUE="0.0"
    SET_GCODE_VARIABLE MACRO=HEATBED_CONTROL VARIABLE=max_z VALUE="0.0"
    {% set RUN_VELOCITY = printer.configfile.settings['printer'].max_velocity|float %}
    {% set RUN_ACCEL    = printer.configfile.settings['printer'].max_accel|float %}
    {% set RUN_DECEL    = printer.configfile.settings['printer'].max_accel_to_decel|float %}
    M400
    SET_GCODE_VARIABLE MACRO=RESUME VARIABLE=flag_print_status VALUE="0"
    M400
    SET_GCODE_VARIABLE MACRO=PAUSE VARIABLE=flag_print_status VALUE="1"
    M400
    SET_VELOCITY_LIMIT VELOCITY={RUN_VELOCITY} ACCEL={RUN_ACCEL} ACCEL_TO_DECEL={RUN_DECEL}
    M220 S100
    M221 S100
    {% set z = params.Z|default(200)|int %}
    {% if (printer.gcode_move.position.z+10) < z %}
      G90
      G1 Z{z+10} F6000
    {% endif %}
    G91 ;Relative positioning
    M83
    G1 E-2 F2700 ;Retract a bit
    G1 X5 Y5 E-8 Z3 F2400 ;Retract and raise Z
    G90 ;Absolute positioning
    G1 X20 Y20 F9000 ;Present print
    TURN_OFF_HEATERS
    M106 S0 ;Turn off fan
    M104 S0 ;Turn off hotend
    M140 S0 ;Turn off bed
    M84 X Y E ;Disable all steppers but Z
    M107
    M84


[gcode_macro CANCEL_PRINT]
rename_existing: BASE_CANCEL_PRINT
variable_flag_home_x: 0
variable_flag_home_y: 0
gcode:
    SAVE_VARIABLE VARIABLE=was_interrupted VALUE=False
    RUN_SHELL_COMMAND CMD=clear_plr
    clear_last_file
    # Clear min/max XYZ variables
    SET_GCODE_VARIABLE MACRO=HEATBED_CONTROL VARIABLE=min_x VALUE=""
    SET_GCODE_VARIABLE MACRO=HEATBED_CONTROL VARIABLE=min_y VALUE=""
    SET_GCODE_VARIABLE MACRO=HEATBED_CONTROL VARIABLE=min_z VALUE=""
    SET_GCODE_VARIABLE MACRO=HEATBED_CONTROL VARIABLE=max_x VALUE=""
    SET_GCODE_VARIABLE MACRO=HEATBED_CONTROL VARIABLE=max_y VALUE=""
    SET_GCODE_VARIABLE MACRO=HEATBED_CONTROL VARIABLE=max_z VALUE=""
    M400
    SET_GCODE_VARIABLE MACRO=RESUME VARIABLE=flag_print_status VALUE="{0}"
    M400
    SET_GCODE_VARIABLE MACRO=PAUSE VARIABLE=flag_print_status VALUE="{1}"
    M400
    {% set RUN_VELOCITY = printer.configfile.settings['printer'].max_velocity|float %}
    {% set RUN_ACCEL    = printer.configfile.settings['printer'].max_accel|float %}
    {% set RUN_DECEL    = printer.configfile.settings['printer'].max_accel_to_decel|float %}
    SET_VELOCITY_LIMIT VELOCITY={RUN_VELOCITY} ACCEL={RUN_ACCEL} ACCEL_TO_DECEL={RUN_DECEL}
    {% set z = params.Z|default(200)|int %}
    {% set x_park = params.X|default(printer.toolhead.axis_minimum.x+30)|int %}
    {% set y_park = params.Y|default(printer.toolhead.axis_minimum.y+30)|int %}
    SET_IDLE_TIMEOUT TIMEOUT={printer.configfile.settings.idle_timeout.timeout}
    SDCARD_RESET_FILE
    M220 S100
    M221 S100
    {%if flag_home_x > 0.5 and flag_home_y > 0.5%}
      M400
      M83
      G1 E-2.0 F1200
      {%if (printer.gcode_move.position.z+10) < z %}
      G90 
      G1 X{x_park} Y{y_park} Z{z+10} F6000 
      {% else %}
      G91
      G1 Z5 F600
      G90 
      G1 X{x_park} Y{y_park} F6000 
      {% endif %}
    {% else %}
    G91
    G1 Z5 F600
    {% endif %}        
    TURN_OFF_HEATERS
    M107
    G90
    M82
    M84



[gcode_macro PAUSE]
rename_existing: BASE_PAUSE
variable_flag_print_status: 1
variable_flag_home_x: 0
variable_flag_home_y: 0
gcode:
    {%if flag_home_x > 0.5 and flag_home_y > 0.5%}
      {% if flag_print_status > 0.5 %}
        M400  
      {% set z = params.Z|default(200)|int %}                                                   
      {% set E = (params.E|default(2))|float %}
      {% set x_park = params.X|default(printer.toolhead.axis_minimum.x+30)|int %} 
      {% set y_park = params.Y|default(printer.toolhead.axis_minimum.y+30)|int %} 
        M400
      {% set position = printer.gcode_move.gcode_position %}
        SET_GCODE_VARIABLE MACRO=RESUME VARIABLE=saved_x VALUE="{position.x}"
        SET_GCODE_VARIABLE MACRO=RESUME VARIABLE=saved_y VALUE="{position.y}"
        SET_GCODE_VARIABLE MACRO=RESUME VARIABLE=saved_z VALUE="{position.z}"
        SET_GCODE_VARIABLE MACRO=RESUME VARIABLE=saved_e VALUE="{position.e}"
      {% if printer[printer.toolhead.extruder].temperature > 150 %}
        SET_GCODE_VARIABLE MACRO=RESUME VARIABLE=etemp VALUE={printer['extruder'].target}
      {% endif %}
        SET_GCODE_VARIABLE MACRO=RESUME VARIABLE=bed_temp VALUE={printer['heater_bed'].target}
        SET_GCODE_VARIABLE MACRO=RESUME VARIABLE=bed_temp1 VALUE={printer['heater_generic heater_bed1'].target}
        SET_GCODE_VARIABLE MACRO=RESUME VARIABLE=bed_temp2 VALUE={printer['heater_generic heater_bed2'].target}
        SET_GCODE_VARIABLE MACRO=RESUME VARIABLE=bed_temp3 VALUE={printer['heater_generic heater_bed3'].target}
        SAVE_GCODE_STATE NAME=timelapse_state_a
        M83
        G91
        G1 E-2 X3 Z1 F2100
        G1 Z4 F600
        SAVE_GCODE_STATE NAME=timelapse_state_b
        G90
      {% if (printer.gcode_move.position.z+10) < z %} 
        G1 Z{z+10} X{x_park} Y{y_park} E-20 F6000 
      {% else %}
        G1 X{x_park} Y{y_park} E-20 F6000
      {% endif %}
        M400
        SET_GCODE_VARIABLE MACRO=PAUSE VARIABLE=flag_print_status VALUE="{0}"
        M400
        SET_GCODE_VARIABLE MACRO=RESUME VARIABLE=flag_print_status VALUE="{1}"
        M25
        SET_IDLE_TIMEOUT TIMEOUT=1800
        M400
      {% endif %}
    {% endif %}



[gcode_macro M140]
rename_existing: M140.6245197
gcode:
    {% if "T" in params %}
        {% set heater_bed = "heater_bed" ~ params.T|replace('0', '') %}
        SET_HEATER_TEMPERATURE HEATER={heater_bed} TARGET={params.S|int}
    {% else %}
        {% set min_x = printer["gcode_macro HEATBED_CONTROL"].min_x|float %}
        {% set min_y = printer["gcode_macro HEATBED_CONTROL"].min_y|float %}
        {% set max_x = printer["gcode_macro HEATBED_CONTROL"].max_x|float %}
        {% set max_y = printer["gcode_macro HEATBED_CONTROL"].max_y|float %}
        {% set temp = params.S|int %}

        {% if min_x < 405 and min_y < 402.5 %}
            SET_HEATER_TEMPERATURE HEATER=heater_bed TARGET={temp}  ; Heatbed 0 (Bottom Left)
        {% endif %}
        {% if max_x >= 405 and min_y < 402.5 %}
            SET_HEATER_TEMPERATURE HEATER=heater_bed1 TARGET={temp} ; Heatbed 1 (Bottom Right)
        {% endif %}
        {% if max_x >= 405 and max_y >= 402.5 %}
            SET_HEATER_TEMPERATURE HEATER=heater_bed2 TARGET={temp} ; Heatbed 2 (Top Right)
        {% endif %}
        {% if min_x < 405 and max_y >= 402.5 %}
            SET_HEATER_TEMPERATURE HEATER=heater_bed3 TARGET={temp} ; Heatbed 3 (Top Left)
        {% endif %}
    {% endif %}


[gcode_macro M190]
rename_existing: M190.6245197
gcode:
    {% if "T" in params %}
        {% set heater_bed = "heater_bed" ~ params.T|replace('0', '') %}
        TEMPERATURE_WAIT SENSOR={heater_bed} MINIMUM={params.S|int-2} MAXIMUM={params.S|int+2}
    {% else %}
        {% set min_x = printer["gcode_macro HEATBED_CONTROL"].min_x|float %}
        {% set min_y = printer["gcode_macro HEATBED_CONTROL"].min_y|float %}
        {% set max_x = printer["gcode_macro HEATBED_CONTROL"].max_x|float %}
        {% set max_y = printer["gcode_macro HEATBED_CONTROL"].max_y|float %}
        {% set temp = params.S|int %}

        {% if min_x < 405 and min_y < 402.5 %}
            TEMPERATURE_WAIT SENSOR=heater_bed MINIMUM={temp-2} MAXIMUM={temp+2}  ; Wait for Heatbed 0
        {% endif %}
        {% if max_x >= 405 and min_y < 402.5 %}
            TEMPERATURE_WAIT SENSOR="heater_generic heater_bed1" MINIMUM={temp-2} MAXIMUM={temp+2} ; Wait for Heatbed 1
        {% endif %}
        {% if max_x >= 405 and max_y >= 402.5 %}
            TEMPERATURE_WAIT SENSOR="heater_generic heater_bed2" MINIMUM={temp-2} MAXIMUM={temp+2} ; Wait for Heatbed 2
        {% endif %}
        {% if min_x < 405 and max_y >= 402.5 %}
            TEMPERATURE_WAIT SENSOR="heater_generic heater_bed3" MINIMUM={temp-2} MAXIMUM={temp+2} ; Wait for Heatbed 3
        {% endif %}
    {% endif %}