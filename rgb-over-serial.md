# RGB over Serial

*[needs better organization -- just dumping thoughts for now]*

**Question:** What is the best way to send large amounts of RGB data from a computer to an Arduino in order to drive a large(ish) LED matrix?

## VideoDisplay

OctoWS2811 is an [Adaptor for the Teensy 3.2](https://www.pjrc.com/store/octo28_adaptor.html) and an [accompanying Arduino library](https://www.pjrc.com/teensy/td_libs_OctoWS2811.html) that comes with an example called [VideoDisplay](https://github.com/PaulStoffregen/OctoWS2811/blob/master/examples/VideoDisplay/VideoDisplay.ino). This demonstrates how to prepare your Teensy/Octo to receive a super efficient stream of RGB data. The [Processing side is provided here](https://github.com/PaulStoffregen/OctoWS2811/blob/master/extras/VideoDisplay/Processing/movie2serial.pde). In a nutshell, every frame is encoded into chunks of 8 pixels. This must have a compression effect, but I don't totally understand how yet.

## Serial Buffer

Different Arduino have different size serial buffer, which can greatly affect how to structure your communication.

*[insert more information here]*