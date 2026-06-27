# Embedded-Escape-Room-README
This contains the readme for my Escape Room assignment programmed in C and Assembly on the STM32 Microcontroller with several peripherals and a GUI.

## Repository Structure
The repository is divided into the following project folders: BasicFunctionality, Puzzle1, Puzzle2, Puzzle3, Puzzle4, MainBoard.
All projects follow similar structure and reference the CMSIS folder path. All Puzzle and MainBoard modules constantly update and reference BasicFunctionality modules to ensure consistency and modularity (see Section 7: Initial Code Design for base modules). 

## Overview of System Concept

### Story Line
The world has been brought to its knees by the unlikeliest of sources, the STM32F3Disovery. The boards have gained sentience and have made the world do their UART and I2C bidding for decades. Now, thanks to the resistance and your crack team of scientists, the all-powerful boards are trapped in the briefcase before you. Alas, their resolve stays strong and won't go down without a fight. Using STM's backdoors, can you end the AI overlords once and for all?
- Puzzle 1: Obviously the boards need to see, PWM communications to their eyes have been hacked to show the code to disable it.
- Puzzle 2: Shut down the floating-point unit using its binary backdoor.
- Puzzle 3: Type in STM's master password to disable the I2C communication muahahahahahah
- Puzzle 4: FRY THEM FRY THEM FRY THEM BLIND IT BLIND IT EYES

### Game Design
- A guided escape room where the user(s) will be guided into completing ordered puzzles to ‘escape’.
- There will be four puzzles to complete, each yielding a number upon completion. After completing each puzzle, the user will input the code, if successful an LED will alight.
- After all four puzzles are successfully completed, the final puzzle will be unlocked and the game completed, with a storyline-based output on the monitor interface.

### Python GUI

![GUI](Images/MTRX2700_MAJOR_GUI.png)

### Physical Design
- The entire escape room will be contained in a briefcase, connected to a laptop/computer.
- The computer will not be used for any user input and will be used as an output device for hints, story line messages and to display a timer.
- The briefcase allows the user to interact with the system physically, with a range of tactile inputs such as flick switches, buttons, fuse connections, and joystick controls. 

### Progression Flow Chart

![Flowchart](Images/MTRX2700_Major_Project_Flowchart.drawio.png)

### Puzzle Design

#### Puzzle 1
- There will be an eye blinking a cypher in the briefcase.
- The user(s) will work together using a provided ‘decoding table’ to translate the message.
- This will be the solution to the puzzle.

#### Puzzle 2
- Using a clue hidden in a sentence at the beginning the user will unlock a physical box (using a letter combination lock).​
- Inside the box will be a code that needs to be decoded into binary.​
- The user will then flick toggle switches according to the binary code, which will move a servo to release a fuse.​
- The user then places the fuse into a receptacle, and a 7-seg display will then display the solution to the puzzle.

#### Puzzle 3
- The user will be given a context clue leading them towards the cogs.
- There will be two cogs one of which will be physically connected to the STM32, connecting these into the right orientation and rotating them will move a servo motor (which will move a dial, similar to a tachometer).
- When pointed to certain values a LED will light up.
- There will be two values that are lit which will be the code to complete this puzzle.

#### Puzzle 4
- The user will then be guided to holding a button which will trigger a laser.
- Using a joystick the user will be able to redirect the laser via moving a mirror in which the laser is pointed at.
- The clue will suggest a target that the user must guide the laser to point at.
- After targeting this point, the solution to the puzzle will be displayed.

#### Final Puzzle
- After each other puzzle, the user will enter the code into a keypad, which will light up corresponding LEDs
- Once all LEDs are lit, final puzzle will be solved.
- The final puzzle will output on the monitor the completion message (a story line derived output), alongside the timer stopping.
- Each time a wrong code is entered a time penalty is applied to the timer.



## Functional and Performance Requirements


| ***Functional Requirements*** | ***Performance Requirements*** |
|------|-----|
| System: Act as a 4-puzzle guided escape room contained within physical briefcase. | Responsiveness: Utilise hardware interrupts to trigger immediate physical actuations. |
| User Interface: Restrict all user input to physical tactile controls (switches, buttons, etc). | Real-Time Polling: Continuously read sensor data without noticeable latency to ensure smooth operation. |
| Game Logic: Validate puzzle success via a keypad, which will light an LED for success or deduct time from timer for failure. | Precision Control: Maintain stable PWM signals to ensure accurate and jitter-free targeting. |
| Puzzle Mechanics: Execute specific hardware actions per puzzle. |  |




