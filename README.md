# Arduino IR repeater


## Description

A simple solution to repeating any IR signals around walls. With a IR receiver to receive the signal and IR LEDS to send the signal.


## Quick Code rundown

Code relies on the <a href = "https://github.com/z3t0/Arduino-IRremote">Arduino IRremote library</a> for sending, receiving, encoding and decoding IR signals. The code can be broken down into three steps;
Receive a IR signal, cleaning and remembering the signal and sending the signal. Base code is from the examples and changed to fit my needs of repeating any IR signal.

### Receive IR signal

 lines 76 - 82
 ```cpp
 if (irrecv.decode(&results)) {
   digitalWrite(STATUS_PIN, HIGH);
   storeCode(&results);
   irrecv.resume();
   digitalWrite(STATUS_PIN, LOW);
   delay(30);
 ```

### Cleaning/remembering signal


``` c++
// Stores the code for later playback
// Most of this code is just logging
void storeCode(decode_results *results) {
  codeType = results->decode_type;
    Serial.println("Received unknown code, saving as raw");
    codeLen = results->rawlen - 1;
    // To store raw codes:
    // Drop first value (gap)
    // Convert from ticks to microseconds
    // Tweak marks shorter, and spaces longer to cancel out IR receiver distortion
    for (int i = 1; i <= codeLen; i++) {
      if (i % 2) {
        // Mark
        rawCodes[i - 1] = results->rawbuf[i]*USECPERTICK - MARK_EXCESS;
        Serial.print(" m");
      }
      else {
        // Space
        rawCodes[i - 1] = results->rawbuf[i]*USECPERTICK + MARK_EXCESS;
        Serial.print(" s");
      }
      Serial.print(rawCodes[i - 1], DEC);
    }
    Serial.println("");
}

```

### Sending IR signal

```c++
digitalWrite(STATUS_PIN, HIGH);
sendCode(lastButtonState == buttonState); // relay signal to ir lights
digitalWrite(STATUS_PIN, LOW);
delay(30); // Wait between retransmissions
```

## Parts / wiring

**Im no expert in circuitry**

You could use this <a href = "https://www.circuito.io/app?components=512,9349,10266,11021"> wiring diagram </a>.

Hypothetically all parts except for the Arduino IR repeater can be found in a broken tv and remote. The IR receiver is found along the bottom of the tv and IR LED can be found in the tip of the remote controller.

## Quick code setup

1. Setup Arduino<a href = "https://www.arduino.cc/en/Guide/HomePage">here</a>.

2. Download the zip Arduino RemoteIR library <a href = "https://github.com/z3t0/Arduino-IRremote/archive/master.zip"> here</a>.

3. Adding the zipped library to Arduino text editor<a href = "https://www.arduino.cc/en/guide/libraries#toc4"> here </a>.

4. git clone (download) this repository https://github.com/MitchTODO/Arduino-IR-repeater.git .

5. Open irRepeater.ino from Arduino text editor make changes if needed and upload to arduino.

6. Test with phone camera (IR is outside the visual light spectrum) and Serial Monitor within the Arduino text editor under tools.
