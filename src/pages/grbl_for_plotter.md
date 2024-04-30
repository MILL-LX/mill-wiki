# Configuring Grbl for Plotter using Servo and CoreXY

Rough documentation of the process of configuring grbl for [[morgan]].  

I ended up using cprezzi's modification for grbl: https://github.com/cprezzi/grbl-servo. The M5 command to disable the servo did not work for me, but this wasn't crucial.  

The servo signal is connected to D11 / spindle pwm.  

## Grbl Setup
**Make sure that you only have one version of Grbl in your Arduino libraries folder!**  

### in config.h:

For coreXY comment
```
//#define HOMING_CYCLE_0 ((1<<X_AXIS)|(1<<Y_AXIS))  // NOT COMPATIBLE WITH COREXY: Homes both X-Y in one cycle.
```
and uncomment
```
#define HOMING_CYCLE_0 (1<<X_AXIS)  // COREXY COMPATIBLE: First home X
#define HOMING_CYCLE_1 (1<<Y_AXIS)  // COREXY COMPATIBLE: Then home Y
```
and
```
#define COREXY // Default disabled. Uncomment to enable.
```

For servo control uncomment
```
#define SPINDLE_IS_SERVO // Default disabled. Uncomment to enable.
```

## Grbl settings

Check endstop polarities with `?`  
If and endstop is triggered it will show as `Pn:X` or `Pn:Y`  
To invert polarity invert switch connections or: `$5=1`  

Check motor directions:  
`G91 G0 X10` / `X-10` / `Y10` / `Y-10`  
If the polarity is wrong then invert motor coil connections or `#3=3` to invert both axis directions.  
In theory, `$3=1` should invert x and `#3=2` invert y, but it looks like in coreXY this just inverts the motor axes.  

Set steps/mm:  
`$100=80`  
`$101=80`  

Set homing feed:  
`$24=500`  
Set homing acceleration:  
`$25=7500`  

Set max feedrate:  
`$110=15000`  
`$111=15000`  

Set acceleration:  
`$120=500`  
`$121=500`  

Set max travel:  
`$130=1000` x  
`$131=700` y  

Setup homing and limits:  
`$21=1` activate limit switches  
`$22=1` activate homing cycle  
`$23=3` invert homing direction (normally homes in positive dir)  
`$20=1` activate soft limits (homing must be activated first)  

Disable step idle delay (always keep steppers enabled):  
`$1=255`  

Regarding use as plotter / servo control:  
`$32=1` activate laser mode.  
`$30=255` (Optional) Set the max S-value for M3 commands. I believe i this is 1000 by default, which is what we are using.  
  

## Different modifications for servo control

I found a number of different efforts to add servo control to grbl, here are my notes:

### DWiskow grbl1-1g-Servo

https://github.com/DWiskow/grbl1-1g-Servo  
https://forum.eleksmaker.com/topic/2510/grbl-1-1g-with-servo-for-mana-se  
replaces spindle pwm control with servo control  
servo connected on D11 / spindle pwm  
uses M3 Sxx for motion  

"To use this, download the latest version of grbl (1.1g) from github and replace ‘spindle_control.c’ with the version provided in the above link. You will also need to select/modify SPINDLE_TCCRB_INIT_MASK in ‘cpu_map.h’ to select the appropriate (61Hz) servo frequency rate"  

to changing prescalar:  
`#define SPINDLE_TCCRB_INIT_MASK ( (1<<CS22) | (1<<CS21) | (1<<CS20) ) 1/1024 prescaler -> 61Hz (Servo)`  
with help from: http://www.fiz-ix.com/2012/01/how-to-configure-arduino-timer-2-registers-to-drive-an-ultrasonic-transducer-with-a-square-wave/  
_motion with M3 worked but M5 to deactivate didn't_  

### cprezzi (switchable) Servo support

https://github.com/cprezzi/grbl-servo  
https://github.com/gnea/grbl/pull/153  
modified grbl 1.1f  
very similar to DWiskow's solution, but seem to include some config.h edits for homing specific to his setup.  
copies some of spindle code to keep spindle mode as an option.  
mode switched in config.h:  
`#define SPINDLE_IS_SERVO`  
_motion with M3 worked but M5 to deactivate didn't_

### KoljaWindeler $33 servo mode

https://github.com/gnea/grbl/pull/245  
adds new mode that copies some of spindle code and adapts it for servo  
adds eemprom setting $33 to toggle servo/spindle control  
_could not get it to compile small enough for atmega328_  

### Misan/robottini grbl-servo

https://github.com/misan/grbl-servo  
https://github.com/robottini/grbl-servo  
https://www.instructables.com/id/4xiDraw/  
https://www.instructables.com/id/CNC-Arduino-GRBL-Easydriver-Shield/  
modified grbl 0.9i  
servo connected on D11 / spindle pwm  
uses M3 Sxx for motion  

### arnabkumardas

https://www.arnabkumardas.com/cnc.html  
https://github.com/arnabdasbwn/grbl-coreXY-servo  
another fork from robottini's version, a lot of documentation of build  

### Buildlog modified grbl

https://github.com/bdring/Grbl_Pen_Servo  
http://www.buildlog.net/blog/2017/08/using-grbls-spindle-pwm-to-control-a-servo/  
https://www.instructables.com/id/How-to-Control-a-Servo-Using-GRBL/  
https://jpralves.net/post/2018/03/07/how-to-control-a-servo-using-grbl.html  
modified grbl1.1f  
pen up down positions defined in firmware  
movement comes from z position  