## Sensors, Actuators, and Mechanical Control Elements


| ***Sensors & Physical Inputs*** | ***Mechanical Control Elements*** | ***Actuators*** |
|------|-----|-----|
|Light Dependent Resistor (LDR) | Push Buttons​ + Flick Switches | Physical Combination Locks |
| Magnetometer| Keypad | Laser Pointer |
| | Joystick | LEDs including 7 Segment Displays | 
| | Physical Combination Lock | Servo Motor (Eyes + Opening Latches) |
| | Fuse + Holder | Pan Tilt Unit (PTU) |



## Initial Code Design


### Base Modules
- gpio.c
- buttons.c
- pwm.c
- timer.c
- uart.c
- led.c
- i2c.c
- adc.c
  


## Module Testing Procedures


**I2C Testing:** Testing of the I2C between board was tested using I2C_board2board_test function in main.c in the BasicFunctionality project. The process was as follows:
- Connect master board and slave board by connecting pins PB6 and PB7 for I2C1, or PA9 and PA10 for I2C2. The grounds of both boards must also be connected.
- Flash the master board while the code under // SLAVE BOARD is commented
- Flash the slave board while the code under // MASTER BOARD is commented

- Using the console connected to each board via UART, the sending and receiving of each board can be seen, as long as the printf debugs within I2C.c are uncommented.
  
- To test I2C, the second 2 from each I2C2 function has to be removed, i.e I2C2_read should become I2C_read. The bus value in I2C_slave_init also has to be changed from 2 to 1.

- The slave board should be sending and the master board should be receiving a constantlychanging value between 0 and 255. Each send and receive should correspond in value.
- Valid slave addresses can be any 2 digit hex value except for values between 0x00 and 0x07, as well as 0x78 and 0x7F, inclusive, as these values are reserved. This is also specified in I2C.h.


**ADC Testing (Joystick):** Testing of the ADC was mainly conducted using analog_read, which gets the input from a specified ADC1 channel and returns the output from the ADC. 
- A joycon, which is effectively two potentiometers, was then connected to PA6 and PA7 and analog_read was then run continuously for both pins.
- The return of these functions was printed through UART to the console on STM32IDE. The values shown ranged from 0 to the max_res value, depending on the resolution ADC1 was set to. For example, the max_res value for TWELVE_B was 4096, as that is the max value for 12 bits of data.
- These values should correlate tho the position of the joycon.


**ADC Testing (LDR):** Testing of the ADC was mainly conducted using analog_read, which gets the input from a specified ADC1 channel and returns the output from the ADC via printing to terminal using UART. 
- A voltage divider circuit was built using a 10k resistor in a pull down configuration.
- The LDR outputs were measured under two scenarios: ambient, and laser pointed at LDR.
- Laser pointed at the LDR will result in a high ADC value, tested to be 4096. With ambient value of approximately 2000. Note: Both these values heavily dependent on the environmental light levels, the code was designed in a way where threshold can quickly be calculated and set.
- The middle point of these values is now the threshold value, this provides a point of comparison to determine two states of the LDR: 'laser detected' and 'laser not detected'.
- This threshold value is then used in puzzle 4 to determine if the laser is pointing at the LDR.


**Compass Testing:** This module is tested using compass_read, which reads the magnetometer data using I2C. which returns a 0-359 degree heading value, representing direction pointed.
- This is tested using debugger or using UART to terminal outputs.
- Firstly, calibrate initial direction (zeroing board) by calling compass_calib which sets direction board is currently pointed to the zero reference point.
- Turning the board to the right should result in heading values increased from 0.
- Turning the board to the left should result in heading values counting down from 359, leftward rotation results in heading values wrapping around.
- Ensure values being output are logical and correct and that the heading values capture the full 0-359 degree range.


**PWM Testing**: PWM was based on code from previous assignment, although some changes were made. PWM was tested via measuring the pulse width using an oscilioscope.
- A multi-channel approach was taken, where when setting position both a pulse width and channel is passed, this is necessary as multiple axis of servo motors is desired.
- So to test if the channel switching is working appropriately, an oscilloscope is used to measure different output pins when different pulse widths and channel is passed to function.
- Applying this to servos, passing values pulse width values of 1000-2000ms should move servo through its full range (approximately).


