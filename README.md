# guest book telephone project.
## Level of ambition
Please note that this was never intended to be a well documented project for further development or education. It was a quick lash-up for my youngest daughter's wedding. 
I didn't document progress. No git was installed. This documentation is post factum for people who can accept "inspiration" rather than "instruction".

## More details
The short overview is:

I took inspiration from this project: https://www.instructables.com/Raspberry-Pi-in-Rotary-Phone/

But I had a black telephone, so I did not use the old handset - changing loudspeaker and microphone etc, but instead I could use a handset meant to be used with a mobile phone, such as  ![Handset box](/z_15_handset_package.png) 
(https://www.prylstaden.se/svart-telefonlur-med-spiralsladd-till-iphone or https://www.amazon.com/Retro-Phone-Handset/s?k=Retro+Phone+Handset or similar)

I got a small USB sound card and connected the handset to that, to avoid using the Raspberry Pi built in audio which in my experience can behave in "interesting ways".

As an extra backup I got a USB memory stick where I copied the sound files to. It is not a good idea to ask people to redo the wedding/party because the SD card got corrupted. (Sending the files to the cloud is of course an option if the WiFi is good - but setting up WiFi at the party site can be tricky on a headless system.)

I used a Raspberry Pi 4, because I had one laying around. The performance is probably not needed, but it was nice to have all the USB ports etc. And it is also nice and fast to do the code development on, with screen, mouse and keyboard attached.

I used Node Red as the primary programming environment, since it is great for this kind of IoT work, and found some suitable packages for handling the recording and interaction with the handset. I had to develop some shell scripts to handle some weaknesses in the packages. For example: the recording output filename was not dynamically configurable from the Node Red program, so after each new message (file), I copied it from the default name to a new name (and copied it over to the backup USB).

(I should of course add the code here, sometime in the future...)

I figured out how the mechanics of the phone worked, and there was an unused set of pins in the hook switch (operated by the handset) that I could use. I used the circuit from the instructable (mentioned above), including a resistor, for the switch. There seems to be good timing in the code, so even if there were any bounces in the switch, it did not lead to multiple "calls". It just worked.
(photo of pins to solder to, to be added, for fellow countrymen who happen to have one similar, https://sv.wikipedia.org/wiki/Diavox#/media/Fil:Diavox_1975.jpg , laying around)

So on this schematic: https://content.instructables.com/FSH/ERAR/HXMUWMGR/FSHERARHXMUWMGR.png
I did not use the “rotary” and “dial” part of the circuit, because it was a push-button phone, and I won’t be using it to make calls.

## Components I used

- Diavox phone
- Handset for mobile phones
- 3,5mm headset to mic+stereo headphone split cable (TRRS to TS+TRS)
- USB sound card
- Short USB cable (for freer positioning of components inside phone)
- Raspberry PI 4 (overkill ?, but I had one available, and did all development on it).
- Power supply. Replacing the old phone's phone cable to the wall.
- USB memory stick (for backup, and transfer of files to be given as a gift to the married couple. Just remember to have a new one available to actually give away, if you like me had set it up to mount explicitly. Without it the right one, the system didn't boot properly...)

 ![Packages of some components](/z_00_packages_of_components.png)

Optional:
- Lock and locking wire. It would have been sad to lose it in the night, or have it unknowingly be yanked from the wall power.
  
During programming:
- Micro-HDMI - HDMI adapter cable suitable for Raspberry Pi 4
- HDMI cable
- Keyboard + Mouse
- Screen 

# Warning / caveat
Test your finished project/phone for changes in network access. You've probably built it at home/in your workshop with (good) access to one Wifi network. If you bring it with you to another venue the network is going to be different/non-existing. Verify that it still works as it should. The version of RPi and OS I used was a bit picky, so I turned off the networking in the phone (RPI) before we took it to the wedding. Another solution is of course to bring an access point with you that preserves the networking environment the RPi can see...
(I also brought screen, mouse, keyboard, spare parts etc in my car, because I'm an engineer, and believe that Murphy was an optimist...)

The problems I had may be due to the OS waiting for network connection, and it may be solved by setting ”S6 Network at Boot” in raspi-config to not wait for network, but I would definitely verify before relying on it.

## Files
- [flows.json](/flows.json)  An export of the flows in my node red. Mostly samples and debug stuff. Only one flow (that starts with Pin 7) is used, as far as I can remember.
- [do_audiobackup](/do_audiobackup) Start background backup of audio files and quickly return.
- [do_audiobackup2](/do_audiobackup2) Do actual backup.
- [prompts16.wav](/prompts16.wav) Prompt message we used at the wedding.
- [do_rename](/do_rename) A limitation with the node used for recording is that it uses the same file name every time. This program renames the previous recording (to make room for a new) and plays the recorded greeting. So, notice that the time stamp is for the renaming and not the recording. (If you want to re-construct the actual recording timestamp, look at the name of the previously recorded message :) ). Could of course be refined to get time stamp of previous recording and turn that into a new name, but...

## Node red packages I have installed (may be some surplus)
- node-red-contrib-micropi
- node-red-contrib-play-audio
- node-red-node-pi-gpio
- node-red-node-ping
- node-red-node-random
- node-red-node-serialport
- node-red-node-smooth

## Swedish encryption
Occasionally there are Swedish words scattered amongst the files. 
- *Lur* means handset/receiver
  
## General flow in Node Red
 ![Flow](/flow.png)
From left to right.
1. Something happens at Pin 7. (Which is connected to the handset "sensor").
2. The Switch node figures out if it was lifting or putting down the handset that happened.
3. The upper flow is for handset lifted.
    1. Rename previous recording from default name to one with a timestamp.
    2. Set input to true for recording stage.
    3. Start recording.
    4. Kick off backup of audio to USB stick.
4. The lower flow is form handset put down again.
    1. Set input to false to stop recording
    2. Stop recording
    3. Kick off backup of audio to USB stick.

This is a very simple and brutal approach. Worked for me.
The do_audiobackup has two stages. The first just kicks off the second one and returns as quickly as possible to the flow. The second one runs in the background and does any copying. If a file has already been copied, and exists on the target media, then it is not copied again. Works for the relatively limited number of greetings for one night. Room for improved sophistication if you expect to have thousands of files...

## Misc

I believe I used this to have node-red running on boot

    $ sudo systemctl enable nodered.service

## Getting in

Overview:
 ![01](/z_01_overview.png)

Loosen these two (deeply recessed) screws:
 ![02](/z_02_remove_screws.png)

Take off lid:
 ![03](/z_03_lid_off.png)

 Lift from front (loosen front of circuit board, thumb(s) down in slot or similar):
 ![04](/z_04_lift_front.png)

Board with dust shield off. Notice that I had removed all original cabling between board, handset, wall cable, and ringer system. 
 ![05](/z_05_dustshield_off.png)

 Where I attached the "sensor" wires:
 ![06](/z_06_handset_switch_pins.png)
 Close up:
 ![07](/z_07_handset_switch_pins_closeup.png)

 What it looks like with board gone:
 ![08](/z_08_buttons_removed.png)
 
 To remove back part, first poke screws out:
 ![09](/z_09_poke_out_screws_1.png)
 ![10](/z_10_poke_out_screws_2.png)
 ![11](/z_11_push_to_open_1.png)

 Push from sides to open
 ![12](/z_12_push_to_open_2.png)
 ![13](/z_13_push_to_open_3.png)
 ![14](/z_14_fully_opened.png)
 
 


