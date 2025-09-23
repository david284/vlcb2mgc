# vlcb2mcg

Library for decoding and encoding VLCB/CBUS Layout Control Bus (LCB) messages into a modified version of Grid Connect (MGC) format, as used by many MERG devices (see below)
There is a very comprehensive set of automated unit tests for this

CBUS specifications are available here
`<a href="https://www.merg.org.uk/merg_wiki/doku.php?id=public:cbuspublic:developerguide">`CBUS dev guide `</a>`

For full documentation on API, tests etc.. follow this link `<a href="https://david284.github.io/vclb2mgc/">`documentation `</a>`

## Installation

    npm install vlcb2mgc

   (note all lower case - npm no longer allows uppercase)

## usage

    const vlcb2mgc = require('vlcb2mgc')
An instance of the class is created automatically, so can be used immediately

## modified Grid Connect

Grid Connect is an established format used to encode a bit orientated CAN BUS message into a byte orientated ASCII string that can be sent over a serial channel (or other transport methods), for use with devices like the MERG CANUSB4 serial to CAN adapter

The modifed version used by many MERG devices was originally created to make it easier to use with PIC embedded CAN interfaces.

1. The header field has been converted to a fixed length - 4 bytes for standard CAN messages, and 8 bytes for extended CAN messages
2. The bits within the header have been assigned to match the registers in the PIC CAN interface
