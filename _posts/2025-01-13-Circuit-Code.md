---
title: "Circuit Project Code"
date: 2025-01-13
categories: [projects, Documentation]
tags: [Code, Python]
---
###### This is a post that includes the code for the laundry machine circuit project
Go to the about page to see all the features that the code runs in the machine

```python
import RPi.GPIO as GPIO
import time

# GPIO pin assignments (correct BCM pin numbers for Raspberry Pi 4)
START_PAUSE_PIN = 17  # Button 1 (Start/Pause)
STOP_PIN = 27         # Button 2 (Stop)
SPEED_PIN = 22        # Button 3 (Speed cycle)
PWM_PIN = 18          # Motor PWM control
BUZZER_PIN = 23       # Buzzer pin

# LED pin assignments (green, yellow, red)
GREEN_LED_PIN = 24
YELLOW_LED_PIN = 25
RED_LED_PIN = 5

# Speed settings (Duty cycle for PWM)
SPEEDS = [10, 30, 90]  # Slow, Medium, Fast
current_speed = 0       # Index for current speed (0 = slow, 1 = medium, 2 = fast)
machine_running = False
machine_paused = False

# Setup GPIO
GPIO.setmode(GPIO.BCM)  # Set the pin numbering to BCM mode
GPIO.setup(START_PAUSE_PIN, GPIO.IN, pull_up_down=GPIO.PUD_UP)  # Button 1
GPIO.setup(STOP_PIN, GPIO.IN, pull_up_down=GPIO.PUD_UP)         # Button 2
GPIO.setup(SPEED_PIN, GPIO.IN, pull_up_down=GPIO.PUD_UP)        # Button 3
GPIO.setup(PWM_PIN, GPIO.OUT)                                   # Motor PWM pin
GPIO.setup(BUZZER_PIN, GPIO.OUT)                                 # Buzzer pin

# Setup LED pins
GPIO.setup(GREEN_LED_PIN, GPIO.OUT)
GPIO.setup(YELLOW_LED_PIN, GPIO.OUT)
GPIO.setup(RED_LED_PIN, GPIO.OUT)

# Setup PWM for motor control
pwm_motor = GPIO.PWM(PWM_PIN, 1000)  # PWM frequency = 1kHz
pwm_motor.start(0)  # Start with 0% duty cycle (motor off initially)

# Function to start or pause the machine
def toggle_start_pause(channel):
    global machine_running, machine_paused, cycle_start_time
    if machine_running:
        if not machine_paused:
            print("Pausing the machine")
            pwm_motor.ChangeDutyCycle(0)  # Stop motor
            machine_paused = True
        else:
            print("Resuming the machine")
            pwm_motor.ChangeDutyCycle(SPEEDS[current_speed])  # Resume with current speed
            machine_paused = False
    else:
        print("Starting the machine")
        pwm_motor.ChangeDutyCycle(SPEEDS[current_speed])  # Start motor with current speed
        machine_running = True
        cycle_start_time = time.time()  # Record the start time of the cycle

# Function to stop the machine
def stop_machine(channel):
    global machine_running, machine_paused
    print("Stopping the machine")
    pwm_motor.ChangeDutyCycle(0)  # Stop motor
    machine_running = False
    machine_paused = False
    # Turn off all LEDs
    GPIO.output(GREEN_LED_PIN, GPIO.LOW)
    GPIO.output(YELLOW_LED_PIN, GPIO.LOW)
    GPIO.output(RED_LED_PIN, GPIO.LOW)

# Function to cycle through the speeds
def cycle_speed(channel):
    global current_speed
    current_speed = (current_speed + 1) % len(SPEEDS)
    if machine_running and not machine_paused:
        print(f"Changing speed to {SPEEDS[current_speed]}%")
        pwm_motor.ChangeDutyCycle(SPEEDS[current_speed])
    update_leds()  # Update LEDs when speed changes

# Function to update LEDs based on the current speed
def update_leds():
    # Turn off all LEDs
    GPIO.output(GREEN_LED_PIN, GPIO.LOW)
    GPIO.output(YELLOW_LED_PIN, GPIO.LOW)
    GPIO.output(RED_LED_PIN, GPIO.LOW)

    # Turn on appropriate LED based on current speed
    if SPEEDS[current_speed] == 10:
        GPIO.output(GREEN_LED_PIN, GPIO.HIGH)
    elif SPEEDS[current_speed] == 30:
        GPIO.output(YELLOW_LED_PIN, GPIO.HIGH)
    elif SPEEDS[current_speed] == 90:
        GPIO.output(RED_LED_PIN, GPIO.HIGH)

# Function to activate the buzzer for 3 seconds
def activate_buzzer():
    print("Cycle completed. Activating buzzer for 3 seconds.")
    GPIO.output(BUZZER_PIN, GPIO.HIGH)  # Turn on the buzzer
    time.sleep(3)  # Keep the buzzer on for 3 seconds
    GPIO.output(BUZZER_PIN, GPIO.LOW)   # Turn off the buzzer

# Setup event detection for buttons
GPIO.add_event_detect(START_PAUSE_PIN, GPIO.FALLING, callback=toggle_start_pause, bouncetime=300)
GPIO.add_event_detect(STOP_PIN, GPIO.FALLING, callback=stop_machine, bouncetime=300)
GPIO.add_event_detect(SPEED_PIN, GPIO.FALLING, callback=cycle_speed, bouncetime=300)

try:
    while True:
        if machine_running and not machine_paused:
            # Check if 10 seconds have passed since the cycle started
            if time.time() - cycle_start_time >= 10:
                print("Cycle completed!")
                pwm_motor.ChangeDutyCycle(0)  # Stop motor after 10 seconds
                machine_running = False  # Stop the machine
                activate_buzzer()  # Activate buzzer for 3 seconds

        time.sleep(0.1)  # Main loop is idle, waiting for button presses

except KeyboardInterrupt:
    print("Program interrupted")

finally:
    pwm_motor.stop()  # Stop the motor before exiting
    GPIO.cleanup()     # Clean up GPIO settings
```
