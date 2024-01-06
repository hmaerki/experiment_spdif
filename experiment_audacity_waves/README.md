https://www.youtube.com/watch?v=THbPdNHJTMI

https://jxxcarlson.medium.com/creating-audio-files-with-python-55cba61bfe73



# Python

https://pypi.org/project/WaveGenPy/
https://github.com/EfiPy/WaveGenPy

```
pyenv virtualenv 3.11.7 venv_tmp_audio
pyenv shell venv_tmp_audio
pip install WaveGenPy

python -m WaveGenPy.Sample.SampleTone -o sawtooth.wav -W triangle sawtooth -r 16 -f 30 -t 0.3 -v 70

ls -l sawtooth.wav
57644 Jan  6 13:00 sawtooth.wav
```


## Aplay

Add user to group `audio` and `pulse`
```
sudo usermod -a -G audio $USER
sudo usermod -a -G pulse $USER
```

```
aplay -L
hw:CARD=Device,DEV=0
    USB Audio Device, USB Audio
    Direct hardware device without any conversions
plughw:CARD=Device,DEV=0
    USB Audio Device, USB Audio
    Hardware device with all software conversions
sysdefault:CARD=Device
    USB Audio Device, USB Audio
    Default Audio Device

hw:CARD=Device,DEV=0
    USB Audio Device, USB Audio
    Direct hardware device without any conversions

iec958:CARD=Device,DEV=0
    USB Audio Device, USB Audio
    IEC958 (S/PDIF) Digital Audio Output
dmix:CARD=Device,DEV=0
    USB Audio Device, USB Audio
    Direct sample mixing device
```

Sendig sound to different devices does not seem to work:
```
aplay --device 'hw:CARD=Device,DEV=0' sawtooth.wav
==> Headset
aplay --device 'iec958:CARD=Device,DEV=0' sawtooth.wav
==> Headset
aplay --device 'dmix:CARD=Device,DEV=0' sawtooth.wav
==> Headset
```

```
aplay sawtooth.wav

while true; do aplay sawtooth.wav; sleep 1; done
```
