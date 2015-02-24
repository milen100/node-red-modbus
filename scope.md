Overview:

- Create 3 node-red compatible nodes:  modbus-in; modbus-out; modbus-config
- Follow node-red coding conventions
- See examples here: https://github.com/node-red/node-red/tree/master/nodes/core/io
- Use modbus node: https://www.npmjs.com/package/modbus for protocol stack

.html for config nodes
- Modbus TCP needs to be unique by IP Address and Port, Max connections (default 1)
- Modbus RTU needs to be unique by slaveId, device and baud (see Assumptions)
- Specify whether slave or master (modbus-in only)
- Config node needs a name and label identifying type and connection paramaters (summary).
- Config node exposes its connection params, mode (slave/master), name and connection context (ctx)  - see server.js example

.js for config nodes
- Supports both slave and master modes
- For slave mode need to be able to bind to specified address. Create a modbus slave server; Estabish and listen to connection; or warn.  
- For master mode, needs to be able to handle disconnects and reconnect
- Make cconnection context available to owner node.

.html form elements for modbus-in  node-red html interface

- Name (required)
- Topic - default function code + register i.e. 40001, or overwritten by mqtt topic definition
- Function Code (limited to read for read/write registers ) required
- Register value (uint 0-9999) required
- Length (uint number of 16bit registers - default 1) required
- Type ( integer , decimal(float), hex, bit ) use radio buttons. default integer?
- Poll interval (milliseconds)
- Inject message even if unchanged (checkbox)?

.js for modbus-in

require modbus
on start and poll
check value of register(s) defined in html - ctx.getReg
if new or changed (and report if unchanged)
format register value as type (use JSON [address,value] or [[addressx,value],[addressx+1,value]] where length >1)
send message

Outputs 1. Topic + Payload (register value + length cast as type)

.html form elements for modbus-out node

- Name (required)
- Function Code (uint limited to write only or read/write registers ) not mandatory
- Register value (uint 0-9999) not mandatory
Note: above paramaters can be overwritten by incoming topic if not set. i.e. 40001
- Type ( integer , decimal(float), hex, bit )

.js logic for modbus-out

listen for on('message',...)
perform any type casting operations and validations - i.e. isInt()
derrive function code and register from topic and/or node params
perform ctx.setBit,ctx.setReg operations etc. based on html form elements
flag any errors

Inputs 1:Topic (i.e. 40001) +  Payload (value)
Outputs 1. Logging of operation

Assumptions:

- Modbus RTU currently out of scope.  Leave placeholders where required but omit implementation.


Non functional requirements:

- Use modbus node: https://www.npmjs.com/package/modbus for protocol stack
- This uses the node libmodbus libraries:  https://github.com/tuxnsk/nodejs_libmodbus
- modbus config would have default settings to assist in its form creation and would expose the context and connection/config parameters.
- create test scripts and sample flow



