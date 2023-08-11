# Purpose of this project
Hello everyone! Hope all is good. I've spent the past few months scouring pages just to try and get decryption keys for widevine content. There was no clear instructions on the internet and the instructions that are there need pieced togther for a proper result. What I've tried to do is condense these fragments down to a cohesive guide for people to follow. If you have any questions feel free to message me on here, my reddit or discord, contact information can be found on my profile.


## Prerequisites

I used all the mentioned tools on windows 10 x64

[Golang](https://go.dev/dl/ "Golang")

[Python 3.10.5](https://www.python.org/downloads/release/python-3105/ "Python 3.10.5") *Make sure to check "add to path" option when installing.*

[ADB Minimal Install](https://www.droidmirror.com/download/minimal-adb-fastboot-v1-4-3-zip "ADB Minimal Install") *Make sure to choose yes when it asks if you would like to add to path*

An Android 7-12 device that is able to be rooted.

#### Tools used

[Dumper](https://git.keysdb.net/Phantom/L3-Dumper "Dumper") 

[DumperV2](https://git.keysdb.net/Phantom/DumperV2 "DumperV2")

[Downey](https://git.keysdb.net/Phantom/Downey "Downey")


#### Optional

[YT-DLP](https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp.exe "YT-DLP")

[MP4Decrypt](https://www.bok.net/Bento4/binaries/Bento4-SDK-1-6-0-639.x86_64-microsoft-win32.zip "MP4 Decrypt")

[ffMPEG](https://www.gyan.dev/ffmpeg/builds/ffmpeg-release-full.7z "ffMPEG")

#### Android devices I used
DumperV1: [Truewell T95 S1](https://www.amazon.com/T95-S1-Android-Amlogic-Ethernet/dp/B07F8X1PQR "Truewell T95 S1")
Pre-rooted, around $30 USD, multiple user success of dumping keys (myself included), good for those who don't aren't technically adept to rooting.

DumperV2: [onn. Android TV 4K UHD Streaming Device](https://www.walmart.com/ip/onn-Android-TV-4K-UHD-Streaming-Device-with-Voice-Remote-Control-HDMI-Cable/636597403 "onn. Android TV 4K UHD Streaming Device")
Cheap and accesable but, needs magisk, frida module, and liboemcrypto disabler installed. You can find insturctions and scripts for installing magisk and rooting [here](https://git.keysdb.net/Phantom/onn-scripts "here"), follow up until step 7 and install the frida-server and liboemcrypto modules below from the magisk app.

**IF YOU DO USE YOUR OWN ANDROID DEVICE YOU MUST INSTALL [MAGISK](https://topjohnwu.github.io/Magisk/install.html "MAGISK") AND THE [FRIDA-SERVER MODULE](https://github.com/ViRb3/magisk-frida "FRIDA-SERVER MODULE") OR PUSH THE [FRIDA SERVER ANDROID APP](https://github.com/frida/frida/releases/ "FRIDA SERVER ANDROID APP") IF YOU DON'T NEED THE LIBOEMCRYPTO DISABLER.**

**AS MENTIONED IN DUMPER READMES, SOME DEVICES WILL CONTINUE TO USE L1 AFTER ROOT, IF UNSURE USE "DRM INFO", IF STILL ON L1 PLEASE INSTALL [LIBOEMCRYPTO DISABLER](https://github.com/Magisk-Modules-Repo/liboemcryptodisabler "LIBOEMCRYPTO DISABLER") MAGISK MODULE**

# Step 1: Preparing CDM

Connect to your android device via ADB, on the T95 this is enabled by default over wifi. Other devices may vary. 

**To enable ADB / USB Debugging follow these steps**
1. Open your device's Settings and tap About phone or About tablet. 
2. Tap Build number repeatedly until you see a notification that reads "You are now a developer." 
3. Go back to the main System menu, then tap Developer options. 
4. Tap the toggle switch in the top-right corner to enable developer options (if it's not already enabled). 
5.  Tap OK to confirm. 
6.  Tap the USB debugging toggle switch. 
7.  Tap OK to confirm. 
8. The next time you plug your device into a computer, you'll receive a prompt asking if you want to authorize USB debugging for that computer. Tap OK to confirm. 

You may now connect to your device 

`adb connect <device-ip-address>`
or if using USB
`adb devices`

On your device you should receive a notification to accept the adb connection, checkmark trust this device and then click allow.

**IF YOU HAVE MAGISK-FRIDA MODULE INSTALLED YOU MAY SKIP TO THE NEXT STEP**

**THIS METHOD *MAY* BE USED IF YOU ARE RUNNING AT AN L3 LEVEL AND HAVE ROOT ACCESS, THIS IS JUST SPECIFIC FOR THE T95S1**

Let's go ahead and push frida-server to the Android device, for the T95 S1 I used [Frida-Server 15.1.17](https://github.com/frida/frida/releases/download/15.1.17/frida-server-15.1.17-android-arm.xz "Frida-Server 15.1.17") for android. Unnzip the xz file and extract Frida-Server file. 

Change directory to where you extracted the file and enter the command `adb push frida-server-15.1.17-android-arm /sdcard/`

Once it has been pushed open up a shell with `adb shell`

Move the server to the tmp directory `mv /sdcard/frida-server-15.1.17-android-arm /data/local/tmp/`

You may get an error about chown permission, this is negligible 

Login as super user and create a new environment `su -`

 Give execute privileges to frida-server `chmod +x  /data/local/tmp/frida-server-15.1.17-android-arm`
 
 Start the server `/data/local/tmp/frida-server-15.1.17-android-arm`
 
 **When you start the server command prompt will hang, this is normal and means the program is running, do not close out and continue to step 2**
 
#  Step 2: Extracting the CDM

*If you would like to check if frida-server is running connect via adb to your device in new command prompt like in step one, then use `adb shell` and then `su` and then `ps | grep frida` this should show you the process running, if it does not something went wrong* **NOTE: MAY NOT BE APPLICABLE IF MODULE WAS INSTALLED VIA FRIDA**

Download and extract [Dumper](https://git.keysdb.net/Phantom/L3-Dumper "Dumper") for android 7-9

Download and extract [DumperV2](https://git.keysdb.net/Phantom/DumperV2 "DumperV2") for android 10-12

Open a new command prompt as administrator and change directories to the newly extracted folder and run `pip3 install -r requirements.txt` and once that's finished, start the program with `python dump_keys.py`

Now play some widevine encrpyed content on your Android device. I suggest using [https://bitmovin.com/demos/drm](https://bitmovin.com/demos/drm "https://bitmovin.com/demos/drm") playing over google chrome.

*Side note: on the T95 S1 I had to do these specific steps to get it to reliably dump. Play the video on bitmovin, stop dumper, start dumper, wait for it to finish booting, then click on the green "load" button in the top right of bitmovin while the video was still playing, so far this method has worked the best for me*

*Side note: on the onn 4k box, I had to install fx file manager and sideload launcher, push a chrome apk, install with the file manager, and run from sideloader, started on a page that wasn't bitmovin (duckduckgo.com in my case) started the script, loaded bitmovin, clicked the "load" button in the top right corner, stopped the script, started the script again, went back to duckduckgo.com, stopped the script and started repeatedly until you see an RSA fingerprint, when you see that you can go back to butmovin and play the video and dump the keys*

Once you have played the content your keys should be dumped in the dumper folder under `/key-dumps/your-device-keys/xxxx/xxxxxxxx/` and should be named `client_id.bin` and `private_key.pem`

Make a copy of these for safekeeping wherever you like but DO NOT LOSE THEM

*You can now close out of running frida server if you are running it over adb, you may also close dumper once you have verified successful dumping.*

Make sure to have file extensions visible on windows, this can be turned on by searching "show file extensions" in the windows search bar.

Now we will rename these files, `clent_id.bin` to `device_client_id_blob.` and rename `private_key.pem` to `device_private_key.` **The periods on the end is intentional as windows likes to keep file extentions, please make sure to have this exact or it will cause errors with downey! Only rename with windows, using a program such as text editor and saving under all files will still corrupt the data!**

# Step 3: Obtaining decryption keys

Download and extract [Downey](https://git.keysdb.net/Phantom/Downey "Downey") 

Open a new command prompt as administrator and change directories to the newly extracted folder

Build Downey with command `go build -o downey.exe -a main.go` once this is finished you should see `downeye.exe` in your Downey folder

Copy the `device_client_id_blob` file and `device_private_key` file to the Downey folder.

Open Firefox or Chrome and navigate back to [https://bitmovin.com/demos/drm](https://bitmovin.com/demos/drm "https://bitmovin.com/demos/drm") and open web devoloper tools (ctrl+shift+c) and click on the network tab

Play the video on the site, then in the filter urls type in `method:POST` and under the domain look for the licensing server, in this case it's `https://cwip-shaka-proxy.appspot.com/` right click on this value and copy value -> copy post data and paste this somewhere safe for now.

Next we need the MPD file, under the filter url section in the network tab now we are going to filter for `MPD` one result should show up for `11331.mpd` downoad this by double clicking or copying and saving. Rename this file to `manifest.mpd`and place it in your Downey folder

Now we have everything we need, let's go ahead and run downey with `downey.exe --lic-server "https://cwip-shaka-proxy.appspot.com/no_auth"` if everything goes successful you should get the decryption keys for the video, so for bitmovin i received

```
Decryption keys:
ccbf5fb4c2965be7aa130ffb3ba9fd73:9cc0c92044cb1d69433f5f5839a159df
9bf0e9cf0d7b55aeb4b289a63bab8610:90f52fd8ca48717b21d0c2fed7a12ae1
eb676abbcb345e96bbcf616630f1a3da:100b6c20940f779a4589152b57d2dacb
0294b9599d755de2bbf0fdca3fa5eab7:3bda2f40344c7def614227b9c0f03e26
639da80cf23b55f3b8cab3f64cfa5df6:229f5f29b643e203004b30c4eaf348f4
```
And there you have it! decryption keys for all versions of the video and one for the audio.

# Optional: How to download, decrypt, and merge Bitmovin video and audio

### Downloading

Download [YT-DLP](https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp.exe "YT-DLP") and place in a new folder or a folder of your choice.

Open a new command prompt as administrator and change directories to the folder you've placed `yt-dlp.exe`

Go back to [https://bitmovin.com/demos/drm](https://bitmovin.com/demos/drm "https://bitmovin.com/demos/drm") and open up the web devoloper tools (ctrl+shift+c) and click on the network tab. 

Play the video on the site, then in the filter urls type in `MPD` like in step 3.

Copy the url value from the search result. You can do this by: Right click -> Copy Value -> Copy URL In this case it should be `https://cdn.bitmovin.com/content/assets/art-of-motion_drm/mpds/11331.mpd`

Now in the command prompt you have opened execute the command `yt-dlp.exe --allow-unplayable https://cdn.bitmovin.com/content/assets/art-of-motion_drm/mpds/11331.mpd`

This should download two files in your yt-dlp directory, an encrpyted `mp4` video file  and an encrypted `m4a` audio file. For ease I have renamed these `encrypted.mp4` and `encrypted.m4a`

## Decrypting

Download and extract [MP4Decrypt](https://www.bok.net/Bento4/binaries/Bento4-SDK-1-6-0-639.x86_64-microsoft-win32.zip "MP4 Decrypt")

Open a new command prompt as administrator and change directories to the MP4Decrypt bin folder.

Place your `encrpyted.mp4` and `encrypted.m4a` files in this bin folder as well

Decrpyt the video by using `mp4decrypt.exe --key xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx inputfile.mp4 outputfile.mp4` where the key in this case would be one we extracted earlier, `inputfile.mp4` being the `encrytped.mp4` file we downloaded earlier and `outputfile.mp4` can be named whatever you choose, I will be using `decrypted.mp4` so for this instance it would be `mp4decrypt.exe --key eb676abbcb345e96bbcf616630f1a3da:100b6c20940f779a4589152b57d2dacb encrypted.mp4 decrypted.mp4`

Decrpyt the audio by using `mp4decrypt.exe --key xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx inputfile.m4a outputfile.m4a` where the key in this case would be one we extracted earlier, `inputfile.m4a` being the `encrytped.m4a` file we downloaded earlier and `outputfile.m4a` can be named whatever you choose, I will be using `decrypted.m4a` so for this instance it would be `mp4decrypt.exe --key eb676abbcb345e96bbcf616630f1a3da:100b6c20940f779a4589152b57d2dacb encrypted.m4a decrypted.m4a`

You should now have two playable files, the video `decrypted.mp4` and the audio `decrypted.m4a` now it's time for the final step .

### Merging

Download and extract [ffMPEG](https://www.gyan.dev/ffmpeg/builds/ffmpeg-release-full.7z "ffMPEG")

Open a new command prompt as administrator and change directories to the ffmpeg bin folder.

Place your `decrpyted.mp4` and `derypted.m4a` files in this bin folder as well

In command prompt issue the command to merge the two streams `ffmpeg.exe -i decrypted.mp4 -i decrypted.m4a -vcodec copy -acodec copy bitmovin.mp4`

Congrats, you now have a playable widevine audio/video file!

# Site Specific instructions.

Hopefully more to come!

[HBO Max](https://git.keysdb.net/Phantom/hbo-max "HBO Max")

[Udemy](https://git.keysdb.net/Phantom/udemy "Udemy")

[Hulu](https://git.keysdb.net/Phantom/hulu "Hulu")

[Disney+](https://git.keysdb.net/Phantom/disneyplus "Disney+")
# Don't feel like rooting and dumping? Purchase an L3 CDM.

Contact me on my telegram or discord (info found on my gitlab profile)

Selling for $35 USD