**PWM Handling Functions**: There is two handling functions used, servo_handler and compass_translation. One is used for multi-servo control and the other is used to translate compass to the servo's 90 degree range.
- servo_handler is designed to work with a joystick, and smooths x, y and z inputs (for PTU and eyeball control module). Therefore if individual servo control is working this module will also work, as servos don't conflict and aren't referrential.
- compass_translation is designed to translate compass inputs into servo outputs. This takes 0-359 values and translates this into the 90 degree servo range. This has been refined such that values of 0-180 result in pulse-width ranges of 1500-2000 while 180-359 results in pulse-width ranges of 1000-1500. This is set such that when compass value is 0 (at initial condition) servo is in middle of range (for puzzle purposes), therefore transforming the 0-359 range into an active -180 - +180 range (still has full 0-359 range).
- This can be tested by calling the function logic and seeing what pulse width is outputted from function, ensuring that the mapping is correct.


**Timer Testing:** Timer module is used primarily for oneshot functionality.
- Default timer is based on previous assignment, by using a callback function to an LED or GPIO pin an oscilloscope and physical timer can be used to verify auto reload register and prescaler is working correctly.
- One shot can be tested by again using an oscilloscope, ensuring its getting triggered after set time but ceases counting.
- This can be seen in debugger, by placing a breakpoint inside the timer ISR. Initalise the one shot and ensure the break point is hit, resuming execution verify the breakpoint is not hit again. Confirming timer correctly stops after one cycle and didn't auto reload.


**GPIO Testing:** Handles the fundamental input and output interactions.
- After configuring an output pin, ensure its correctly set high using a multimeter to read the pin voltage.
- After configuring an input pin, ensure when set high/low software correctly reflects 1/0 value.


**UART Testing:** This is responsible for printing to terminal for debug purposes as well as the interfacing with Python for a GUI display.
- When calling printf, output should route to its own input line. Therefore when the STM is plugged into laptop, using terminal software printf should route to terminal and display message.
- Python interface is not examined but uses similar logic, where output is routed to its own input but instead Python is used to design the visual elements.


**Morse Code Blinker Testing:** Responsible for handling the blinking eye motion in the z direction.
- State Machine Verification: Pass a known, simple string (e.g., "SOS") into the blinker function. Use terminal output to track the state transitions (READY $\rightarrow$ CLOSING $\rightarrow$ WAITING $\rightarrow$ OPENING $\rightarrow$ WAITING). Ensure the software smoothly transitions between states without hanging.
- Timing & Readability: Visually observe the servo output. Use a stopwatch or record a video of the blinking sequence to verify that the SHORT_BEEP (dot), LONG_BEEP (dash), and REST_TIME (gap between letters) are distinctly different and easily readable by a human player.
- Positional Limits: Verify that the "OPEN" and "CLOSE" target PWM values don't push the servo past its mechanical limits, preventing stalling or gear stripping during rapid blinking.




## Puzzle Code Overview


### Puzzle 1
This puzzle makes use of 3 servos: 
- x = horizontal eyeball motion
- y = vertical eye motion
- z = blinking eye motion
Whilst only z is essential to the puzzle, the other two also see usage in Puzzle 4 when integrated with the Joycon. They are elaborated upon there.

In order to avoid stalling the program in its main loop, the function call () updates the z servo position by a small amount with each call. This puzzle uses a state machine such that the current state will determine the next target eye position, and only once the target is reached over multiple calls will the next logical state follow.

The morse blinking module makes use of the following states:
- CLOSING - Eye is closing. 
- OPENING - Eye is opening.
- WAITING - For transitional delays when calling update_blink_position() will do nothing. Also used and timed for the long and short blink pulses.
- READY -   Eye has finished its previous blink pulse. Ready to close again.    
- MANUAL - For user set commands e.g. force_close_eye() and force_open_eye(). Closes the eye then waits.
<img width="577" height="694" alt="image" src="https://github.com/user-attachments/assets/7115ff17-b62b-4a77-938d-33f90c932de7" />

