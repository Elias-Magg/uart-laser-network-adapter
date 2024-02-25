# uart-laser-network-adapter
This is a proof of concept project to determine the viability for communication through ligth and specifially directed light in the form of lasers.

## Project prerequisites
1. A solution has to be implemented using readily available hardware and software (no proprietery drivers or pcbs)
2. The communication has to be bi-directional

## This concept is nothing new
From tv remotes to optical fibers light has been used to transfer data for a decades now.
The approach of this project however is in long range communication . One application of this could be deep space communication.
In fact Nasa conducted such an experiment not that long ago .
You can read more on that [here]([https://pages.github.com/](https://www.jpl.nasa.gov/news/nasas-deep-space-optical-comm-demo-sends-receives-first-data)https://www.jpl.nasa.gov/news/nasas-deep-space-optical-comm-demo-sends-receives-first-data)

## Part 1 - Hardware 
Searching the internet for similar projects to what I had in mind I could not find something that fulfilled my requirements.
It was apparent that I had to start from scratch. 
The easiest way I came up with to start making a laset network interface was to find an already existing networking protocol and couple it with custom hardware.
The protocol I ended up with was UART since its high bandwidth, lack of dependency on an accurate clock and error checking/correction capabilities seemed to fit right in with what I had in mind.
As an extra bonus this protocol could also used for regular tcp/ip communication meaning I could benchmark my solution with readily available network tools such as iperf.
The uart capable device I ender up using was a regular raspberry pi. 
After a lot of digging and tutorials on electronics (I am not an electrical engineer) the schematic for the custom adapter I ended up with was this: 

![image](/assets/images/schematic.png)

It is a simple photodiode - op amp circuit for the receiver side and a transistor - laser diode for the transmitter side.
In essence on the receiver side the first op amp amplifies the weak voltage the photodiode produces when exposed to light 
and the second divides the voltage with the help of a sensitivity potentiometer to values that the raspberry pi can interpret as 0s or 1s.
The trasmitter side is self explanatory its just a transistor that works as a relay switching the laser on and off.
The assembled prototype board looks something like this:

![image](/assets/images/proto-board-2.png)

![image](/assets/images/proto-board-1.png)

## Part 2 - Software
After lining up the two devices (which seems easier than it actually is)

![image](/assets/images/lining-transiever.png)

I needed to figure out what a good baudrate value would be. To do this I used a program called minicom to send regular string data between the two devices.
Increasing the baudrate until the strings were transmitted strings started showing artifacts I was able to settle at a baudrate of just 9600. Yikes.
A typical baudrate for uart is 115200 much much higher than what i was able to produce.

![image](/assets/images/terminal-1.png)

![image](/assets/images/terminal-2.png)

![image](/assets/images/terminal-3.png)

## Part 3 - What can be improved
1. Lining up the two devices was extremely tideous. In a second iteration I would for sure incorporate some kind of motorised precision alignment system.
2. Increase baud rate. The receiver cirquit was made with components that could on paper handle pulses in frequencies of a couple of MHz .
   The issue thus was the transmitter and specifically the laser diode. It comes with its own power management circuitry to ensure that the laser diode doesnt burn out .
   This is a double edged knife since it means that I didnt have to worry about powering the diode but at the same time the circuit was probably not designed for high frequency pulsing
   and as result bottlenecked my solution.
