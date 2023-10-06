# Quality Audio Streaming Setup Guide with Raspberry Pi, Icecast, and Darkice

This repository provides all the tools you need to convert your vinyl records into a high-quality digital audio stream, making your analog collection accessible across a wide range of digital platforms. At the heart of this setup is the Raspberry Pi, a small and affordable computer that serves as the streaming server. 

I use a PHONO PP400 amplifier and a Behringer UCA202 capture device, but this method should work with every other Raspberry-compatible capture device and with any other amplifier.

![](https://i.ibb.co/pKLs9nC/image.webp)


### How It Works:

1. **Audio Capture**: Your turntable's audio output is first routed through the PHONO PP400 amplifier, which prepares the analog signal for digital capture.
  
2. **Digital Conversion**: The amplified signal is then fed into the Behringer UCA202 capture device, where it's digitized and sent to the Raspberry Pi.

3. **Streaming**: On the Raspberry Pi, two critical software components come into play:
    - **Darkice**: This live audio streamer captures the digital audio and encodes it into a streamable format.
    - **Icecast**: This streaming media server takes the encoded audio from Darkice and broadcasts it, making it accessible to listeners over the internet.

### Setup Options:

- **Quick Start**: For those eager to get started, a pre-configured Raspberry Pi image is available for download. Simply flash it onto a Micro SD card, insert the card into your Raspberry Pi, and you're ready to stream.
  
- **Manual Configuration**: For users who prefer to fine-tune their setup, step-by-step instructions are provided for configuring Darkice and Icecast, as well as identifying and setting up your audio capture device.

After that, you will have a stream on your local network which you can access with multiple programs such as iTunes, VLC Player, or Chrome. From these programs, you can also stream your audio to multiple rooms using Google Chrome or AirPlay 2.

<br/><br/>


## Step-by-Step Guide


### Step 0: Acquire Hardware

- **Capture Device**: Behringer UCA202
- **Amplifier**: PHONO PP400
- **Server:** Raspberry Pi 3 and Micro SD Card (8GB or 32GB) - (8GB is enough)

Ensure that you have the above-mentioned hardware components before proceeding with the setup.


---

### Step 1: Download

Download the Raspberry Pi Imager from [Raspberry Pi Software](https://www.raspberrypi.com/software/).

![Raspberry Pi Software](https://assets.raspberrypi.com/static/md-bfd602be71b2c1099b91877aed3b41f0.png)

Also, download the preconfigured image for your project from GitHub: [Download Image](https://github.com/quebulm/Raspberry-Pi-Vinyl-Streamer/).

....Unpack with Z Unpack.....

---

### Step 2: Configure

1. Insert your Micro SD card into your computer.
2. Open the Raspberry Pi Imager.

- **Choose OS**: Select "Custom Image" in the dropdown menu. Select the Downloaded preconfigured image. 
- **Choose Storage**: Select your SD card as the storage destination.
- **Settings**: Click on the Settings tab and input the following information:

  - Hostname: `vinylstream`
  - User: `vinyluser` # Is no longer needed after the easy installation
  - Password: `setyourpassword` # Is no longer needed after the easy installation
  - Input your Wi-Fi information.
  
  <img src="https://i.ibb.co/tDW8Qrk/Bildschirmfoto-2023-10-06-um-16-20-48.png" style="max-height:600px; background-color:white">

 
3. Save these settings.

4. Click on "Write" and wait for the process to complete.

---

### Step 3: Try it Out

1. Insert the Micro SD card into your Raspberry Pi.
2. Connect the Raspberry Pi to a power source.
3. Connect the capture card to the Pi.

Wait for 3-5 minutes, then try to open this page in your browser: [http://vinylstream:8000/](http://vinylstream:8000/)

That's it! You can now stream your music from [http://vinylstream:8000/stream.mp3](http://vinylstream:8000/stream.mp3).



#### Here is an example with the VLC App:

<a href="https://ibb.co/WWD14Kz"><img src="https://i.ibb.co/xSmQt5g/IMG-0749.png" width="400" height="auto" alt="IMG-0749" border="0"></a>

#### And here's one with iTunes (Music):

Press inside the iTunes (Music) App: ⌘U (Command U)  
Then type in http://vinylstream:8000/stream.mp3

<a href="https://ibb.co/tmGcPNb"><img src="https://i.ibb.co/f1gtGKC/Bildschirmfoto-2023-10-02-um-16-40-17.png" alt="Bildschirmfoto-2023-10-02-um-16-40-17" width="400" height="auto" border="0"></a>
<a href="https://ibb.co/4FmHkDk"><img src="https://i.ibb.co/sgmZ7D7/Bildschirmfoto-2023-10-02-um-16-40-56.png" alt="Bildschirmfoto-2023-10-02-um-16-40-56" width="400" height="auto" border="0"></a>


---

<br/><br/><br/><br/><br/>


Advanced Configuration (if you want to Configure the Raspberry PI yourself)
--- 


### Step 1: Identify the Capture Device

```bash
# Lists audio devices
arecord -l
```

Look for the output that identifies your Behringer UCA202. For example:

```
**** List of CAPTURE Hardware Devices ****
card 1: CODEC [USB Audio CODEC], device 0: USB Audio [USB Audio]
```

Note down the `card` and `device` numbers as you will need them for configuration (e.g., `hw:1,0`).

---

### Step 4: Test Audio Capture

```bash
# Captures 10 seconds of audio and saves it as test.wav
arecord -D hw:1,0 -d 10 test.wav
```

The command will record 10 seconds of audio from your capture device. Play the `test.wav` file to check the audio quality. If everything sounds good, proceed with configuring Icecast.

To manually set the sample rate, you can use:

```bash
# Sets the sample rate and captures 10 seconds of audio
arecord -D hw:1,0 -d 10 -f S16_LE -r 48000 test.wav
```

For the Behringer UCA202 for example, the max sample rate is 48 kHz 

---

### Step 5: Install Darkice and Icecast

Before we dive into the configuration part, it's crucial to install and understand the role of two essential software components for this setup: Darkice and Icecast.

#### Why Darkice?

Darkice is a live audio streamer that captures audio from your specified device (in this case, Behringer UCA202) and encodes it into a streamable format. It acts as the bridge between your audio capture device and the Icecast server, capturing audio in real-time and sending it to Icecast for distribution.

#### Why Icecast?

Icecast is a streaming media server that will broadcast the audio stream to listeners. It's highly configurable and handles multiple clients, allowing you to stream audio to numerous listeners simultaneously. Icecast takes the encoded audio feed from Darkice and makes it accessible over the internet.

#### Installation

If you haven't installed these yet, you can do so with the following commands:

For Darkice:

```bash
sudo apt-get install darkice
```

For Icecast:

```bash
sudo apt-get install icecast2
```


### Darkice Configuration

- Open the Darkice configuration file, usually named `darkice.cfg`, in a text editor.
  
    ```bash
    sudo nano /etc/darkice.cfg
    ```
  
- Insert the configuration code provided in the referenced Git repository. This code typically sets the input device, sample rate, and other parameters tailored for optimum performance with your Behringer UCA202 capture device.

    **Note**: The Git repository is your go-to source for a sample configuration. Simply copy-paste the code into your `darkice.cfg` file.

---

### Starting Darkice


#### Without System Service

If you dont want to set Darkice as a system service, you can manually start it using the custom configuration file:

```bash
# Starts Darkice with the custom configuration file
darkice -c /path/to/darkice.cfg
```

In this case, you will need to run this command each time you wish to start Darkice. Make sure to replace `/path/to/darkice.cfg` with the actual path to your Darkice configuration file.

Either method will initiate Darkice, allowing it to capture audio based on the parameters set in your configuration file and send it to the Icecast server for streaming.    

#### Running as System Services (Optional)

If you find yourself frequently using Darkice and Icecast, setting them up as system services can automate the startup process and help manage the applications more efficiently.


To set up Darkice as a system service, you can create a custom systemd service file:

1. Create a new systemd service file for Darkice.

    ```bash
    sudo nano /etc/systemd/system/darkice.service
    ```

2. Add the following content:

    ```ini
		[Unit]
		Description=Darkice live audio streamer
		After=default.target
		
		[Service]
		ExecStartPre=/bin/sleep 10
		ExecStart=/usr/bin/darkice -c /etc/darkice.cfg
		Restart=always
		User=vinyluser
		
		[Install]
		WantedBy=multi-user.target
    ```

    **Note**: Replace `/etc/darkice.cfg` with the actual path to your Darkice configuration file and `vinyluser` with the user you want the service to run as.

3. Reload the systemd daemon and enable the service.

    ```bash
    sudo systemctl daemon-reload
    sudo systemctl enable darkice
    ```

### Starting Icecast

```bash
sudo systemctl start icecast2
sudo systemctl enable icecast2 # start on boot

```

#### Icecast as a System Service

Icecast usually installs itself as a service by default. If for some reason it isn't, you can create a custom systemd service file similar to Darkice.

### Final Test

1. Connect to your stream using a web browser (Chrome) or a streaming app (iTunes / VLC).
2. Listen to some music

If all goes well, your quality audio streaming setup is now complete!


