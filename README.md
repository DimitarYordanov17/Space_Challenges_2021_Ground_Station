# Introduction

While at Space Challenges Bootcamp 2021 our team of 8 was assigned the task to build a ground station.  

The project would have been considered successful if we successfully captured, demodulated and deframed telemetry data from one of EnduroSat's educational cubesats - QMR-KWT.  

While our design documents describes everything on the technical side (including mission, system, subsystem requirements, design, implementation and evaluation), in this repository I will go over the step-by-step methodology used for the signal processing (which I and Dimitar were responsible for, but not limited to).

That would include even more detailed steps, than those presented in the design document, how we tested the systems, and problems faced, including the ways we overcame them.

Additionally I will also post here our results from the project altogether and our presentation which we held in front of most of EnduroSat's team and a special guest on the bootcamp - Pete Worden, former NASA Ames Research center director, and current Chairman of the Breakthrough Prize Foundation.

# Signal Processing

To successfully process the signals, we run a GNU Radio flow that appreciates 3 main consecutive steps: Receive and Save, Demodulate and Deframe, Output.

## Prerequisites

To be able to process the signal one would need to download Miniconda (download from [here](https://docs.conda.io/en/latest/miniconda.html) and turn off antivirus while downloading and installing) and then follow the steps from [here](https://gr-satellites.readthedocs.io/en/latest/installation_conda.html#installing-using-conda) to then download gnu radio companion and gr-satellites. If you did those successfully you will have GNU Radio version 3.9.2 with gr-satellites as an additional library. Additional to that, you will also have to install one additional library called gr-osmosdr (this is necessary to be able to receive RF signals live). After this is all set up, one can move to the steps below.

## Flowgraph

First, one would start with assembling the flow. This calls the GNU Radio (essentially, a python script) to initiate the flow.

![flowgraph](https://github.com/VladStoyanoff/Space_Challenges_2021_Ground_Station/blob/master/Screenshots/flowgraph.png)

QT GUI Range - All those blocks are variables fed to the flow
Osmocom Source - your live RF receiver (the antenna)  
Complex to Float - converts the format of the information received so that it could be saved to a wav file and demodulated   
QT GUI Sink - visualization of the data (not necessary, but very useful if one is testing with a graphical interface, rather than only on Raspberry Pi without a monitor. Also this is how the waterfalls you can see on the pictures lower are visualized)  
Wav File Sink - Saves the raw data to a wav file, in case we want that for further analysis  
FSK Demodulator - demodulates the data live
AX.25 Deframer - Deframes the data by the AX.25 protocol live
Message Debug - Used to print out the data as it's getting received

In the following paragraphs I will go in more detail on how each block corresponds with the rest of the flow

## Receiving + Saving + Waterfall Visualizing

![Receiving_Saving_Visualizing](https://github.com/VladStoyanoff/Space_Challenges_2021_Ground_Station/blob/master/Screenshots/Receiving_Saving_Visualizing.png)

The block responsible for receiving is Osmocom Source. The main thing we should keep in mind here are the following variables:  
* Sample rate - Anything lower than 1k will hinder the signal and we will lose important data;  
* Frequency - The frequency at which the cubesat transmits data. It should be set prior to the cubesat pass;  
* Bandwidth - 10k is the optimal bandwidht for receiving telemtry data from QMR-KWT. This was corresponded directly with the CTO of EnduroSat;  
* RF gain - We amplify the signal so that we can process it later on;  

The block responsible for visualizing the waterfall is QT GUI Sink

Not much to say here, except that the values should be identical with those on the Osmocom source to ensure data integrity. We have saved many waterfalls of different satellites which you can see in the waterfalls folder.  

The block responsible for formatting the raw data from the cubesat is the Complex to Float block  

The block responsible for saving the raw data is WAV File Sink  

This block ensures that the raw data is saved in case we need it for further analysis  

## Demodulating + Deframing

![Demodulate_Deframe](https://github.com/VladStoyanoff/Space_Challenges_2021_Ground_Station/blob/master/Screenshots/Demodulate_Deframe.png)

The block responsible for demodulating the data is the FSK demodulator.

There are many different modulations, and the cubesat manufactures decides what the modulation of the signal will be. Modulation is the process of converting data into electrical signals and is useful for optimizing transmission.

The block responsible for deframing the signal is AX.25 deframer.  

Similar to the demodulation, the communication protocol is determined by the cubesat manufacturer. This protects the signal from being decoded by anyone. Communication protocols that are open-source are usually used for educational purposes.  

## Output

![PDU_Packet_Output](https://github.com/VladStoyanoff/Space_Challenges_2021_Ground_Station/blob/master/Screenshots/PDU_Packet_Output.png)

Finally we output the data into PDU packets.

This happens directly in GNU Radio and some sample telemetry data can be seen in the folder decoded_data.

## Results

[Our presentation can be found here](https://docs.google.com/presentation/d/16nQSsULeZnAyRO5knKby9Yxyl1VgrfHguLCeWdUZ5Wo/edit#slide=id.geee1d22ef7_0_153)  

Waterfall from QMR-KWT:

![QMR_KWT_waterfall](https://github.com/VladStoyanoff/Space_Challenges_2021_Ground_Station/blob/master/waterfalls/QMR_KWT_waterfall.png)

Additional waterfalls from different cubesats can be found in the waterfalls folder

PDU packet from QMR-KWT:

![QMR_KWT](https://github.com/VladStoyanoff/Space_Challenges_2021_Ground_Station/blob/master/Screenshots/QMR_KWT.png)

The Ground Station in action:

https://user-images.githubusercontent.com/88051591/137527052-ba6f591f-a93a-42f8-9a56-8607a9372a54.mp4



