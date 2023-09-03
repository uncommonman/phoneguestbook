# guest book telephone project.
## Level of ambition
Please note that this was never intended to be a well documented project for further development or education. It was a quick lash-up for my youngest daughter's wedding. 
I didn't document progress. No git was installed. This documentation is post factum for people who can accept "inspiration" rather than "instruction".

## More details
The short overview is:

I took inspiration from this project: https://www.instructables.com/Raspberry-Pi-in-Rotary-Phone/

But I had a black telephone, so I did not use the old handset - changing loudspeaker and microphone etc, but instead I could use a handset meant to be used with a mobile phone, such as 
--placeholder for photo--
( https://www.amazon.com/Retro-Phone-Handset/s?k=Retro+Phone+Handset )

I got a small USB sound card and connected the handset to that, to avoid using the Raspberry Pi built in audio which in my experience can behave in "interesting ways".

As an extra backup I got a USB memory stick where I copied the sound files to. It is not a good idea to ask people to redo the wedding/party because the SD card got corrupted. (Sending the files to the cloud is of course an option if the WiFi is good - but setting up WiFi at the party site can be tricky on a headless system.)

I used a Raspberry Pi 4, because I had one laying around. The performance is probably not needed, but it was nice to have all the USB ports etc. And it is also nice and fast to do the code development on, with screen, mouse and keyboard attached.

I used Node Red as the primary programming environment, since it is great for this kind of IoT work, and found some suitable packages for handling the recording and interaction with the handset. I had to develop some shell scripts to handle some weaknesses in the packages. For example: the recording output filename was not dynamically configurable from the Node Red program, so after each new message (file), I copied it from the default name to a new name (and copied it over to the backup USB).

(I should of course add the code here, sometime in the future...)

I figured out how the mechanics of the phone worked, and there was an unused set of pins in the hook switch (operated by the handset) that I could use. I used the circuit from the instructable (mentioned above), including a resistor, for the switch. There seems to be good timing in the code, so even if there were any bounces in the switch, it did not lead to multiple "calls". It just worked.
(photo of pins to solder to, to be added, for fellow countrymen who happen to have one similar, https://sv.wikipedia.org/wiki/Diavox#/media/Fil:Diavox_1975.jpg , laying around)

So on this schematic: https://content.instructables.com/FSH/ERAR/HXMUWMGR/FSHERARHXMUWMGR.png
I did not use the “rotary” and “dial” part of the circuit, because it was a push-button phone, and I won’t be using it to make calls.

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