___Pseudocode​___
```
main():
          intiialise pwm
          set state to READY
          blink_code_update()

blink_code_update():
          if the state is:
          CLOSING
                    1. check if target position is reached
                    2.1 if yes, set mode to WAITING
                    3.1 set target_position to OPEN
                    3.2 trigger a oneshot timer to OPENING only after the blink time period passed (LONG_BEEP, SHORT_BEEP)
                    2.2 if no, update position with smoothed function
          OPENING
                    1. check if target position is reached
                    2.1 if yes, set mode to WAITING
                    3.1 set target_position to CLOSE
                    3.2 trigger a oneshot timer to READY after a short gap (REST_TIME)
                    2.2 if no, update position with smoothed function
          WAITING
                    1. return
          READY
                    1. read next morse char in the message
                    2. if at end of message, set mode to WAITING
                    3. otherwise, set mode to CLOSING
          MANUAL
                    1. set target position to CLOSE or OPEN as requested
                    2. update position with smoothed function                    

```
___Modules___
- morse_blinker.c
- servo_handler.c
- i2c.c
- adc.c
- gpio.c
- timer.c
- uart.c
- button.c

___Puzzle Testing___
**Test 1:** When button is pressed ensure eyes start the blinking sequence.

**Test 2:** Ensure in the blinking sequence, the states change as intended. Eyes should open fully and close fully, ensure servos are correctly going through entire range but not attempting to move outside physical limits (no excess noise).

**Test 3:** Ensure blinking sequence is correct and decodable (e.g long enough pauses between characters).

**Test 4:** When button is unpressed, verify eyes stop blinking and enter idle state.

 

### Puzzle 2
Puzzle 2 is comprised of 3 components - a physical box, a set of switches, and a 7-seg display. The player will solve a story-based puzzle to open the box, which will then give them a clue to input the correct switch code. Once this is input, the 3d-printed mouth will open, and inside is a fuse. The fuse should be put in the physical fuse holder which will then display the final puzzle code. This puzzle utilises, IO, interrupts, and pwm.

___Pseudocode​___
```
GPIO_Init​

Interrupt_Init​

While(1):​

// Wait for interrupt​

IRQHandler:​

 if solution == true:​

          i2c_completed_message();
          gpio(); // Display solution on 7 segment display​

    //puzzle complete
```
___Modules___
- switch_checker.c
- uart.c
- i2c.c
- pwm.c
- gpio.c

___Puzzle Testing___
**Test 1:** Flick switches to correct orientation and ensure servo switches position
**Test 2:** Confirm switches map correctly to LEDs


### Puzzle 3
Puzzle 3 simply translates the boards physical cardinal direction into a servo motor measurement. Where when pointed in a specific direction an LED will light, this will co-inside with the servo pointing at a number that will serve as the solution code to the puzzle. In practice the board will not be moved directly, but moved indirectly using two compatible gears, this is a unique way we were able to need more user input and precision without having to deal with turning a single board more than 360° which would cause issues with running wiring.  


___Pseudocode​___
```
While(1):​

// Read compass and display on servo​

heading = compass_read()​

pwm_compass_display(heading)​

​

// Turn on LED when pointed in desired direction​

if check_direction(heading, target)​

GPIO_Write_Pin(GPIOB, 3, GPIO_PIN_SET);
```
___Modules___
- compass.c
- compass_translation.c
- pwm.c
- i2c.c
- led.c
- gpio.c

___Puzzle Testing___

After setting the 'zero' direction, by using the STM's reset button the code can be sucessfully tested.

**Test 1:** Turn board from zero position in a full circle, track servo position. Servo should start at 45° and move all the way to 0° and 90°, it should sucessfully stop at this interval and not 'keep moving'. If it keeps attempting to move past this point it indicates a failure point in the edge case code and will cause damage to servo.

**Test 2:** Turn board to specified target direction, on boards LED's should light within tolerance of this direction. LED's not lighting at desired direction indicate that there was a failure in 'zeroing' the direction and it was not correctly reset, or in direction checking code.

**Test 3:** After target 1 has been found, the next direction is able to be found. Scan the range of numbers until next number is found, all onboards LED's should light. If no other values result in LED's lighting it indicates a failure in finding target 1.



### Puzzle 4
Puzzle 4 will prompt the user to point a laser using a pan-tilt unit driven by a joystick at a 'target', success will be measured using an LDR. This puzzle has hazards to be considered, the laser is quite powerful and could cause serious harm to users (or other people) if laser is pointed in their eyes. There will be two safety precausions, the laser will need a button to activate requiring the user to be actively focused to use. Next precausion will be a software based limit on the direction the laser can point, this will be hardcoded as limits to the servos that drive the PTU.


Two servos will drive the PTU's movements by a joystick:
- x = horizontal motion
- y = vertical motion

Successly pointing the laser at the target will be measured using a LDR which will toggle flag when found, this will then output the puzzles solution code to a 7 segment display. 

