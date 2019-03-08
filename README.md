# Iot-Remote-Control-Car

## Summary

The goal of this project is to remotly control a car with four motors, ultrasonic sensors and an IR receiver through a web server. An IR receiver is added to receive encoded messages from beacons (esp32 microcontrollers with IR senders which constently send encoded messages). A webcam and a Raspberry Pi is attached on top of car to provide a navigating view to the users. 

## Evaluation Criteria

 - Car	is	controlled	remotely	from	DDNS	URL (remotecontrol.ddns.net)	and drives	successfully	including	L,	R,	F,	R	controls.

 - Each	beacon	is	visited	by	car	and	each	message fragement	decoded	in	program.

 - Video	incorporated	in	single	browser	frame	with controls.

 - Collision	avoidance	used;	no	collisions

 - Catenated	message	formed	in	program	and decoded	automatically	in	browser	showing hidden	message.


## Solution Design

This project consists of several components: a web server for remote control, a webcam for live navigating view, an IR receivers for receiving message, ultrasonic sensors for collision avoidance. 

First, the remote control webpage was hosted on laptop with node.js. The page consists of 4 buttons, a video, and text revealing decoded message. Each button is bound with a unique 'click' event listener - it sends a POST request to node server (http://remotecontrol.ddns.net:1111/) with a json object with value of '0', '1', '2', and '3' representing forward, right, backward, and left directions respectively. Although Ajax module supports direct HTTP requests to HTTPD server on esp32, we ended up using XMLHttpRequest to post to node server which then sends requests to esp because we ran into CORS (Cross-Origins Resource Sharing) problems with Ajax. Therefore, node server sends a POST request which triggers car to move in the corresponding direction for 200ms. The integration of webcam is simple - it was done by simply setting the src attribute of video element on page to the ip address of pi plus port 8081. Obviously the webcam has to be started through ssh-ing into pi and enter prompt 'sudo service motion start'.

Second, the IR receiver decodes the message by finding the start byte '0x1B' and recognizes the message's id with its next byte. Because there are 4 messages in total to be decoded and concatenated, its corresponding flag is set when received. When all 4 flags are set, the concatenated message is then sent to web server. Because we couldn't understand how to dynamically change html DOM elements on node server side, we instead have a timer on client side that posts GET request every second. Server only responds to this GET request when the complete message is decoded and combined and ignores it otherwise.

Last, ultrasonic sensors integration was simple. We used the maxbotics sensor because we had much trouble debugging with the more accurate lidar, and it still works perfectly. It is called within drive forward function within esp32. When there is object in front (within 40cm range), even if user clicks forward button on webpage, the car wouldn't move forward to avoid collision. The sensor reading is obtained with ADC unit 1, since we know that ADC unit 2 input is impossible when WiFi is enabled on esp.

## Sketches and Photos

 - Web interface
 ![web](https://user-images.githubusercontent.com/14796259/50057940-ac7dbe00-013f-11e9-8f3c-13ee98e8aa86.png)

 - Top view
 ![top](https://user-images.githubusercontent.com/24300238/50047656-e55a5c00-0087-11e9-94df-1c52004a19f0.JPG)

 - Front view
 ![ffront](https://user-images.githubusercontent.com/24300238/50047772-1c317180-008a-11e9-8c4c-a851f9b60328.JPG)

 - Side view
 ![side](https://user-images.githubusercontent.com/24300238/50047664-1a66ae80-0088-11e9-8c95-38ec71d15ad6.JPG)


## Links

 - http://whizzer.bu.edu/team-quests/primary/remote-control  


## Modules, Tools, Source Used in Solution

- Ultrasound (ADC)

- PWM (car)

- node.js and express (w/ request module)

- HTML (client)

- Pi


## Supporting Artifacts
-[Video demo](https://youtu.be/uIKiAT7xqJo)
