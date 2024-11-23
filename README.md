# Santa Claus Detector

This is again the time of the year when it pays off for children between the age of 0-99 to be nice to everyone. You never know who's watching through the window!



## Story

Living just a few sledge rides from the Polar Circle and the mountain of Korvatunturi where the real Santa Claus lives, we are in Finland used to having his elves or sometimes even Santa himself, checking our behavior through the windows. Typically we never see them in action, but instead only notice tracks in the snow or some fur hair from reindeers the morning after the visit. If we however use some clever technology, we will get alerted when Santa's crew is around!

## Solution

A solution to this problem is to create a device which looks out through the window, checks if a person is looking into the house, and alerts only if it recognizes Santa. To avoid false Santa alarms, it will thus not alert for any other person, friend or foe.

One way is to use a camera connected to a microcontroller or computer and use a machine learning model trained to recognize Santa. The drawback is that you might need to spend up to $100 for a good enough camera and microcontroller and spend hours of building a ML model. 

A more elegant, and very affordable way is to use a Particle Photon 2 and the Person Sensor from Useful Sensors. At the time of writing both of them are priced below $10! Depending on how you want to get alerted, through a notification or some more mechanical way, you might need to spend a few more dollars at hardware.

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

- [Person Sensor by Useful Sensors](https://www.sparkfun.com/products/21231)

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
- Optional but strongly recommended:
  - [3D-printed case](/images/Linear_actuator_holder.stl) for the linear actuator, this can be fastened on any standard camera tripod or the [gooseneck](https://www.amazon.de/-/en/SUNNINGUP-Webcam-Holder-Gooseneck-Logitech/dp/B0CY1XCK5Y?crid=2WFW7HQM0HJPJ&dib=eyJ2IjoiMSJ9.kT3ZM6nWF0QKwosUQT_7Zq7jwWNwwZ8WvtMwOnsT8BZB1YZatMffhUGVJ9v7VmbB6F0LRjLQwBTMhXCrjN0eL_oO6GXo6AwQT3qCoP9wAKfZoJhlRgNDi21J_63oxpuw1q6M_synfozWbhuBRjnp_lzQP1CZA_pqM8mBNxlh91G__mYFKp-3W7TjKo_ue9WQ-rurY08xTsVJNB-5aWHc9owTR8U0kZnV634K6-KGCno.96RiRaGgM76K_7PEJZX35qy5HXdGSG0hmovSdrlnwe4&dib_tag=se&keywords=gooseneck+tripod&qid=1725531849&sprefix=gooseneck+tripod%2Caps%2C92&sr=8-10) I used
  - [3D-printed enclosure](/images/Person%20Sensor%20Body_v1.1.stl) and [back plate](/images/Person%20Sensor%20Back%20Plate.stl) for the Person Sensor 


# Assembly

Only a few steps are needed to assemble the system:
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



## Software apps and online services

- Visual Studio Code with the Particle Workbench extension installed
- Optional if you want to get a Santa notification
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

Apart from verification and troubleshooting purposes, you should also take a note of which face is having which ID. E.g. if your face were registered with ID 1, and Santa's with ID 4, you need to remember this for next step.

### Set up the Santa detection alarm ###

If you want the servo to react to specific faces, you need to change this code (around line 156):
`(face-> id == 4)` to include the face or faces you want to be able to unlock the door. In this case Santa has id #4, and while other faces like the president of Finland and Mona Lisa are identified by the Person Sensor, we don't take any actions on them.

Depending on the mechanical alarm device you've created, you might want to change how many times the servo is moving back and forth, in this case with the variable `jingles`, or the end positions and speed of the servo in the `santa`-function.

```
#ifdef santa
        if (face-> id == 4)  {
          int jingles = 6;
          
          for (int jingle = 0; jingle < jingles; jingle++)
            santa();
        }
#else
```



```
void santa()  {
  int speed = 5;

  for(servo_pos = 0; servo_pos < 175; servo_pos += speed)   // goes from 0 degrees to xxx degrees
  {                                                     // in steps of 1 degree
    myservo.write(servo_pos);                           // tell servo to go to position in variable 'pos'
    delay(5);                                           // waits for the servo to reach the position
  }

  delay(200);

  for(servo_pos = 175; servo_pos>=1; servo_pos -= speed)      // goes from xxx degrees to 0 degrees
  {                                                     // in steps of 1 degree
    myservo.write(servo_pos);                           // tell servo to go to position in variable 'pos'
    delay(5);                                           // waits for the servo to reach the position
  }
}
```


### Notification settings ###

With the Pushover service you can send 10000 notifications/month for free. Especially while setting up and testing this concept, I could easily consume close to 100 notifications per day. From a quota point of view this was still not an issue, but I got tired of the constant pings on my mobile and smartwatch. Hence I decided to send a notification if at least 10 seconds have gone since previous notification.


## Security notes ##

According to Useful Sensors you shouldn’t rely on the facial identification results as a **sole** security factor, since it’s not designed to be that accurate. It’s intended for less safety-critical applications like personalizing a user experience automatically, where the cost of mistakes is lower.

You of course also needs to consider physical security of the device, and weather-proofing everything as per the location.



# CONCLUSION #



This PoC and tutorial demonstrated how you with fairly simple devices can build a facial recognition and identification doorbell/notification device, and in addition how you can utilize it for e.g. unlocking purposes. This was also the first time I used a Particle device together with their IoT-platform, and although I didn't understand everything right away, I'm surprised how logical everything is, once I got over the initial hurdle!