___Pseudocode___
```
while(1):​

// Interrupt contained in read_ldr​

read_ldr() ​

heading = read_joystick()​

if ldr_found == 1:​

7_segment() // Puzzle solution​

​

move_ptu(heading)
```
___Modules___
- pwm.c
- servo_handler.c
- ldr.c
- adc.c
- i2c.c
- gpio.c
- uart.c

___Puzzle Testing___

**Test 1:** Check LDR is accurately calibrated to lighting, and actively detecting laser. Point laser at LDR and ensure it is setting found value to high, this can be done through debugger or console output for simplicity. Adjust threshold value if it seems inaccurate and/or laser resolution by adjusting lens.

**Test 2:** Test PTU and joystick movement, by measuring x and y inputs to board using console/debugger and ensure that is being correctly reflected on PTU. Check whether boundary conditions are being correctly enforced, and not exceeding the predefined range for safety.

**Test 3:** Ensure after laser is detected, puzzle output is sucessfully triggered. Artifically light up LDR and test whether puzzle solution is displayed.


### Final Puzzle
The final puzzle will be completed when all individual puzzle are completed and all solution codes entered, this will be communicated to this puzzle (and board) using I2C. This final puzzle board will be the I2C master terminal with all other puzzles having their own boards and being slave terminals. After each puzzle users can enter the solution code using a keypad, after all puzzles are completed and all codes entered the final puzzle will be compelted and completion message would be sent using the computer interface. This board is also responsible for controlling and interfacing with the computer via Python, the interface has three main features: Timer countdown, Hint function for each puzzle, and Puzzle progress tracking. 

___Pseudocode___
```
while(1):​

          // Initalise fucntions

          If (!game_started && start_switch_active)
                    start_timer() // start timer if game not started and key on
          If (game_started) // get keypad input and check code
                    key = get_key()
                    process_key(key)
                    time = timer_get() // get the timer and send it to the gui
                    printf(time)
```
___Modules___
- start_switch.c
- pass_checker.c
- countdown.c
- keypad.c
- i2c.c


___Puzzle Testing___
**Test 1:** Ensure communication between all slave boards is successful, i.e after each puzzle is completed a success flag is passed to master board.
I2C communication was also tested using main.c in BasicFunctionality project. This is also detailed below.

**Test 2:** Keypad entries are correctly read to board, and only read when master board receives a success flag from puzzle, i.e penalty is applied to user trying to guess code without completing puzzle, therefore timer should decrease in time increment also.

**Test 3:** All this is being successfully outputted to computer interface via Python (puzzle completion and timer countdown).

**Test 4:** After all 4 puzzles are completed, the escape room is complete therefore timer should stop counting, and success message outputted (story based).

## Basic Functionality Code Overview
### I2C
___Overview___
The I2C module for this project was largely built off of the I2C module from the last assingment. It feautres a similar state machine, which is the same for board read and writes.
The added functionality is in the ability to use the I2C2 bus, adn to use a board as a slave device in order to enable board-to-board communication.
This works by running the ```I2C_slave_init()``` function to set the board as a slave, denoted by the I2C state. This sets the board's OAR and enables address-match interrupts, so that an interrupt can trigger when the I2C interface "calls" the board and data can be sent or received.
For this reason, a board cannot be both a master and a slave on the same bus due to the state machine, also, due to the buffers used, it cannot be a master and master on the same bus as well as a slave on both. The multiple busses are purely to be both a master and a slave for our use case.

