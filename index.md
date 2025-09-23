# vlcb2mgc

### Library for decoding and encoding CBUS Layout Control Bus (LCB) messages, including extended messages used for firmware download

There is a very comprehensive set of tests for this - see **tests** section further below.
The decoding and encoding of standard (11 bit) CAN ID messages follows the CBUS specification available here [CBUS dev guide](`https://www.merg.org.uk/merg_wiki/doku.php?id=public:cbuspublic:developerguide`)

At the time of writing, there isn't a formal definition of extended (29 bit) CAN ID message formats, so the methods are based on current implementations.

<h3>Full API details available <a href="cbusLibrary.html">here</a></h3>

## Installation

```
npm install vlcb2mgc
```

   (note all lower case - npm no longer allows uppercase)

## Usage

```
const vlcb2mgc= require('vlcb2mgc')
```

An instance of the class is created automatically, so can be used immediately.

## General information

### decoding

for decoding, there's one common function (as the opcode is embedded in the message)

```
 var cbusMsg = vlcb2mgc.decode(
```

The decode function returns a collection of values, with "encoded", "ID_TYPE" and "text" being standard for all messages.
See an expanded description of 'eventName' further below.

example CBUS (11 bit Id) message decode:

```
    {
      "encoded": ":SB780ND200010000FF00;",
      "ID_TYPE":"S",
      "mnemonic": "EVLRN",
      "opCode": "D2",
      "nodeNumber": 1,
      "eventNumber": 0,
      "eventName": "00010000",
      "eventVariableIndex": 255,
      "eventVariableValue": 0,
      "text": "EVLRN (D2) nodeNumber 1 eventNumber 0 eventName 00010000 Event Variable Index 255 Event Variable Value 0"
    }
```

Note that some properties have full names to avoid confusion (e.g. eventVariableIndex, rather than EV#)

example extended (29 bit Id) message decode:

```
    {
        "encoded":":X00080004N000000000D040000;",
        "ID_TYPE":"X",
        "operation":"PUT",
        "type":"CONTROL",
        "address":"000000",
        "RESVD":0,
        "CTLBT":13,
        "SPCMD":4,
        "CPDTL":0,
        "CPDTH":0
        "text": {"encoded":":X00080004N000000000D040000;","ID_TYPE":"X","operation":"PUT","type":"CONTROL","address":"000000","RESVD":0,"CTLBT":13,"SPCMD":4,"CPDTL":0,"CPDTH":0}
    }
```

### encoding

For encoding standard CBUS messages, there's one function encode() thats takes a JSON object, which needs the 'mnemonic' for the specific opcode, and any parameters the opcode requires.

The format of the JSON object follows exactly the same syntax as the result from the decode() function above.

Each opcode also it's own function, that has individual parameters, as the type & number of parameters vary with opCode.

```
var encode = vlcb2mgc.encodeEVLRN(nodeNumber, eventNumber, variableIndex, eventVariableValue);
```

There are only separate functions for encoding extended CBUS messages currently

### eventIdentifier

An event is identified by 4 bytes made up from either the 2 byte node number plus the 2 byte event number in the case of a 'long' event, or in a 'short' event just the 2 byte device number (with the top 2 bytes set to zero).

The term 'node number' is also used by itself in some opcodes to denote either the destination or source address of that message, so to avoid confusion, the term 'eventIdentifier' is used for the whole 4 bytes.

The 'eventIdentifier' is used to provide the correct value depending on the type of event ('long' or 'short'), in an 8 digit hexadecimal string with leading zero's.

i.e.

'long' event  = node number + event number `<br>`
'short' event =    0000     + device number `<br>`
`<br>`
The command 'ENRSP' also expects this four byte 'eventIdentifier' as a parameter as well as the node number `<br>`

### CAN header (11 bit CAN identifier)

There are default values for the 11 bit CAN header used when encoding messages, but these can be changed using the following function

```
setCanHeader(MjPri, CAN_ID)
```

Once set, the new values will continue to be used for subsequent encodes (or until the program is restarted).

Note that MinPri is specifically defined for each opCode, so is set for each individual opCode and not expected to be changed.

### CAN header (29 bit CAN identifier)

The absence of a generic definition for the 29 bit identifier means that the identifier is hard coded to match existing bootloader implementations .

## Tests

There is a comprehensive suite of unit tests for all the message decode/encodes, which also creates log files.

### How to run all tests

```
npm test
```

Will run al the tests - note there are more than 27,000 tests, and although doesn't take too long, it does create a huge log file

### How to run specific test

You can also run a specific test, which produces a much more workable log file.

```
npm test -- --grep "EVLRN"
```

### Tests log file

For all tests, the log file is created in /tests/logs, and is overwritten on each test run.

## Documentation

The project uses JSDocs to generate most of the documentation.

### How to run jsdoc

```
npm run jsdoc
```

This creates the documents in the 'out' folder, and are copied manually in the root project directory when finalised.
