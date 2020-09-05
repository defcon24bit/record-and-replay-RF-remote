# record and replay Radio Frequency (RF) remote signal 
Record any RF signal and trigger replay with voice commands.     
<!--Use voice commands to control devices that use a Radio Frequency (RF) remote.   
Examples with picture: airconditioner, remote power plugs, fans and kichen hoods. 
Text-->

## required hardware

* Raspberry PI 
* radio receiver USB dongle (RTL-SDR with RTL2832U shipset)

## record signal 

install https://github.com/F5OEO/rpitx on your Raspberry PI with RTL-SDR dongle inserted

```bash
cd rpitx
./rtlmenu.sh
```
![](docs/pics/record-RF-menu-00002.png)
![](docs/pics/record-RF-menu-00003.png)
![](docs/pics/record-RF-menu-00004.png)
![](docs/pics/record-RF-menu-00005.png)
![](docs/pics/record-RF-menu-00006.png)
![](docs/pics/record-RF-menu-00007.png)
![](docs/pics/record-RF-menu-00008.png)

## create webhook

You'll need internet-facing webhooks to receive the voice triggers.  
I'll use [Home Assistant](https://www.home-assistant.io) (hassio) for that.  

#### create certificate on hassio

A certificate allows hassio to remote execute a commands on your PI without a password prompt. 
> Only required if hassio runs on a different machine than rpitx.  
>     

```bash
# create certificate on hassio
ssh-keygen -t rsa -b 4096
# send certificate to your pi (command still run on hassio)
ssh-copy-id pi@192.168.1.203
```

#### create command_line switch to configuration.yaml

```yaml
switch:
  - platform: command_line
    switches:
      fan_on:
        command_on: "ssh -i /config/id_rsa -o StrictHostKeyChecking=no -q pi@192.168.1.203 sudo ./rpitx/sendiq -s 250000 -f 868.0000e6 -t u8 -i ./rpitx/fan-all-on.iq | wc -l >> /config/command.log"
        command_off: off
        command_state: off
        friendly_name: Fan On
```

#### create an automation that exposes a webhook 

```yaml
automation:
  - alias: webhook_fan_on
    trigger:
    - platform: webhook
      webhook_id: <pick-a-random-webhook-id>
    condition: []
    action:
    - data: {}
      entity_id: switch.fan_on
      service: switch.turn_on
    mode: single
```
