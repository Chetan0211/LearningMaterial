# OSI Model

Full form of OSI is Open Systerm Interconnection. This OSI model was developed to help computing systems to communicate each other.

There are 7layers in this model:<br/>
**Layer-1 : Physical**<br/>
**Layer-2 : Data Link**<br/>
**Layer-3 : Network**<br/>
**Layer-4 : Transport**<br/>
**Layer-5 : Session**<br/>
**Layer-6 : Presentation**<br/>
**Layer-7 : Application**<br/>

Let's talk about each one below:

## Layer-1 : Physical Layer

In this layer we focus on how to pass the signals from one place to another through medium(wire/signals).<br/>
Devices used for this are analog modem, hub, etc.

**Modem:**<br/>
The main purpose of modem is to get the digital signal and change it to analog signal and vise versa. That is how modem got its name Modulator/Demodulator for modulating and demodulating the signals.

**Hub:**<br/>
Hub works a little different. A hub takes the single and it duplicates that signal. So it basically works as concentrator or repeater. It has few to many ports.

## Layer-2 : Data Link Layer
It is used to deliver the data from one node to another node. It's major role is to ensure error-free transmission of information.<br/>
Devices used for this are Swith, WAP, etc.

**Switch:**<br/>
Switch is a type of network switch or device that works on the data link layer (OSI Layer 2) and utilizes MAC Address to determine the path through where the frames are to be forwarded. It uses hardware based switching techniques to connect and transmit data in a local area network (LAN). The hadware that is used is called as Application Specific Integration Chip(ASIC). Switch can only communicate with Local Network Devices.

**WAP:**<br/>
WAP(Wireless Access Point) is a specific type of network bridge that connects wireless network segments with wired network segments. Thw most common type of WAP bridges are 802.11 wireless network segments with 802.3 ethernet network segments. WAP can only communicate with Local Network Devices.

## Layer-3 : Network Layer
It handles the routing and sending of data between different networks.
Devices used for this are Multilayer Switch, Router, etc.

**Multilayer Switch(MLS):**
We have seen switch in layer-2. But it can also be used as Layer-3. Most of the switches are layer-3. In layer-3 ASIC can also programmed to handle routing functions. This allows the device to communicate and pass data to non-local network devices. It is a highly programmable and complex device. It can have few or many ports.

**Router:**<br/>
It is the most common device used to connect different networks together. This router used software programming foer decision making as compared to switch(which uses ASIC chip). It can keep track of diffrent networks and best possible routes to reach those networks using programming. It can communicate with both local and non-local nwtwork devices.
