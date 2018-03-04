Turn your rpi into a bluetooth audio receiver
======================================================================


This tutorial is written against BlueZ 5 and PulseAudio 5.


## Install BlueZ and PulseAudio

```shell
apt-get install --no-install-recommends bluez       \
                                        bluez-utils \
                                        bluez-tools \
                                        pulseaudio  \
                                        pulseaudio-module-bluetooth
```


## PulseAudio configuration

Edit `/etc/pulse/system.pa` and add:

```
.ifexists module-bluetooth-policy.so
load-module module-bluetooth-policy
.endif

.ifexists module-bluetooth-discover.so
load-module module-bluetooth-discover
.endif
```

Create `/etc/systemd/system/pulseaudio.service` and add:


```
[Unit]
Description=Pulse Audio

[Service]
Type=simple
ExecStart=/usr/bin/pulseaudio --system --disallow-exit --disable-shm

[Install]
WantedBy=multi-user.target
```

Add the pulse user to the bluetooth group:

```
usermod -a -G bluetooth pulse
```

Create `/etc/dbus-1/system.d/pulseaudio-bluetooth.conf` and add:

```
<busconfig>
  <policy user="pulse">
    <allow send_destination="org.bluez"/>
  </policy>
</busconfig>
```

Enable and start the bluetooth and pulseaudio services:

```
systemctl daemon-reload
systemctl enable --now pulseaudio
systemctl enable --now bluetooth
```

## Pair devices

Run `bluetoothctl` and pair new devices:

```
agent on
default-agent
scan on
pair xx:xx:xx:xx:xx:xx
trust xx:xx:xx:xx:xx:xx
```
