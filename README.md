# Arduino library fo 4d Systems's uCamII TTL Camera

This is an Arduino library for 4D System's uCam-II http://4dsystems.com.au/product/uCAM_II/

Todo 
- Checksum check on each received packet
- More error detection and termination
- Timeout/return if no response received



```
#include <uCamII.h>
#include <SoftwareSerial.h>

SoftwareSerial mySerial(3, 2); // RX, TX (or tx on the end and rx on the end)

void setup() {
  mySerial.begin(57600);
  Serial.begin(57600);
  mySerial.println("trying ..");


  UCAMII camera;
  short x = 0;
  int bytes;

  // unComment the #define cameraDebugSerial in uCamII.cpp 
  // and set the debug output interface below
  // camera.setDebug(&mySerial);

  if (camera.init()) {
    camera.takePicture();
    mySerial.print("Image size: ");
    mySerial.println(camera.imageSize, DEC);
    mySerial.print("number of packages: ");
    mySerial.println(camera.numberOfPackages(), DEC);

    while ( bytes = camera.getData() ) {
      for (x = 0; x < bytes; x++) {
        mySerial.print("0x");
        mySerial.print(camera.imgBuffer[x], HEX);
        mySerial.print(" ");
      }
      mySerial.println("");
    }
    mySerial.println("done downloading");

  }
}

void loop() {
  // put your main code here, to run repeatedly:

}
```

Included also is a python script to convert the dbeug output bytes back to a JPEG, save the output of the terminal (make sure no pieces are missing, compare last byte values etc) to a text file, then
```cat output.txt|./hex-to-bytes.py``` and you should have a output.jpeg file
