# record-and-replay-RF-remote
Using a Raspberry PI, record any Radio Frequency (RF) remote signal and replay it with voice commands through Alexa, Google Home or Siri.  

## project objective

Use voice commands to control devices that use a Radio Frequency (RF) remote.   
Examples with picture: airconditioner, remote power plugs, fans and kichen hoods. 

## required hardware

raspberry PI
radio receiver USB dongle (RTL-SDR with RTL2832U shipset)

## record RF signal 
```bash
cd rpitx
./rtlmenu.sh
