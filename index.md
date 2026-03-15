---
title: Final Project
---

# Overview

Desk Buddy is a standalone embedded device designed to help students stay focused while studying by following a Pomodoro-style work and break cycle. The goal of the project was to build a small desk companion that helps users manage their focus time without needing to rely on a phone or computer. The device uses an OLED display to show the countdown timer and the current system mode, such as `SELECT`, `IDLE`, `RUN`, `BREAK`, `PAUSE`, and `ALARM`. The user interacts with the system using an IR remote, which allows them to choose a sprite, start a work session, pause the timer, reset the system, or switch to break mode without needing to use a terminal or connect to a computer.

## Goals
- Implement Pomodoro Timer
- Detect shakes
- Dictionary Search

## Design

### Functional Specification

The behavior of Desk Buddy is controlled using a finite state machine that manages the different phases of the Pomodoro workflow. The system moves between states based on user input from the IR remote, timer events, and shake detection from the accelerometer. The state machine allows the device to control when the timer is running, paused, or finished, while also determining how the display, LED, and buzzer should behave. When the device first starts, it enters the `SELECT` state. In this state, the user chooses one of the available sprite sets using the number buttons on the IR remote. Once a sprite is selected, the system transitions to the IDLE state. In the `IDLE` state, the device waits for the user to begin a work session. The OLED display shows the current mode and the timer, but no countdown is active yet. 
When the user presses the start button, the system moves into the RUNNING state and begins the work timer countdown. While the timer is running, the user can pause the timer, reset the session, or manually switch to a break. If the pause command is used, the system transitions to the PAUSED state, where the remaining time is preserved until the user resumes the timer or resets the system. If the work timer reaches zero, the device enters the ALARM condition. During this time, the buzzer sounds and the RGB LED blinks to notify the user that the session has finished. The alarm remains active until the user physically shakes the device. The accelerometer detects this shake gesture and acknowledges the alarm. 
After the alarm is acknowledged, the next state depends on which timer finishes. If the alarm occurs after a work session, the system transitions into the BREAK state and begins the break timer. If the alarm occurs after a break session, the system returns to the IDLE state and waits for the user to start another work session. Overall, the state machine ensures that the device follows the full cycle of selecting a character, starting a work session, optionally pausing or resetting, completing the session, and transitioning into a break period before returning to idle. This structure allows the device to consistently manage the Pomodoro workflow while responding to user input and timer events. 



### System Architecture



# Implementation

## Finite State Machine

## Timer System

The timing behavior of the system is controlled using the SysTick timer, which generates interrupts every 1 millisecond (ms). Each interrupt increments a global millisecond counter that acts as the main time reference for the system. This counter is used to track the countdown timer, system uptime, and timing for alarms and animations. 
To implement the Pomodoro timer, the system checks the elapsed milliseconds in the main loop and converts this value into one second intervals. Each second the countdown timer decreases until it reaches zero. When the timer reaches zero, an alarm flag is triggered which causes the system to activate the buzzer and LED alarm behavior. 
TimerA0 is also used separately for measuring the pulse widths of incoming infrared signals from the remote. By measuring the duration of the pulses, the system can decode the transmitted IR command values mentioned beforehand. 


## IR Remote Module

### Circuit Setup

The IR interface uses a three pin IR receiver module with Vs, OUT, and GND. The Vs pin is connected to the board supply voltage, and the GND pin is connected to ground. The OUT pin is connected to a GPIO input pin on the CC3200 microcontroller. This output provides a digital signal that represents the infrared pulses received from the remote transmitter. To improve signal stability, the recommended protection components from the receiver datasheet were used in the design. A resistor was placed in series with the supply line, and a capacitor was placed between the supply and ground near the receiver. These components help filter electrical noise and protect the receiver from electrical overstress. The capacitor also stabilizes the supply voltage so that the receiver can detect fast infrared pulse signals reliably. When a button is pressed on the remote, the infrared transmitter sends a coded pulse train. The receiver extracts the infrared signal and outputs a sequence of digital high and low pulses on the OUT pin. These pulses are then captured and processed by the microcontroller.

## OLED Display

### Serial Peripheral Interface (SPI) Communication

#### GPIO Pin Setup

The Adafruit SSD1351 OLED communicates through the SPI protocol. Five signals are required to control the OLED. This includes the Data/Command (DC), OLED Chip Select (CS), Reset, Master-Out-Slave-In (MOSI), and the SPI Clock (SC) signal. 
We selected package pins 61, 62, 18, 7, and 5 for these connections from the microcontroller to our OLED. The GPIO pins are set as output as we are using the MOSI line.

#### SPI Configuration

In our `main` program, we set the SPI bit rate to 4 MHz for default data transfer. This could have been increased to 8 MHz if needed for faster refresh.
For initialization, we first reset the SPI controller and clear buffers. For the configuration settings, we chose to set our microcontroller as the master, the 8-bit word length to match communication with our peripheral, and three pin mode which allows us to manually choose the peripheral though toggling our GPIO pin. After configuration, we enabled the SPI channel.

#### Adafruit SSD1351 Library

To communicate with the OLED through SPI, the peripheral must be able to distinguish between writing commands and data. The `“`Adafruit_Init()`”` function from the `Adafruit_OLED` program is called in `main` after enabling the SPI channel. 

##### *Write Command*

The CS signal is set low, and the DC signal is set low to transmit the command byte. We put the byte on the SPI line, clear the byte, and set CS high to the end the transmission.

#### *Write Data*

Like `writeCommand`, except the DC signal is set to high to signify it as a data byte.

#### Update UI

#### *Formatting, Displaying, and Clearing Text*
From the Adafruit open-source graphics library `Adafruit_GFX.c`, take advantage of the `drawChar()` function to display characters on our OLED. Our custom created `drawText()` function takes in a string and uses a for loop to display the characters. 

#### *Draw Select Screen*

#### *Draw Layout*

## Accelerometer

## AWS

## RGB LED

## Buzzer

## Stretch Goals
- Speech to Text

## Challenges

## Bill of Materials
- CC3200 Board

# Video Demo

