# Introduction

While at Space Challenges Bootcamp 2021 our team of 8 was assigned the task to build a ground station.  

The project would have been considered successful if we successfully captured, demodulated and deframed telemetry data from one of EnduroSat's educational cubesats - QMR-KWT.  

While our design documents describes everything on the technical side (including mission, system, subsystem requirements, design, implementation and evaluation), in this repository I will go over the step-by-step methodology used for the signal processing (which I and Dimitar were responsible for, but not limited to).

That would include even more detailed steps, than those presented in the design document, how we tested the systems, and problems faced, including the ways we overcame them.

Additionally I will also post here our results from the project altogether and our presentation which we held in front of most of EnduroSat's team and a special guest on the bootcamp - Pete Worden, former NASA Ames Research center director, and current Chairman of the Breakthrough Prize Foundation.

# Signal Processing

To successfully process the signals, we run a GNU Radio flow that appreciates 3 main consecutive steps: Receive and Save, Demodulate and Deframe, Upload.

The below steps will explore 2 ways of doing the aforementioned:

**The first way is by saving the file received and deframing it later. **

The pros of this is that we will have the data saved on a wav file and can use it for more considerate analysis.

The cons is that in case we need the decoded data fast, this way will just not suffice. For that reason we explored a second way:  

**The second way is bypassing the saving part and directly decoding the information live as the satellite passes.**  

The pros here are that you get decoded data fast.  

The cons are that you don't have a file with the raw data recorded and the only data you get out is the decoded one. This would pose problems in case you need to analyze the raw data.  

We will explore each method in detail.  

# Method 1

## Prerequisites

To be able to process the signal one would need to download Miniconda (download from [here](https://docs.conda.io/en/latest/miniconda.html) and turn off antivirus while downloading and installing) and then follow the steps from [here](https://gr-satellites.readthedocs.io/en/latest/installation_conda.html#installing-using-conda) to then download gnu radio companion and gr-satellites. If you did those successfully you will have GNU Radio version 3.9.2 with gr-satellites as an additional library. Additional to that, you will also have to install one additional library called gr-osmosdr (this is necessary to be able to receive RF signals live). After this is all set up, one can move to the steps below.

## Flowgraph

First, one would start with assembling the flow. This calls the GNU Radio (essentially, a python script) to initiate the flow.

![flowgraph](link to gnuradio flowgraph)

QT GUI Range - All those blocks are variables fed to the flow
Osmocom Source - your live RF receiver (the antenna)  
Complex to Float - converts the format of the information received so that it could be saved to a wav file and demodulated   
QT GUI Sink - visualization of the data (not necessary, but very useful if one is testing with a graphical interface, rather than only on Raspberry Pi without a monitor. Also this is how the waterfalls you can see on the pictures lower are visualized)  
Wav File Sink - Saves the raw data to a wav file, in case we want that for further analysis  
FSK Demodulator - 

Wav File Sink - Saves the file

## Demodulating + Deframing

The wav file generated in the first graph is then fed to the second flow which demodulates and deframes the signal. 






To receive data successfully with our Yagi-Antenna our team had to ensure several things:

1. Proper design of the antenna  
2. Proper tracking throughout the cubesat's pass  
3. Proper flow diagrams within GNU Radio (our signal processing software)  

I and Dimitar were responsible mainly for the third point mentioned so that is what I will go over in-depth in this repository. The rest can be found in the design document.

The flow diagram used to capture the signal is the following:




