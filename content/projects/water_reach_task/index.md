+++
title = "Water Droplet Reaching Task for Mice"
date = 2025-08-05T22:27:02-04:00
draft = false
featured_image = "/images/wdrt_cover.jpg"
description = "Rig, sensors, hardware, software for water droplet reaching task"
github_url = "https://github.com/emodom468/water_reach_task"
+++


Documentation of RIG and software. 

## Bill of Materials

I am trying to find all components of the rig. 

{{< rawhtml >}}
<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vS9a9wGIX4Bf6rD-zuoecSxzVqqyblmre1gvwzgA6rQGxJ3U0qr9HTC3ILTakiOUpVooNBT7ZXMeznA/pubhtml?gid=0&single=true" width="100%" height="400" frameborder="0"></iframe>
{{< /rawhtml >}}

- cheaper breadboards may actually make errant contacts internally - the metal shaft that holds the pin can shift around. we noticed this is a problem when we make the conductance sensor. 

## Initial Computer Setup 
#### Softwares to download
I provide links to help find correct download, but it is possible they will go out of date and you will need to find the download yourself. 

Download (required):
- [Arduino IDE](https://www.arduino.cc/en/software/)
    - Install [CapacitiveSensor](https://github.com/PaulStoffregen/CapacitiveSensor/blob/master/CapacitiveSensor.cpp) library for capacitance based rig. 
    - you can do this directly in Arduino IDE by **Library Manager > search "capacitivesensor" > select version by Paul > Install**

   {{< rawhtml >}}
   <img src="/images/cap_sensor_library.jpg" width="250" height="250" style="margin-left: 20px;">
   {{< /rawhtml >}}
   - or you can copy the .cpp file from link above to the correct location .. i have note on this somewhere I think

- Code interpretter
    - I use [VSCode](https://code.visualstudio.com/download)
    - or [Cursor](https://cursor.com/en/downloads) which is built on VSCode. Cursor is useful because it can take the full code base into account when making changes, helping to maintain compatibility.
- Camera control software
    - [AMCap.exe](https://docs.arducam.com/UVC-Camera/Appilcation-Note/Software-Instruction/Windows-AMCap/#focus-mode) for arducam series
    - [Pylon API](https://www.baslerweb.com/en-us/shop/a2a1920-160umbas/#software=2012599532) for Basler cameras
- [VLC media player](https://images.videolan.org/vlc/) for viewing videos
- ffmpeg for video compression

- Anaconda ? do you actually need to install this or is vscode pretty complete when you downoad?

Download (recommended):
- Google Chrome
- Google [Chrome Remote Desktop](https://chromewebstore.google.com/detail/chrome-remote-desktop/inomeogfingihgjfjlpeplalcfajhgai?hl=en&pli=1)
    - easy remote monitoring of behavior
- [Dropbox](https://www.dropbox.com/install)
    - to sync text data output across computers. these files are small in size so I wouldnt worry about using dropbox for them. however, I do not recommend putting large gb videos on dropbox. 
- [Slack](https://slack.com/downloads/windows) 
    - to communicate with yourself
- Github (highly recommend)
    - to maintain latest version of code 
    - use app or CLI

## Day-to-Day operation
The central control script is a jupyter notebook which is organized into cells and allows for user input. 

## Capacitance Circuit

Documented by Vera McCoy. 

## Conductance Circuit


![Conductance Rig with 2P Integration](/images/circuit_conductance.jpg)
Documented by Vera McCoy. 


## Random Tips I Learned
- i am interested in using micropython to control microcontrollers (arduino) rather than arduino IDE bc it might integrate with python better , but I have not tried it. 
- arduino code is just c. 

## Technical Implementation

- **title** : text

text

## Arducam 
Setup on [version]

Open AMCAP

Options > Video Capture Pin
- MJPG
- 640 x 480
- Apply
- OK

Options > Video Capture Filter>camera control
- Unlock auto, Exposure -10
- Apply
- OK


## Basler Camera API
This is the current operating protocol that must be done for each day/session in pylon API (the app).I eventually want to use pypylon the python library to automate and better integrate this camera control with the rest of the script! 

### Connections
1. P2.4 NIDAQ (behavior PC) to Line 2 on Basler cameras
2. 40640930 — side camera
3. 40640920 — front camera

### Pylon settings (manual) to translate to Python
- Select device: 40640930
- Image Format Control
    - Binning horizontal mode: sum
    - Binning horizontal: 4 (max value)
    - Binning vertical mode: sum
    - Binning vertical: 4 (max value)
    - Note: Binning reduces exposure time/gain requirements by increasing light per pixel.
    - Adjust width to 380
    - Default after 4x4 binning: width 480, height 300
    - Offset X: value specific to camera
    - Pixel format: Mono8
- Acquisition Control
    - Exposure time (µs): 2000
    - Acquisition frame rate: 100
    - Enable acquisition frame rate: On
    - Trigger selector: Frame Start
    - Trigger mode: On
    - Trigger source: Line 2
    - Trigger activation: Rising Edge
    - Set identical on both cameras
- Analog Control
    - Gain (dB): 10–15 (front), 7–8 (side)
    - Note: Analog gain increases sensor sensitivity by drawing more current (more power).
    - Warning: Setting gain to 30 previously froze the PC (likely USB hub power issue).
- Many of the above can be verified under the Features > Basic tab.

### Recording settings (Pylon)
- Window > Recording Settings
- Select: "Sequence of still images"
- Output format: TIFF (chosen over BMP for better lossless compression and familiarity)
- Recording buffer size: 1000 frames
- Record a frame every: 1 frame
- Stop recording after: 20 minutes
- Output folder: set per session
- Preallocate: On

After selecting animal:
- Create a folder for the session data
- Create subfolders for side/front
- Consider flat vs nested structure. Flat may be easier later if folder names encode metadata.

Notes/TODO:
- Assess how much of Pylon's default buffer/save behavior maps to `pypylon`.
- First attempt: set and save these params in Pylon GUI; use Python to send Frame Start trigger at 100 Hz.

## Analysis with DeepLabCut

