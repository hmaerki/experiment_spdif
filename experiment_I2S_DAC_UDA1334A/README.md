# Micropython on RP2040 plays music on UDA1334A

## Links

* https://www.raspberrypi.com/documentation/microcontrollers/images/pico-pinout.svg
* https://learn.adafruit.com/mp3-playback-rp2040/pico-i2s-mp3
* https://docs.micropython.org/en/latest/rp2/quickref.html
* https://github.com/miketeachman/micropython-i2s-examples


## Wireing

| Comment | RP2 | UDA1334A |
| - | - | - |
| VIN | 3V3 | 3V0 |
| GND | GND | GND |
| Serial clock (SCK) | PIN21, GPIO16 | BCLK |
| Word select (WS) | PIN22, GPIO17 | WSEL |
| Serial data (SD) | PIN24, GPIO18 | DIN | 


https://github.com/miketeachman/micropython-i2s-examples/raw/master/wav/le-blues-de-la-vache-44k1-16bits-stereo.wav

```python
# Based on https://github.com/miketeachman/micropython-i2s-examples/blob/master/examples/play_wav_from_flash_blocking.py
from machine import I2S, Pin

if False:
    WAV_FILE = "side-to-side-8k-16bits-stereo.wav"
    WAV_SAMPLE_SIZE_IN_BITS = 16
    FORMAT = I2S.STEREO
    SAMPLE_RATE_IN_HZ = 8_000
if False:
    WAV_FILE = "music-16k-16bits-stereo.wav"
    WAV_SAMPLE_SIZE_IN_BITS = 16
    FORMAT = I2S.STEREO
    SAMPLE_RATE_IN_HZ = 16_000
if True:
    WAV_FILE = "le-blues-de-la-vache-44k1-16bits-stereo_short.wav"
    WAV_SAMPLE_SIZE_IN_BITS = 16
    FORMAT = I2S.STEREO
    SAMPLE_RATE_IN_HZ = 44_100

print(WAV_FILE)

i2s = I2S(0,
    sck=Pin("GPIO16"),
    ws=Pin("GPIO17"),
    sd=Pin("GPIO18"),
    mode=I2S.TX,
    bits=WAV_SAMPLE_SIZE_IN_BITS,
    format=FORMAT,
    rate=SAMPLE_RATE_IN_HZ,
    ibuf=40000
)

wav = open(WAV_FILE, "rb")
pos = wav.seek(44)  # advance to first byte of Data section in WAV file

# allocate sample array
# memoryview used to reduce heap allocation
wav_samples = bytearray(1000)
wav_samples_mv = memoryview(wav_samples)

# continuously read audio samples from the WAV file
# and write them to an I2S DAC
print("==========  START PLAYBACK ==========")
try:
    while True:
        num_read = wav.readinto(wav_samples_mv)
        # end of WAV file?
        if num_read == 0:
            # end-of-file, advance to first byte of Data section
            print("EOF")
            _ = wav.seek(44)
        else:
            _ = i2s.write(wav_samples_mv[:num_read])

except (KeyboardInterrupt, Exception) as e:
    print("caught exception {} {}".format(type(e).__name__, e))

# cleanup
wav.close()
i2s.deinit()
```
