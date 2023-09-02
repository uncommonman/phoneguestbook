(text dump from e-mail, so wording may be strange) 
# guest book telephone project.

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
(photo of pins to solder to, for fellow countrymen who happen to have one similar, https://sv.wikipedia.org/wiki/Diavox#/media/Fil:Diavox_1975.jpg , laying around, to be added)

So on this schematic: https://content.instructables.com/FSH/ERAR/HXMUWMGR/FSHERARHXMUWMGR.png
I did not use the “rotary” and “dial” part of the circuit, because it was a push-button phone, and I won’t be using it to make calls.
