# robotic-manipulator
Arduino program that controls a 5-jointed robotic manipulator

This is a team project for the iCORE Stein Lab at Louisiana State University. 
The operator tells the program where a joint needs to move (in degrees) and how fast it should move (in degrees per second). The Arduino Mega 2560 translates that request into two things a stepper motor driver understands: how many pulses to send and how much time to leave between pulses.

HOW TO USE:
Place the arm at its physical zero and use "H" to record that position as home. Send "J3", "F22.5", "G15" each on their own lines. 
J3 selects joint 3. Single-joint commands apply to the selected joint.
F22.5 sets joint 3 to a speed of 22.5 degrees per second.
G15 requests an absolute position of 15 degrees from home. You can also use "A" to move the joint from the current estimated position (ex. A47 would move joint 3 47 degrees from 15 degrees).

Using gear ratios, the functions pulsesPerDegree() and moveTo() convert the angle into a pulse count.
Function prepareRamps() decides when to send the pulses. It uses a 0.5 second ramp to the requested speed, then mirrors the timing to slow down. For this particular 15 degree move, starting from home, joint 3 accelerates over the first 5.625°, travels 3.75° at the requested speed, and decelerates over the final 5.625°. The move still calls for the calculated pulse count. Distance determines the pulse count, while acceleration determines their spacing.
After prepareRamps() calculates the timing, a hardware timer triggers ISR(TIMER1_COMPA_vect), which is an interrupt routine to switch STEP pins to high or low at scheduled times. loop() can still read serial commands during the move, so "S" can request a quick stop.

After testing, the robotic manipulator can successfully move to all angle limitations at all available speeds with satisfactory accuracy. 

NOTE:
The program estimates its position by calculating how many pulses it sends. If a motor misses steps or the configured gear ratio is inaccurate, the reported angle can differ from the physical angle.
