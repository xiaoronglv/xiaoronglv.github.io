2022-02-22-optical-fiber.md

OSI model contains 7 layers.

- Application Layer
- Presentation Layer
- Session Layer
- Transport Layer
- Network Layer
- Datalink Layer
- Physical Layer


There are different implementations for Physical Layer.

## twisted pair cabling.

it's used for the local network within a home or office.

[![enter image description here][1]][1]


For twisted pair cabling, I assume it can use different voltage level to represent 1, 0, or idle. Here is my assumption.

| voltage level      | Binary Data |
| ----------- | ----------- |
| 10 V     | 1       |
| -10 V   | 0        |
| 0 V   | idle (stop sending data)  |


## optical fiber.
    
It's used for connection between ISP and your home, or connection between Europe and Asia.

[![enter image description here][2]][2]


Imagine there are two devices A and B, which has been connected via optical fiber.

A would like to say `hello world` to each other, then stop. The conversation works like following:

- A: hello
- A: device stops sending data and becomes idle


The transmitter of A will help to encode the binary information to physical signals. It either lighten the fiber or extinct the fiber in a fixed frequency, which stand for 1 and 0.

| Physical Signal | Data |
| ----------- | ----------- |
| light    | 1       |
| dark   | 0        |


But how to handle the following cases:


1. sending sequential `111111111` or `000000000`. If we let the fiber keep lightening or dark, how to distinguish individual `1` or `0`?

 
2. once the conversation has been done, how does the device A tell B `I have finished my work, I will become idle`? Does transmitter use a special hardcode signal to represent the end of information?


| Physical Signal | Data |
| ----------- | ----------- |
| light    | 1       |
| dark   | 0        |
| ???   | idle (stop sending data)  |


  [1]: https://i.stack.imgur.com/yM8sW.png
  [2]: https://i.stack.imgur.com/JeoG8.png


