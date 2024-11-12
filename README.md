Digital Oscilloscope Using FPGA

Basys 3 Digilent Board, 100Mhz

An essential tool of analog electronics is the oscilloscope, which acquires arbitrary external signals and displays them cleanly to the user in real time.

The ADC built into the board can acquire 12-bit samples at 1 MSPS. All other components of the oscilloscope operate at a clock rate of 104 MHz
The user controls the oscilloscope using the switches and buttons on the Nexys 4 FPGA board. The oscilloscope uses block RAM, ROM and flip-flops for storage. ROM is used to display the font for the Text display module, and block RAM is used to store samples of the analog input signals 

So we give analog input to ADC of FPGA, it converts it into a 12 bit value at the rate of 	1MSPS
And the values are stored in buffer 
The ADC Controller module accepts samples from the ADC block. On each clock cycle, each input sample is copied directly out on the raw outputs. 

The non-raw outputs are subsampled and sent to the Buffer modules; 
The subsampling rate is determined by the settings sent from the User Control block.
Downsampling the data at this stage allows us to scale the time axis while avoiding the complexity of doing it once the data is already in the buffer.

The trigger system processes the incoming data stream from either channel and asserts the isTriggered signal for one clock cycle when the input data is on a rising edge and crosses the user-defined trigger threshold (the trigger threshold is one of the settings outputted by the User Control block). 
The trigger system consists of two parts: the Edge Type Detector module, which senses whether the signal is on a rising edge, and the Trigger Rising Edge module, which checks the signal level and performs the triggering. So when the input signal crosses the trigger level , a trigger signal is generated which is given as input to buffer


The Buffer module stores ADC samples for further processing. 
The Buffer instantiates two BRAMs, an active BRAM and a locked BRAM, that it uses as ring buffers. 
Incoming samples are stored in the active BRAM. When the isTriggered signal from the Trigger system goes high, the address of the most recent sample in the active BRAM is stored in the triggerSample register. We instantiate one Buffer module for each data channel. A helper module, the Buffer Selector module, interfaces between the display system and the Buffer modules and produces an activeBRAMSelect signal that controls which BRAM is active and which is locked. At the start of drawing each display frame, the activeBRAMSelect signal toggles. This ensures that the data in the locked BRAM is not altered while the frame is being drawn. The Buffer module only supports read access to the locked BRAM. Upon sampleRequests from the Display block, the Buffer module outputs the requested sample three clock cycles after the sampleRequest is received.
