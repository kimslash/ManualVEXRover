#region VEXcode Generated Robot Configuration
from vex import *
import urandom

# Brain should be defined by default
brain=Brain()

# Robot configuration code
motor_group_3_motor_a = Motor(Ports.PORT3, GearSetting.RATIO_18_1, True)
motor_group_3_motor_b = Motor(Ports.PORT4, GearSetting.RATIO_18_1, False)
motor_group_3 = MotorGroup(motor_group_3_motor_a, motor_group_3_motor_b)
optical_2 = Optical(Ports.PORT2)
controller_1 = Controller(PRIMARY)


# wait for rotation sensor to fully initialize
wait(30, MSEC)


def play_vexcode_sound(sound_name):
    # Helper to make playing sounds from the V5 in VEXcode easier and
    # keeps the code cleaner by making it clear what is happening.
    print("VEXPlaySound:" + sound_name)
    wait(5, MSEC)

# add a small delay to make sure we don't print in the middle of the REPL header
wait(200, MSEC)
# clear the console to make sure we don't have the REPL in the console
print("\033[2J")



# define variables used for controlling motors based on controller inputs
controller_1_up_down_buttons_control_motors_stopped = True

# define a task that will handle monitoring inputs from controller_1
def rc_auto_loop_function_controller_1():
    global controller_1_up_down_buttons_control_motors_stopped, remote_control_code_enabled
    # process the controller input every 20 milliseconds
    # update the motors based on the input values
    while True:
        if remote_control_code_enabled:
            # check the buttonUp/buttonDown status
            # to control motor_group_3
            if controller_1.buttonUp.pressing():
                motor_group_3.spin(FORWARD)
                controller_1_up_down_buttons_control_motors_stopped = False
            elif controller_1.buttonDown.pressing():
                motor_group_3.spin(REVERSE)
                controller_1_up_down_buttons_control_motors_stopped = False
            elif not controller_1_up_down_buttons_control_motors_stopped:
                motor_group_3.stop()
                # set the toggle so that we don't constantly tell the motor to stop when
                # the buttons are released
                controller_1_up_down_buttons_control_motors_stopped = True
        # wait before repeating the process
        wait(20, MSEC)

# define variable for remote controller enable/disable
remote_control_code_enabled = True

rc_auto_loop_thread_controller_1 = Thread(rc_auto_loop_function_controller_1)

#endregion VEXcode Generated Robot Configuration

# ------------------------------------------
# 
# 	Project:      rover thingy
#	Author:       group
#	Created:      TODAY
#	Description:  we made rover it's cool and awsome
# 
# ------------------------------------------

# Library imports
from vex import *

# Begin project code

#make variables HERE
blue = 0
orange = 0
brown = 0
green = 0
gray = 0
red = 0
line = 1

#functions start HERE

optical_2.set_light_power(50)
brain.screen.print("Now operational! ^_^")
def testdrive(): # this function was just a test and does nothing, isn't called and is just here as a reminder for where we started
    #motor_group_3.spin(REVERSE)
    wait(250)
    #motor_group_3.stop()
    newlinemsg("finished 2 second drive...")

#testdrive()

def constantcheck(): # this function also does nothing and was used for debug to constantly check the output underneath our sensor
    newlinemsg("now checking for color")
    
    while True:
        wait(1000)
        colornum = checkcolor(optical_2.hue(), optical_2.brightness(), True)
        if not colornum == 0:
            newlinemsg(colornum)



def checkcolor(chue, cbright, increment): # this method compares the hue and brightness outputs to detect which color it sees
    global blue, orange, brown, green, gray, red
    if (chue <= 20 and chue >= 16) and (cbright <= 12 and cbright >= 10): # this is red
        if increment: red += 1
        return ("red " + str(red))
    elif (chue <= 27 and chue >= 21) and (cbright <= 20 and cbright >= 11): # this is orange
        if increment: orange += 1
        return ("orange " + str(orange))
    elif (chue <= 41 and chue >= 34) and (cbright <= 10 and cbright >= 5): # this is brown
        if increment: brown += 1
        return ("brown " + str(brown))
    elif (chue <= 48 and chue >= 44) and (cbright <= 28 and cbright >= 20): # this is gray
        if increment: gray += 1
        return ("gray " + str(gray))
    elif (chue <= 92 and chue >= 84) and (cbright <= 8 and cbright >= 7): # this is green
        if increment: green += 1
        return ("green " + str(green))
    elif (chue <= 233 and chue >= 224) and (cbright <= 10 and cbright >= 7): # this is gray
        if increment: blue += 1
        return ("blue " + str(blue))
    elif (chue < 45 and chue >= 43) and (cbright < 20): # this is the FLOOR
        newlinemsg("THIS IS THE FLOOR.")
        return 0
    else:
        newlinemsg("no color detected, hue: " + str(chue) + " br: " + str(cbright))
        return 0

def newlinemsg(text): # this function makes a quick workaround to printing new lines for messages, and erasing old ones
    global line
    line += 1
    if line > 10:
        line = 1
        brain.screen.clear_screen()
    brain.screen.set_cursor(line, 1)
    brain.screen.print(text)

#constantcheck()

def AButtonPress(): # function for when the A button is pressed

    returntext = checkcolor(optical_2.hue(), optical_2.brightness(), True)
    if not returntext == 0:
        newlinemsg(returntext)

def BButtonPress(): # function for when the B button is pressed (same as A)
    returntext = checkcolor(optical_2.hue(), optical_2.brightness(), False)
    if not returntext == 0:
        newlinemsg(returntext)

def turnside(direction, ontype): # this function is triggered by 4 different calls, and each correspond to what the behavior will be
    if direction:
        if ontype:
            motor_group_3_motor_a.spin(REVERSE)
            motor_group_3_motor_b.spin(FORWARD)
        else:
            motor_group_3.stop()
    else:
        if ontype:
            motor_group_3_motor_a.spin(FORWARD)
            motor_group_3_motor_b.spin(REVERSE)
        else:
            motor_group_3.stop()

def buttonleftpressed(): # button press that calls the turnside() function with parameters
    turnside(True, True)

def buttonleftrelease(): # button press that calls the turnside() function with parameters
    turnside(True, False)

def buttonrightpressed(): # button press that calls the turnside() function with parameters
    turnside(False, True)

def buttonrightrelease(): # button press that calls the turnside() function with parameters
    turnside(False, False)

def buttonycheck(): # the final method, prints all colors and how many we've seen to that point, and also shows the total
    global blue, orange, brown, green, gray, red, line
    line = 4
    brain.screen.clear_screen()
    brain.screen.set_cursor(1, 1)
    brain.screen.print("red: " + str(red) + "   | orange: " + str(orange))
    brain.screen.set_cursor(2, 1)
    brain.screen.print("brown: " + str(brown) + " | gray: " + str(gray))
    brain.screen.set_cursor(3, 1)
    brain.screen.print("green: " + str(green) + " | blue: " + str(blue))
    brain.screen.set_cursor(4, 1)
    brain.screen.print("total: " + str(red + orange + brown + green + gray + blue))
   

controller_1.buttonA.pressed(AButtonPress)
controller_1.buttonB.pressed(BButtonPress)
controller_1.buttonLeft.pressed(buttonleftpressed)
controller_1.buttonLeft.released(buttonleftrelease)
controller_1.buttonRight.pressed(buttonrightpressed)
controller_1.buttonRight.released(buttonrightrelease)
controller_1.buttonY.pressed(buttonycheck)