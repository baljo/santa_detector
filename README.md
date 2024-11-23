# Santa Claus Detector

This is again the time of the year when it pays off for children between the age of 0-99 to be nice to relatives and friends. You never know who's watching through the window!



## Story

Living just a few sledge rides from the Polar Circle and the mountain of Korvatunturi where the real Santa Claus lives, we are in Finland used to having his elves or sometimes even Santa himself, checking our behavior through the windows. Typically we never see them in action, but instead only notice tracks in the snow or some fur hair from reindeers the morning after the visit. If we however use some clever technology, we will get alerted when Santa's crew is around!

## Solution

A solution to this problem is to create a device which looks out through the window, checks if a person is looking into the house, and alerts only if it recognizes Santa. To avoid false Santa alarms, it will thus not alert for any other person, friend or foe.

One way is to use a camera connected to a microcontroller or computer and use a machine learning model trained to recognize Santa. The drawback is that you might need to spend up to $100 for a good enough camera and microcontroller and spend hours of building a ML model. 

A more elegant, and very affordable way is to use a Particle Photon 2 and the Person Sensor from Useful Sensors. At the time of writing both of them are priced at $9! Depending on how you want to get alerted, through a notification or some more mechanical way, you might need to spend a few more dollars at hardware.

As I in a [previous project](https://www.particle.io/blog/how-to-unlock-a-door-with-your-face-using-particle/) had used the M2 SoM Evaluation Board and a B524 SoM, I decided to use the same setup, but there's nothing hindering you using e.g. Photon 2 instead.

## How does it work?

From a user perspective, the process is straightforward:

- The sensor continuously checks for faces in its view.
- If a face is detected:
  - It checks whether the face matches one of the eight identities it has been calibrated to recognize.
  - If a matching face (= Santa) is found, the Particle device signals the servo accordingly.
  - A mobile notification is sent through Pushover, alerting that Santa is around!

Below a GIF-video demonstrating the behavior, here's a short [video clip](https://youtube.com/shorts/aByAacTvO_A) with sound.

![](/images/Santa_Detector.gif)




## Bill of materials

- MCU: [Particle M.2 SoM Evaluation Board](https://store.particle.io/products/som-evaluation-board?_pos=1&_sid=a6a33f54b&_ss=r) 

![](/images/M2_eval_board_cropped_2.jpg)

- [B524](https://store.particle.io/products/b-series-lte-cat1-3g-2g-europe-ethersim?_pos=1&_sid=c3651bdc9&_ss=r), it has cellular and Bluetooth connectivity (no Wi-Fi), but if you want Wi-Fi instead of cellular connectivity, a [Photon 2 dev board](https://store.particle.io/products/photon-2?_pos=1&_sid=5598553d5&_ss=r) can be used and is more cost effective 

![](/images/B524_cropped.jpg)

- [Person Sensor by Useful Sensors](https://www.sparkfun.com/products/21231), priced at ~$10 USD!

![](/images/Person_Sensor_cropped.jpg)


- Optional, but recommended
  - separate power source for the servo or actuator, especially if the latter is power hungry or under load 
- Optional
  - LiPo battery, some [options](https://docs.particle.io/hardware/power/batteries/)
  - [Linear actuator kit](https://kitronik.co.uk/products/2595-linear-actuator?_pos=3&_sid=e648f346a&_ss=r), you can replace this with any servo, push rod, or larger linear actuator

![](/images/Linear_actuator_cropped.jpg)


  - [Qwiic Cable - Grove Adapter (100mm)](https://www.sparkfun.com/products/15109), this if you want an easy way of connecting the Person Sensor to the Grove port on the M.2 SoM Eval Board, another option is to use separate jumper wires.

![](/images/Qwiic_Grove_cable_cropped.jpg)


## Skills & tools required

For this project, you’ll need

- A Particle account and basic understanding of the Particle platform's capabilities.
- Particle-flavored, Arduino-style, C++ development.
- No soldering is required for this PoC
- Optional:
  - 3D-printed case for the linear actuator: 
- Optional: 3D-printed case for the Eval Board, and a [3D-printed enclosure](https://thangs.com/designer/ZackFreedman/3d-model/Person%20Sensor%20Mount%20-%20Print-in-place%2C%20nothing%20but%20filament%21-836098) for the Person Sensor 


# Assembly

Only a few steps are needed to assemble the system (see the top image for a visual overview):
- Attach the B524 to the Eval Board
- Connect the cellular antenna to the Eval Board (the Bluetooth antenna can be left unconnected for this project).
- Connect the Person Sensor to the Eval Board, ensuring that you connect it to the I2C Grove port, not the analog port!

![](/images/Grove_connector_cropped.jpg)


- Optional: 
  - Connect a battery 
  - If using a 6V servo or actuator like in this project, it can be powered temporarily from the Eval Board. For long-term use, a separate power source is recommended.
    - In following images following wire colors are used: white = signal, black = ground, red = power
    - Connect the servo signal wire to **PWM0** (**D4** in the program)
    - Connect the ground wire to **GND** on the board


![](/images/PWM_GND_cropped.jpg)


  - Connect the power wire to **VCC** on the board

![](/images/Power_cropped.jpg)


- Power the B524 using the SOM power switch
- Connect the Eval board to your computer via an USB-cable
- If using a battery, charge it using the BAT POWER switch

![](/images/Switches_cropped_2.jpg)



### Optional: Test the servo

You can optionally verify the servo connection using [this test program](/backup/Servo.cpp), which moves the servo horn between its two endpoints. The program is optimized for the specific servo used in this project, so verify your servo's specifications before running it.

## Software apps and online services

- Visual Studio Code with the Particle Workbench extension installed
- Optional
  - Particle’s Webhook Integration for sending notifications to your mobile
  - Twilio, Pushover, or similar services for sending SMS or notifications
- The complete program can be found [here](/src/Person_sensor.cpp)

### Set up a Webhook integration to Pushover

This assumes you are using the Pushover service, but the overall principle is similar, regardless of chosen service.

#### Pushover service ####
- Create an account at Pushover (or similar service, e.g. Twilio)
  - Create an application in Pushover
  - Take a note of the User Key and API token
- Also install the Pushover app on your mobile device to get notifications


**User key field in Pushover** 

![](/images/Pushover_user_key.jpg)

**API Token/key in Pushover**

![](/images/Pushover_API_key.jpg)



#### Particle Webhook ####

- In the Particle console, Go to `Integrations`
- Add a new integration
- Scroll down and select `Custom Webhook`
- Select ´Custom template`
- Paste in the code below
- Replace the `token` and `user` in the code with the ones from your chosen service 
- `event` - in this case *"face_detected"* - should be same event as you are publishing from your code
- Test the integration, if everything is set up correctly, you should get a notification on your mobile device
  - If you use Pushover, the notification on your mobile is received through the Pushover service, not as a SMS.




```
{
    "name": "Identified as person: ",
    "event": "face_detected",
    "responseTopic": "{{PARTICLE_DEVICE_ID}}/hook-response/{{PARTICLE_EVENT_NAME}}",
    "disabled": false,
    "template": "webhook",
    "url": "https://api.pushover.net/1/messages.json",
    "requestType": "POST",
    "noDefaults": true,
    "rejectUnauthorized": true,
    "unchunked": false,
    "dataUriResponseEvent": false,
    "form": {
        "token": "token to selected service",
        "user": "user id at selected service",
        "title": "Door opened by person ",
        "message": "{{{PARTICLE_EVENT_VALUE}}}"
    }
}
```

![](/images/webhook.jpg)



### Calibrate the Person Sensor

The idea with the Person Sensor is that it should behave similarly as any other sensor, i.e. send data  back to the calling device. It is pre-programmed with algorithms that detect nearby faces, and return information over a simple  I2C interface. It is designed to be used as an input to a larger system and be treated like any other sensor where power and information are provided through the I2C interface.

All this means that apart from a few configurations options, the sensor can't be programmed, which also makes it extremely tamperproof (100 %?). But how do we then get it to not only **recognize if a face is seen**, but also to **identify the face**? The solution here is called calibration.

#### Calibration

The calibration can in theory be done stand-alone, but for practical purposes a computer should be connected. This way you can verify the functionality via the terminal window: 
- Position the person whose face you want to store in front of the sensor. When the face is detected, the green LED will light up.
- Within ~200 ms, press the `Mode` button on the Eval Board to store the face's features and assign it an ID in the sensor's EEPROM. Up to eight faces can be stored, with IDs ranging from 0 to 7.
- Repeat this process for each new face you want to store.

Below the function which is reacting on the button event. The "magic" happens in this code line - `person_sensor_write_reg(NEXT_ID_REGISTER, nextFaceID);` - which stores the face's features and corresponding ID into EEPROM.

```
void button_clicked(system_event_t event, int param)
{
    int times = system_button_clicks(param);

    faceIDs[nextFaceID] = nextFaceID;                   // Assign new ID to face
    person_sensor_write_reg(NEXT_ID_REGISTER, nextFaceID);
    Serial.printlnf("******** %d ********", nextFaceID);
    nextFaceID++;
}
```


**Mode button**

![](/images/Mode_button_cropped.jpg)



### Verify facial identification ###

Both during the calibration process and under normal use you can follow what the program is doing via the terminal window. To open this, open the Command Palette (Ctrl+Shift+P) and search for `Particle: Serial Monitor`. If you want to redo the calibration, just recycle power to the device and start the calibration again.

Apart from verification and troubleshooting purposes, you should also take a note of which face is having which ID. E.g. if your face were registered with ID 0, and your childrens with 1 respectively 2, you need to remember this for next step.

### Set up the facial unlock configuration ###

If you want the servo to react - e.g. open a door - to specific faces, you need to change this code (around line 135):
`(face-> id == 3 || face -> id == 0)` to include the face or faces you want to be able to unlock the door. In this case my own face is registered twice, once with glasses and once without.

```
        // opening the door if it's closed and enough time has passed, also publishing to Particle
        unsigned long currentTime = millis();
        if ( (currentTime - lastPublishTime >= publishInterval) && (face-> id == 3 || face -> id == 0) && door_open == false) {
            Particle.publish("face_detected", String(face->id), PRIVATE);
            open();
            Serial.println("** OPENING **");
            door_open = true;
            lastPublishTime = currentTime;
            lastOpenTime = currentTime;

        }
```

### Notification settings ###

With the Pushover service you can send 10000 notifications/month for free. Especially while setting up and testing this concept, I could easily consume close to 100 notifications per day. From a quota point of view this was still not an issue, but I got tired of the constant pings on my mobile and smartwatch. Hence I decided to send a notification if at least 10 seconds have gone since previous notification.

Furthermore, while you are testing the code, you can comment out the `Particle.puhlish...` function call.


## Security notes ##

According to Useful Sensors you shouldn’t rely on the facial identification results as a **sole** security factor, since it’s not designed to be that accurate. It’s intended for less safety-critical applications like personalizing a user experience automatically, where the cost of mistakes is lower.

You of course also needs to consider physical security of the device, and weather-proofing everything as per the location.

## Real-Life Solution Demonstration ##

In this video you can see how the blue "lock" on the left hand is ensuring no-one can steal my marathon medals. The hinge only unlocks when the sensor sees my face, and in this case automatically locks itself after 10 seconds.

![](/images/Facial_recognition_door_unlock.gif)

At the same time a notification is sent to my phone, informing who is opening the cabinet. This is in this use case useful as I can see if someone, apart from me, is in front of the sensor and perhaps trying to break in.


![](/images/IMG_4441_cropped.jpg)



# CONCLUSION #

This PoC and tutorial demonstrated how you with fairly simple devices can build a facial recognition and identification doorbell/notification device, and in addition how you can utilize it for e.g. unlocking purposes. This was also the first time I used a Particle device together with their IoT-platform, and although I didn't understand everything right away, I'm surprised how logical everything is, once I got over the initial hurdle!