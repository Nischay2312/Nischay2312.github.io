---
layout: post
title: ESP32 based Motion Controlled Car
subtitle: Two ESPs one IMU and a lot of fun
#cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/Motion_Controlled_Car.png
tags: [ESP32, Web Sockets, Motion Controlled, IMU, Motor Driver, PWM]
readtime: true
comments: true
---

## Introduction
In this post, I present a 4-wheel differentially steered car controlled through an IMU (MMPU 6050) and integrated with ESP32. The system uses two ESP32s, one at the controller end and the other on the car end, communicating via web sockets for fast speeds and low latency. On the car side, the setup consists of 4 motors with two motor drivers, controlled via PWM from the ESP32.

![Motion Controlled Car](https://nischay2312.github.io/assets/img/Motion_Controlled_Car.png){: .mx-auto.d-block :}

## Hardware Used
- [2 x ESP 32](https://a.co/d/194DPZh)
- [MPU6050 or Alternative](https://a.co/d/4zm0hvW)
- [2 x L298N Motor Driver Module](https://a.co/d/42O7AS0)
- [Parts for Car or get this cheap Car Kit](https://a.co/d/jiHGEzD)
- Basic electronic componets (jumper wires, soldering iron, breadboard, micro usb cable)

## Software Used
- Arduino IDE to program the ESP32s.
- ESP32 Libraries specifically its **WiFi.h** and **WebSocketsServer.h** library.
- Libraries for MPU6050 or any other IMU that you use.

## Project Demonstration
You can find the YouTube video for this project embedded below or click [here](https://youtube.com/shorts/tKK5ULvAgyA?feature=share) to view it directly.

<iframe width="315" height="560" src="https://youtube.com/embed/tKK5ULvAgyA" title="YouTube video player" frameborder="0" allow="accelerometer; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Future Work
- Add more precise gesture controls
- Integrate additional sensors for obstacle detection
- Add a camera for computer vision based control

Thank you for reading this post. If you have any questions or feedback, please feel free to leave them in the comments section below. I know I havent provided the code for this project and its because I dont know where it is :/ . You can send me an email and I will try to find it and send it to you :)
