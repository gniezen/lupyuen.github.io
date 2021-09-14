# LoRaWAN on PineDio Stack BL604 RISC-V Board

![Tiny tasty treat... PineDio Stack BL604 RISC-V Board](https://lupyuen.github.io/images/lorawan2-title.jpg)

_Tiny tasty treat... PineDio Stack BL604 RISC-V Board_

📝 _19 Sep 2021_

Previously I wrote about testing the prototype __PineDio Stack BL604__ RISC-V Board...

-   [__"PineDio Stack BL604 RISC-V Board: Testing The Prototype"__](https://lupyuen.github.io/articles/pinedio)

Today we dive into the most exciting component on PineDio Stack: __Semtech SX1262 Transceiver__ for __LoRa and LoRaWAN Networking__.

_Why LoRa?_

LoRa is a __Low-Power, Long-Range, Low-Bandwidth__ wireless network.

LoRa is perfect for __IoT Sensor Devices__ that run on Battery Power. (Or Solar Power)

Since PineDio Stack comes with a [__Solar Panel__](https://lupyuen.github.io/articles/pinedio), it will work really well for Agriculture Sensors.

(And many other IoT gadgets out there in the sun)

_Will LoRa support all kinds of messages?_

Not quite. LoRa only supports __Short Messages__ of up to [__242 Bytes__](https://lora-developers.semtech.com/documentation/tech-papers-and-guides/lora-and-lorawan).

And because LoRa is a Low Power (best effort) network, __messages may get dropped.__

Which is probably OK for sensor devices that send data periodically.

(But not for texting your friends)

_Is LoRa secure?_

LoRa messages are delivered securely when we join a __LoRaWAN Network__.

Though our __Security Keys__ would also need to be __stored securely__ on PineDio Stack.

(We'll learn how in a while)

_Which Pine64 devices will talk LoRa and LoRaWAN?_

Once the drivers are implemented, these Pine64 devices will talk LoRa and LoRaWAN to PineDio Stack...

-   [__PineDio LoRa Gateway__](https://wiki.pine64.org/wiki/Pinedio)

-   [__PinePhone with LoRa Backplate__](https://wiki.pine64.org/wiki/Pinedio#Pinephone_backplate)

-   [__Pine64 LoRa USB Adapter__](https://wiki.pine64.org/wiki/Pinedio#USB_adapter)

![PineDio Gateway, PinePhone Backplate and USB Adapter](https://lupyuen.github.io/images/lorawan2-pine64.jpg)

This article describes the (pre-production) __PineDio Stack Prototype__ thus...

> ⚠️ ___Obligatory Disclaimer:__ Features included in The Prototype are not complete, and will most certainly undergo changes before becoming available for public consumption. (Burp) They are described here for testing, exploration, education and entertainment purposes only. The Prototype shall NOT be used in production gadgets. (Like toasters, microwave ovens, and most definitely not, pressure cookers)_

![LoRa SX1262 Transceiver on PineDio Stack BL604](https://lupyuen.github.io/images/lorawan2-board.jpg)

[__CAUTION__: Always connect the Antenna before Powering On... Or the LoRa Transceiver may get damaged! See this](https://electronics.stackexchange.com/questions/335912/can-i-break-a-radio-tranceiving-device-by-operating-it-with-no-antenna-connected)

# LoRa SX1262 Transceiver

According to the PineDio Stack Schematic...

-   [__PineDio Stack Schematic (Prototype)__](https://wiki.pine64.org/wiki/Pinedio#PineDio_Stack)

Our __LoRa SX1262 Transceiver__ is wired onboard like so...

![LoRa SX1262 Transceiver wired to PineDio Stack BL604](https://lupyuen.github.io/images/pinedio-lora.png)

Note that the above SPI Pins are shared with the __SPI Flash and ST7789 Display__...

| GPIO Number | SPI Pin |
| :----------: | :------ |
| __`17`__ | Common SDO _(MOSI)_
| __`0`__  | Common SDI _(MISO)_
| __`11`__ | Common SCK
| __`14`__ | CS for SPI Flash
| __`20`__ | CS for ST7789
| __`15`__ | CS for SX1262

[(More about SDO and SDI)](https://www.oshwa.org/a-resolution-to-redefine-spi-signal-names)

We set the __Chip Select Pin (CS)__ to Low to select the __Active SPI Device__: LoRa SX1262, SPI Flash or ST7789 Display...

![SPI Bus on PineDio Stack](https://lupyuen.github.io/images/pinedio-spi.jpg)

To test the LoRa SX1262 Transceiver, we define the __GPIO Pin Numbers__ like so: [`lora-sx1262 / sx126x-board.h`](https://github.com/lupyuen/bl_iot_sdk/blob/pinedio/components/3rdparty/lora-sx1262/include/sx126x-board.h#L36-L50)

```c
//  Below are the pin numbers for PineDio Stack BL604 with onboard SX1262.
#define SX126X_SPI_IDX           0  //  SPI Port 0
#define SX126X_SPI_SDI_PIN       0  //  SPI Serial Data In Pin  (formerly MISO)
#define SX126X_SPI_SDO_PIN      17  //  SPI Serial Data Out Pin (formerly MOSI)
#define SX126X_SPI_CLK_PIN      11  //  SPI Clock Pin
#define SX126X_SPI_CS_PIN       15  //  SPI Chip Select Pin
#define SX126X_SPI_CS_OLD        8  //  Unused SPI Chip Select Pin
#define SX126X_NRESET           18  //  Reset Pin
#define SX126X_DIO1             19  //  DIO1
#define SX126X_BUSY_PIN         10  //  Busy Pin
#define SX126X_DEBUG_CS_PIN      5  //  Debug Chip Select Pin, mirrors the High / Low State of SX1262 Chip Select Pin. Set to -1 if not needed.
#define SX126X_TCXO_WAKEUP_TIME  5  //  Time required for the TCXO to wakeup (milliseconds)
#define SX126X_SPI_BAUDRATE  (200 * 1000)  //  SPI Frequency (200 kHz)
```

(`SX126X_DEBUG_CS_PIN` should be set to `-1` if we're not debugging. More about this later)

We define the __Chip Select Pins__ for SPI Flash and ST7789 Display as well: [`pinedio_lorawan / demo.c`](https://github.com/lupyuen/bl_iot_sdk/blob/pinedio/customer_app/pinedio_lorawan/pinedio_lorawan/demo.c#L101-L105)

```c
/// GPIO for SPI Flash Chip Select Pin. We must set this to High to deselect SPI Flash.
#define FLASH_CS_PIN     14

/// GPIO for ST7789 SPI Chip Select Pin. We must set this to High to deselect ST7789 Display.
#define DISPLAY_CS_PIN   20
```

# LoRaWAN Firmware

TODO

-   [__`pinedio_lorawan` Firmware__](https://github.com/lupyuen/bl_iot_sdk/tree/pinedio/customer_app/pinedio_lorawan)

-   [__`lorawan` Driver__](https://github.com/lupyuen/bl_iot_sdk/tree/pinedio/components/3rdparty/lorawan)

-   [__`lora-sx1262` Driver__](https://github.com/lupyuen/bl_iot_sdk/tree/pinedio/components/3rdparty/lora-sx1262)

## Deselect SPI Peripherals

TODO

From [`pinedio_lorawan / demo.c`](https://github.com/lupyuen/bl_iot_sdk/blob/pinedio/customer_app/pinedio_lorawan/pinedio_lorawan/demo.c#L107-L130)

```c
/// Set Chip Select pins to High, to deselect SX1262, SPI Flash and ST7789
int deselect_spi(void) {
  //  Configure Chip Select pins as GPIO Output Pins (instead of GPIO Input)
  int rc;
  rc = bl_gpio_enable_output(FLASH_CS_PIN,      0, 0);  assert(rc == 0);
  rc = bl_gpio_enable_output(DISPLAY_CS_PIN,    0, 0);  assert(rc == 0);
  rc = bl_gpio_enable_output(SX126X_SPI_CS_PIN, 0, 0);  assert(rc == 0);
  if (SX126X_DEBUG_CS_PIN >= 0) {  //  Mirror SX126X_SPI_CS_PIN
    rc = bl_gpio_enable_output(SX126X_DEBUG_CS_PIN, 0, 0);  assert(rc == 0);
  }
```

TODO

```c
  //  Set Chip Select pins to High, to deselect SX1262, SPI Flash and ST7789
  rc = bl_gpio_output_set(FLASH_CS_PIN,      1);  assert(rc == 0);
  rc = bl_gpio_output_set(DISPLAY_CS_PIN,    1);  assert(rc == 0);
  rc = bl_gpio_output_set(SX126X_SPI_CS_PIN, 1);  assert(rc == 0);
  if (SX126X_DEBUG_CS_PIN >= 0) {  //  Mirror SX126X_SPI_CS_PIN
    rc = bl_gpio_output_set(SX126X_DEBUG_CS_PIN, 1);  assert(rc == 0);
  }
  return 0;
}
```

TODO: Called by [`init_lorawan`](https://github.com/lupyuen/bl_iot_sdk/blob/pinedio/customer_app/pinedio_lorawan/pinedio_lorawan/lorawan.c#L167-L173)

TODO: This code comes from

![Deselect SPI Peripherals](https://lupyuen.github.io/images/lorawan2-deselect.png)

## Swap SPI Pins

TODO

From [`lora-sx1262 / sx126x-board.c`](https://github.com/lupyuen/bl_iot_sdk/blob/pinedio/components/3rdparty/lora-sx1262/src/sx126x-board.c#L168-L202)

```c
/// Initialise GPIO Pins and SPI Port. Called by SX126xIoIrqInit.
void SX126xIoInit( void ) {
  //  Configure the pins for GPIO Input / Output
  GpioInitOutput( SX126X_SPI_CS_PIN, 1 );
  GpioInitInput( SX126X_BUSY_PIN, 0, 0 );
  GpioInitInput( SX126X_DIO1, 0, 0 );
  if (SX126X_DEBUG_CS_PIN >= 0) { GpioInitOutput( SX126X_DEBUG_CS_PIN, 1 ); }

  //  Note: We must swap MISO and MOSI to comply with the SPI Pin Definitions in BL602 / BL604 Reference Manual
  int rc = GLB_Swap_SPI_0_MOSI_With_MISO(ENABLE);  assert(rc == 0);
```

TODO

```c
  //  Configure the SPI Port
  rc = spi_init(
    &spi_device,     //  SPI Device
    SX126X_SPI_IDX,  //  SPI Port
    0,               //  SPI Mode: 0 for Controller
    //  TODO: Due to a quirk in BL602 SPI, we must set
    //  SPI Polarity-Phase to 1 (CPOL=0, CPHA=1).
    //  But actually Polarity-Phase for SX126X should be 0 (CPOL=0, CPHA=0). 
    1,                    //  SPI Polarity-Phase
    SX126X_SPI_BAUDRATE,  //  SPI Frequency
    2,                    //  Transmit DMA Channel
    3,                    //  Receive DMA Channel
    SX126X_SPI_CLK_PIN,   //  SPI Clock Pin 
    SX126X_SPI_CS_OLD,    //  Unused SPI Chip Select Pin
    SX126X_SPI_SDO_PIN,   //  SPI Serial Data Out Pin (formerly MOSI)
    SX126X_SPI_SDI_PIN    //  SPI Serial Data In Pin  (formerly MISO)
  );
  assert(rc == 0);
}
```

TODO: This code comes from

![Swap SPI Pins](https://lupyuen.github.io/images/lorawan2-swap.png)

TODO: Sync with pine64

# Run The Firmware

TODO

From [`pinedio_lorawan`](https://github.com/lupyuen/bl_iot_sdk/tree/pinedio/customer_app/pinedio_lorawan#lorawan-commands)

```bash
#  Start LoRa background task
create_task

#  Init LoRaWAN driver
init_lorawan

#  Device EUI: Copy from ChirpStack: Applications -> app -> Device EUI
las_wr_dev_eui 0x4b:0xc1:0x5e:0xe7:0x37:0x7b:0xb1:0x5b

#  App EUI: Not needed for ChirpStack, set to default 0000000000000000
las_wr_app_eui 0x00:0x00:0x00:0x00:0x00:0x00:0x00:0x00

#  App Key: Copy from ChirpStack: Applications -> app -> Devices -> device_otaa_class_a -> Keys (OTAA) -> Application Key
las_wr_app_key 0xaa:0xff:0xad:0x5c:0x7e:0x87:0xf6:0x4d:0xe3:0xf0:0x87:0x32:0xfc:0x1d:0xd2:0x5d

#  Join LoRaWAN network, try 3 times
las_join 3

#  Open LoRaWAN port 2 (App Port)
las_app_port open 2

#  Send data to LoRaWAN port 2, 5 bytes, unconfirmed (0)
las_app_tx 2 5 0
```

![](https://lupyuen.github.io/images/lorawan2-commands.png)

# LoRaWAN Gateway

TODO

![](https://lupyuen.github.io/images/lorawan2-chirpstack.png)

TODO4

![](https://lupyuen.github.io/images/lorawan2-chirpstack2.png)

# Logic Analyser

TODO

PineDio Stack's __GPIO Connector__ (at right) exposes the SPI Pins: SDO _(formerly MOSI)_, SDI _(formerly MISO)_ and SCK

![PineDio Stack GPIO Connector](https://lupyuen.github.io/images/pinedio-gpio2.jpg)

We __connect our Logic Analyser__ to the GPIO Connector like so...

| GPIO Number | SPI Pin | Connector Pin |
| :----------: | :------ | :------------: |
| __`17`__ | Common SDO _(MOSI)_ | `7`
| __`0`__  | Common SDI _(MISO)_ | `17`
| __`11`__ | Common SCK | `4`
| __`5`__ | Debug CS | `2`

![Logic Analyser connected to PineDio Stack](https://lupyuen.github.io/images/pinedio-logic2.jpg)

_What about the SX1262 Chip Select Pin: GPIO 15?_

Unfortunately __GPIO 15 is not exposed__ on the GPIO Connector.

But remember: Everything we do to GPIO 15, we __do the same to GPIO 5!__

__GPIO 5 is exposed__ on the GPIO Connector and it mirrors the GPIO High / Low state of GPIO 15.

Thus we simply connect our Logic Analyser to __GPIO 5 as the Chip Select Pin!__ (Pic above)

Let's look at the data collected by our Logic Analyser...

![LoRa SX1262 with Logic Analyser](https://lupyuen.github.io/images/lorawan2-logic.png)

# Spectrum Analyser

TODO

-   [__"Visualise LoRa with Software Defined Radio"__](https://lupyuen.github.io/articles/lora#visualise-lora-with-software-defined-radio)

![LoRa SX1262 visualised with SDR](https://lupyuen.github.io/images/pinedio-chirp2.jpg)

# Security

TODO: Injecting keys, one-time

TODO: Glitching

TODO: WiFi lora bt gateway, Very basic functionality

TODO: Xmpp, Matrix, Or custom LoRaWAN

-   [ATECC608A Library for Helium](https://github.com/helium/ecc508)

-   ["ATECC608A Secure Element on The Things Network"](https://www.thethingsindustries.com/docs/devices/atecc608a/claim/)

-   ["Internet of Things. A Confluence of Many Disciplines"](https://books.google.com.sg/books?id=3F7XDwAAQBAJ&pg=PA302&lpg=PA302&dq=ATECC608A&source=bl&ots=80tY23LkbA&sig=ACfU3U2Ngp_Rao6FG1hpS2ays4O-vNEkCg&hl=en&sa=X&ved=2ahUKEwi_19-4ovnyAhWXILcAHcpQDaY4MhDoAXoECBIQAw#v=onepage&q=ATECC608A&f=false)

-   ["Designing a Community-Driven Decentralized Storage Network for IoT Data"](https://matheo.uliege.be/bitstream/2268.2/11657/12/thesis.pdf)

# Seeking Volunteers!

I'm really excited that PineDio Stack BL604 will be available soon!

But in the meantime, JF and I have __plenty to test on PineDio Stack__...

1.  ST7789 Display _(SPI)_
1.  LoRa SX1262 _(SPI)_
1.  SPI Flash _(SPI)_
1.  Accelerometer _(I2C)_
1.  Heart Rate Sensor _(I2C)_
1.  Touch Panel _(I2C)_
1.  Vibrator _(GPIO)_
1.  Push Button _(GPIO)_
1.  WiFi
1.  Bluetooth LE
1.  JTAG Debugging
1.  Battery Charging
1.  Solar Power

[__Please let us know__](https://twitter.com/MisterTechBlog) if you're keen to help! 🙏

# What's Next

TODO

-   [Sponsor me a coffee](https://github.com/sponsors/lupyuen)

-   [Read "The RISC-V BL602 / BL604 Book"](https://lupyuen.github.io/articles/book)

-   [Check out my articles](https://lupyuen.github.io)

-   [RSS Feed](https://lupyuen.github.io/rss.xml)

_Got a question, comment or suggestion? Create an Issue or submit a Pull Request here..._

[`lupyuen.github.io/src/lorawan2.md`](https://github.com/lupyuen/lupyuen.github.io/blob/master/src/lorawan2.md)

# Notes

1.  This article is the expanded version of [this Twitter Thread](https://twitter.com/MisterTechBlog/status/1436128755987058691)
