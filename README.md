<!--[![Build Status](https://travis-ci.com/beegee-tokyo/nRF52_OLED.svg?branch=master)](https://travis-ci.com/beegee-tokyo/nRF52_OLED)-->
[![Build Status](https://github.com/beegee-tokyo/nRF52_OLED/workflows/Arduino%20Library%20CI/badge.svg)](https://github.com/beegee-tokyo/nRF52_OLED/actions)
# nRF52 OLED SSD1306 SH1106

## Based on the [ESP8266_SSD1306](https://github.com/squix78/esp8266-oled-ssd1306.git) V4.0.0 library written by Daniel Eichhorn & Fabrice Weinberg
## Adapted to work with nRF52 by Bernd Giesecke
## SPI version is not tested on nRF52 because I have no SPI OLED display.

### Runs as well with ESP32, RP2040, STM32WLE5 (tested with RAK4631, RAK11200, RAK11310, RAK3372)

This is a driver for the SSD1306 and SH1106 based 128x64 or 128x32 pixel OLED displays running on the Arduino/nRF52 platform.
Can be used with either the I2C or SPI version of the display

You can either download this library as a zip file and unpack it to your Arduino/libraries folder or (once it has been added) choose it from the Arduino library manager.

It is also available as a platformio library. Just execute the following command:
```
platformio lib install nRF52_OLED
```

## Credits

This library has initially been written by Daniel Eichhorn (@squix78). Many thanks go to Fabrice Weinberg (@FWeinb) for optimizing and refactoring many aspects of the library. Also many thanks to the many committers who helped to add new features and who fixed many bugs.
The init sequence for the SSD1306 was inspired by Adafruit's library for the same display.

## Usage

Check out the examples folder for a few comprehensive demonstrations how to use the library. 

## Features

* Draw pixels at given coordinates
* Draw lines from given coordinates to given coordinates
* Draw or fill a rectangle with given dimensions
* Draw Text at given coordinates:
 * Define Alignment: Left, Right and Center
 * Set the Fontface you want to use (see section Fonts below)
 * Limit the width of the text by an amount of pixels. Before this widths will be reached, the renderer will wrap the text to a new line if possible
* Display content in automatically side scrolling carousel
 * Define transition cycles
 * Define how long one frame will be displayed
 * Draw the different frames in callback methods
 * One indicator per frame will be automatically displayed. The active frame will be displayed from inactive once

## Fonts

Fonts are defined in a proprietary but open format. You can create new font files by choosing from a given list
of open sourced Fonts from this web app: http://oleddisplay.squix.ch
Choose the font family, style and size, check the preview image and if you like what you see click the "Create" button. This will create the font array in a text area form where you can copy and paste it into a new or existing header file.


![FontTool](https://github.com/beegee-tokyo/nRF52_OLED/raw/master/resources/FontTool.png)

## Hardware Abstraction

The library supports different protocols to access the OLED display. Currently there is support for I2C using the built in Wire.h library and it also supports displays which come with the SPI interface.

### I2C with Wire.h

```C++
#include <Wire.h>  
#include "nrf52_SSD1306Wire.h"

SSD1306Wire display(ADDRESS, SDA, SDC);
```
or for a SH1106:
```C++
#include <Wire.h>  
#include "nrf52_SH1106Wire.h"

SH1106Wire display(ADDRESS, SDA, SDC);
```

### SPI

```C++
#include <SPI.h>
#include "SSD1306Spi.h"

SSD1306Spi display(RES, DC, CS);
```
or for the SH1106:
```C++
#include <SPI.h>
#include "SH1106Spi.h"

SH1106Spi display(RES, DC, CS);
```

## API

### Display Control

```C++
// Initialize the display
void init();

// Free the memory used by the display
void end();

// Cycle through the initialization
void resetDisplay(void);

// Connect again to the display through I2C
void reconnect(void);

// Turn the display on
void displayOn(void);

// Turn the display offs
void displayOff(void);

// Clear the local pixel buffer
void clear(void);

// Write the buffer to the display memory
void display(void);

// Inverted display mode
void invertDisplay(void);

// Normal display mode
void normalDisplay(void);

// Set display contrast
// really low brightness & contrast: contrast = 10, precharge = 5, comdetect = 0
// normal brightness & contrast:  contrast = 100
void setContrast(uint8_t contrast, uint8_t precharge = 241, uint8_t comdetect = 64);

// Convenience method to access
void setBrightness(uint8_t);

// Turn the display upside down
void flipScreenVertically();

// Draw the screen mirrored
void mirrorScreen();
```

## Pixel drawing

```C++

/* Drawing functions */
// Sets the color of all pixel operations
void setColor(OLEDDISPLAY_COLOR color);

// Draw a pixel at given position
void setPixel(int16_t x, int16_t y);

// Draw a line from position 0 to position 1
void drawLine(int16_t x0, int16_t y0, int16_t x1, int16_t y1);

// Draw the border of a rectangle at the given location
void drawRect(int16_t x, int16_t y, int16_t width, int16_t height);

// Fill the rectangle
void fillRect(int16_t x, int16_t y, int16_t width, int16_t height);

// Draw the border of a circle
void drawCircle(int16_t x, int16_t y, int16_t radius);

// Fill circle
void fillCircle(int16_t x, int16_t y, int16_t radius);

// Draw a line horizontally
void drawHorizontalLine(int16_t x, int16_t y, int16_t length);

// Draw a lin vertically
void drawVerticalLine(int16_t x, int16_t y, int16_t length);

// Draws a rounded progress bar with the outer dimensions given by width and height. Progress is
// a unsigned byte value between 0 and 100
void drawProgressBar(uint16_t x, uint16_t y, uint16_t width, uint16_t height, uint8_t progress);

// Draw a bitmap in the internal image format
void drawFastImage(int16_t x, int16_t y, int16_t width, int16_t height, const uint8_t *image);

// Draw a XBM
void drawXbm(int16_t x, int16_t y, int16_t width, int16_t height, const char* xbm);
```

## Text operations

``` C++
void drawString(int16_t x, int16_t y, String text);

// Draws a String with a maximum width at the given location.
// If the given String is wider than the specified width
// The text will be wrapped to the next line at a space or dash
void drawStringMaxWidth(int16_t x, int16_t y, int16_t maxLineWidth, String text);

// Returns the width of the const char* with the current
// font settings
uint16_t getStringWidth(const char* text, uint16_t length);

// Convencience method for the const char version
uint16_t getStringWidth(String text);

// Specifies relative to which anchor point
// the text is rendered. Available constants:
// TEXT_ALIGN_LEFT, TEXT_ALIGN_CENTER, TEXT_ALIGN_RIGHT, TEXT_ALIGN_CENTER_BOTH
void setTextAlignment(OLEDDISPLAY_TEXT_ALIGNMENT textAlignment);

// Sets the current font. Available default fonts
// ArialMT_Plain_10, ArialMT_Plain_16, ArialMT_Plain_24
// Or create one with the font tool at http://oleddisplay.squix.ch
void setFont(const uint8_t* fontData);
```

## Ui Library (OLEDDisplayUi)

The Ui Library is used to provide a basic set of Ui elements called, `Frames` and `Overlays`. A `Frame` is used to provide
information the default behaviour is to display a `Frame` for a defined time and than move to the next. The library also provides an `Indicator` that will be updated accordingly. An `Overlay` on the other hand is a pieces of information (e.g. a clock) that is displayed always at the same position.


```C++
/**
 * Initialise the display
 */
void init();

/**
 * Configure the internal used target FPS
 */
void setTargetFPS(uint8_t fps);

/**
 * Enable automatic transition to next frame after the some time can be configured with
 * `setTimePerFrame` and `setTimePerTransition`.
 */
void enableAutoTransition();

/**
 * Disable automatic transition to next frame.
 */
void disableAutoTransition();

/**
 * Set the direction if the automatic transitioning
 */
void setAutoTransitionForwards();
void setAutoTransitionBackwards();

/**
 *  Set the approx. time a frame is displayed
 */
void setTimePerFrame(uint16_t time);

/**
 * Set the approx. time a transition will take
 */
void setTimePerTransition(uint16_t time);

/**
 * Draw the indicator.
 * This is the default state for all frames if
 * the indicator was hidden on the previous frame
 * it will be slided in.
 */
void enableIndicator();

/**
 * Don't draw the indicator.
 * This will slide out the indicator
 * when transitioning to the next frame.
 */
void disableIndicator();

/**
 * Enable drawing of all indicators.
 */
void enableAllIndicators();

/**
 * Disable drawing of all indicators.
 */
void disableAllIndicators();

/**
 * Set the position of the indicator bar.
 */
void setIndicatorPosition(IndicatorPosition pos);

/**
 * Set the direction of the indicator bar. Defining the order of frames ASCENDING / DESCENDING
 */
void setIndicatorDirection(IndicatorDirection dir);

/**
 * Set the symbol to indicate an active frame in the indicator bar.
 */
void setActiveSymbol(const char* symbol);

/**
 * Set the symbol to indicate an inactive frame in the indicator bar.
 */
void setInactiveSymbol(const char* symbol);

/**
 * Configure what animation is used to transition from one frame to another
 */
void setFrameAnimation(AnimationDirection dir);

/**
 * Add frame drawing functions
 */
void setFrames(FrameCallback* frameFunctions, uint8_t frameCount);

/**
 * Add overlays drawing functions that are draw independent of the Frames
 */
void setOverlays(OverlayCallback* overlayFunctions, uint8_t overlayCount);

/**
 * Set the function that will draw each step
 * in the loading animation
 */
void setLoadingDrawFunction(LoadingDrawFunction loadingDrawFunction);

/**
 * Run the loading process
 */
void runLoadingProcess(LoadingStage* stages, uint8_t stagesCount);

// Manuell Controll
void nextFrame();
void previousFrame();

/**
 * Switch without transition to frame `frame`.
 */
void switchToFrame(uint8_t frame);

/**
 * Transition to frame `frame`, when the `frame` number is bigger than the current
 * frame the forward animation will be used, otherwise the backwards animation is used.
 */
void transitionToFrame(uint8_t frame);

// State Info
OLEDDisplayUiState* getUiState();

// This needs to be called in the main loop
// the returned value is the remaining time (in ms)
// you have to draw after drawing to keep the frame budget.
int8_t update();
```

## Example: SSD1306Demo

### Frame 1
![DemoFrame1](https://github.com/beegee-tokyo/nRF52_OLED/raw/master/resources/DemoFrame1.jpg)

This frame shows three things:
 * How to draw an xbm image
 * How to draw a static text which is not moved by the frame transition
 * The active/inactive frame indicators

### Frame 2
![DemoFrame2](https://github.com/beegee-tokyo/nRF52_OLED/raw/master/resources/DemoFrame2.jpg)

Currently there are one fontface with three sizes included in the library: Arial 10, 16 and 24. Once the converter is published you will be able to convert any ttf font into the used format.

### Frame 3

![DemoFrame3](https://github.com/beegee-tokyo/nRF52_OLED/raw/master/resources/DemoFrame3.jpg)

This frame demonstrates the text alignment. The coordinates in the frame show relative to which position the texts have been rendered.

### Frame 4

![DemoFrame4](https://github.com/beegee-tokyo/nRF52_OLED/raw/master/resources/DemoFrame4.jpg)

This shows how to use define a maximum width after which the driver automatically wraps a word to the next line. This comes in very handy if you have longer texts to display.

### SPI version

![SPIVersion](https://github.com/beegee-tokyo/nRF52_OLED/raw/master/resources/SPI_version.jpg)

This shows the code working on the SPI version of the display. See demo code for ESP8266 pins used.

