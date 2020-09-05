# record and replay Radio Frequency (RF) remote signal 
Record any RF signal and trigger replay with voice commands.     
<!--Use voice commands to control devices that use a Radio Frequency (RF) remote.   
Examples with picture: airconditioner, remote power plugs, fans and kichen hoods. 
Text-->

## required hardware

* Raspberry Pi 
* radio receiver USB dongle (RTL-SDR with RTL2832U shipset)

## record RF signal 

on your Pi
1. Install [Raspberry Pi OS](https://www.raspberrypi.org/downloads/raspberry-pi-os/) (previously called Raspbian). 
2. Install https://github.com/F5OEO/rpitx
3. Connect RTL-SDR dongle to your Pi. 
4. Record remote signal.  
```bash
cd rpitx
# this records on 868 frequancy and writes it to fan-on-button.iq file
rtl_sdr -s 250000 -g 35 -f 868.0000e6 fan-on-button.iq
```
5. CTRL + C to stop recording.
6. Replay recording. 
```bash
sudo ./rpitx/sendiq -s 250000 -f 868.0000e6 -t u8 -i ./rpitx/fan-on-button.iq
```
<!--4. Launch the rpitx menu.
```bash
cd rpitx
./rtlmenu.sh
```
5. Set frequency, record and test replay - see [screenshot examples](https://github.com/defcon24bit/record-and-replay-RF-remote/tree/master/docs/record-RF-signal-screenshots.md).  
6. Rename the /rpitx/record.iq file.  
> The menu always uses the same file, so to avoid overwriting rename the file
```bash
cp record.iq on-button.iq
```
7. Go back to step 4. and repeat until you've recorded all buttons on your remote.    
-->

## create webhooks

You need internet-facing webhooks to intercept the voice triggers we'll create later. 
Below example uses [Home Assistant](https://www.home-assistant.io) (HA) installed on a second machine to do that.  
> Adding webhooks to your PI, without HA and without the need for a second machine should be possible.  Please share if you get that working.  

#### install HA

On a second machine:
1. Install https://www.home-assistant.io/getting-started/
2. Add DuckDNS add-on.  This will put your HA on the internet.  

#### create certificate on HA

The certificate allows HA to remote execute a commands on your PI without a password prompt. 
  
create certificate
```bash
ssh-keygen -t rsa -b 4096
```
send certificate to your pi (command still run on HA)
```bash
ssh-copy-id pi@192.168.1.203
```
> Initially,  HA gave errors when running the remote ssh commands.  I can't remember the exact fix.  It was either a HA user context not having access to the key files or the file and folder permissions for the certificate keys were not set correctly.  I think below two things fixed it.  Let me know if this works for you.

on HA
copy the id_rsa to the HA /config folder

on your Pi
```bash
chmod 700 ~/.ssh/
chmod 600 ~/.ssh/*
```

#### add command_line switch to your HA configuration.yaml

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

#### create automation on HA that exposes a webhook 

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
