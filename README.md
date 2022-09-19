# DWM1001c
RTLS

TIMEAS MATTER  (RTLS)
DWM1001c 

￼
￼
￼

The Decawave Positioning and Networking Stack (“PANS”), available as a library accompanied by source code that allows a level of user customisation. The PANS software is pre-installed and runs on the Module as supplied, and enables mobile “tags”, fixed “anchors” and “gateways” that together deliver the DWM1001 Two-Way-Ranging Real Time Location System (“DRTLS”) Network.

Gateway 192.168.86.118
Raspberry pi connected to Ethernet and to one of the DWM1001 modules
It runs a mqtt broker and a web server

find RPI: 
ping raspberrypi.local


http://192.168.86.118

It will show a florplan with the anchors, gateway and tag positions

listen to MQTT message using
mosquitto_sub -t \# -h 192.168.86.118




communicate with devices via USB
ls /dev/tty.*

screen /dev/xxxxxxxxx 115200

ENTER ENTER

nis 0x0000 —> sets ID as 0000
nis 0x0001 —> sets ID as 0001 
…

aps x y z   // x,y,z are position in mm
e.g. 
aps 0 0 1800 

set node modes:

nmi —> initiator (x1)
nma —> anchor (x2)
nmb —> bridge (x1) 
nmt —> tag (x1) 

si —> get system information




ideal setup: 

6 x DWM1001-DEV boards (3 x anchors, 1 x initiator, 1 x tag, 1 x bridge)
10 x power banks (4  for anchors and initiator + 1 for tag + replacements) 
1 x raspberry PI 
1 x 16GB SD card
1 x RPI power supply
1 x wifi router
6 x 3D printed cases


￼




mosquitto_sub -t \# -h 192.168.86.118 | jq -r  '[.position.x, .position.y, .position.z] | join(", ") | .+= " TAG"'


3.372638, 2.4369018, -0.3349463 TAG
3.3663588, 2.4469326, -0.33751172 TAG
3.3709345, 2.4353786, -0.33575431 TAG
3.3653281, 2.4299736, -0.32368752 TAG

it’s also possible to pipe into netcat using: 

|  nc -u -c 127.0.0.1 12000 

========

MQTT to MaxMSP

￼

## mqtt-to-osc.js

const readline = require('readline');
const OSC = require('node-osc') 
    
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
  terminal: false
});
   
rl.on('line', function (cmd) {
  console.log(cmd);
  try{
    const msg = JSON.parse(cmd);
    const data = Object.values(msg.position);
    console.log(data);
    const client = new OSC.Client('127.0.0.1', 12000);
    client.send('/position', data, () => {
      client.close();
    });
  }
  catch(err){
    console.error(err);
  }
});


RUN: 

mosquitto_sub -t \# -h 192.168.86.118 | node mqtt-to-osc.js

￼