___Pseudocode___
```
typedef enum {
  REG_WRITE,
  WRITE,
  REG_READ,
  READ,
  DONE,
  SLAVE
} I2C_state_t;

volatile I2C_state_t I2C_state;
volatile I2C_state_t I2C2_state;

uint8_t slave_rx_buf[16]
uint8_t slave_tx_buf[16]

void I2C_init() {
  // Set I2C1 to interrupt-based standard mode
}
void I2C2_init() {
  // Set I2C2 to interrupt-based standard mode
}

void I2C_slave_init(int bus, int addr) {
  // Set board to slave mode on specified bus with OAR as specified address
}

void I2C_write(int slv_addr, int slv_reg, int data[], int len) {
  // Write to specified I2C address and register (or no register if board-to-board) and      // write specified data with specified length.

  // User to ensure length is correct.
}

void I2C_read(int slv_addr, int slv_reg, int *dest, int len) {
  // Read from specified I2C address and register (or no register if board-to-board) and     // write specified data with specified length to dest array.

  // User to ensure length is correct.
}

void I2C_tx() {
  // Handle transmitting data on I2C1 via intterupt
  if (I2C_state == SLAVE) { slave_rx_buf[] = incoming_data; }
}
void I2C_rx() {
  // Handle receiveing data on I2C1 via interrupt
  if (I2C_state == SLAVE) { outgoing_data = slave_tx_buf[]; }
}

void I2C1_EV_EXTI23_IRQ_Handler() {
  // Handle logic for when interrupt flag are raised
}
void I2C1_ER_IRQ_Handler() {
  // Handle logic for when error interrupt flags are raised
}

// Repeat all functions for I2C2 (except init and slave init)

```
___State Machine___
```
            REG_WRITE                     REG_READ                  SLAVE
(default or from I2C_write call)     (from I2C_read call)   (from I2C_slave_init)
               |                              |
           Interrupt                      Interrupt
               |                              |  
               v                              v  
             WRITE                           READ  
               \_Interrupt_> DONE <_Interrupt_/ 
```
___Testing___
Testing procedures specified above.

### ADC
___Overview___
Handles analog input via GPIO pin and converts it into a readable digital output using the onboard analog to digital converter.

___Pseudocode___
```
typedef enum {
  TWELVE_B,
  TEN_B,
  EIGHT_B,
  SIX_B
} ADC_res

void ADC1_init(ADC_res res) {
  // Initialise ADC1 according to given resolution and datasheet
  ADC1->CFGR = res;
}

// Assumes GPIO has been set up for required pin/channel
uint16_t analog_read(uint8_t channel) {
  // Set up ADC for incoming conversion
  // Specify channel for conversion
  ADC1->CR |= ADC_CR_ADSTART;

  return ADC1_data;
}
```
___Testing___
Testing procedures specified above.

## Gantt Chart

<img width="790" height="386" alt="GANTT CHART AHHHHH" src="https://github.com/user-attachments/assets/aac1345f-4291-4677-b025-b9a880302750" />

## Pin Reservations		
<img width="989" height="289" alt="image" src="https://github.com/user-attachments/assets/d26fe00b-b56a-4aa6-8ff4-fc2625548737" />
<img width="829" height="217" alt="image" src="https://github.com/user-attachments/assets/186433a2-9273-45a7-b62c-3e096ae3d7cb" />
<img width="829" height="169" alt="image" src="https://github.com/user-attachments/assets/25106194-2ce6-4739-b175-71b0f7704971" />
<img width="829" height="145" alt="image" src="https://github.com/user-attachments/assets/71615d34-3e47-4564-b335-02c3f53035a3" />
<img width="829" height="289" alt="image" src="https://github.com/user-attachments/assets/1867d611-1227-46af-9a1b-c506f3a2854b" />


## User Instructions (Gameplay Instructions and Guide) 

1. Open briefcase, insert and twist key in keylock to start game. On GUI timer will start counting, players are given 8 minutes to complete all puzzles with time penalties applying for incorrect answers.
2. Puzzles can be solved in any order, multiple puzzles could be solved simultaneously with good teamwork. At any point if players are struggling or lost they can use the computer GUI to get 1 hint for each puzzle, to aid them in finding a solution.
3. Puzzle 1: Find button, press and hold. Robotic eyes will start blinking, find and use the decoding table to decode the message, then enter on keypad. This puzzle will require one user to look at the eyes and another to decode the pattern.
4. Puzzle 2: Using a clue hidden somewhere in the brief case will contain a code, which will be used to unlock a physical lock box. Inside this lockbox will be another code to be decoded as well as a fuse, once decoded this message can be entered using 4 flip switches, and place the fuse in the fuseholder to complete the circuit. Solution code will then be displayed on an 7 segment display, enter this number on keypad to complete puzzle.
5. Puzzle 3: Two gears will be present on board, moving the gears will move a dial that points at a range of numbers. Players must turn the gears in a specific order, this will cause an LED to light when pointed in the correct direction. This is the code to be entered on key pad to complete the puzzle.
6. Puzzle 4: The two players must communicate here to complete the puzzle, when user presses a button which turns on a laser. The other user must control the laser using a joystick, they must communicate in order to point the laser at a target, once the target is hit the puzzle is complete and code will be displayed to enter on keypad.
8. After all 4 solutions to individual puzzles are entered on keypad, the game will be complete. The timer on GUI will stop counting and success message will be displayed.





