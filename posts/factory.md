```
Our infrastructure is under attack! The HMI interface went offline and we lost control of some critical PLCs in our ICS system. Moments after the attack started we managed to identify the target but did not have time to respond. The water storage facility's high/low sensors are corrupted thus setting the PLC into a halt state. We need to regain control and empty the water tank before it overflows. Our field operative has set a remote connection directly with the serial network of the system.
```

![[Pasted image 20230828215353.png]]

![[Pasted image 20230828215026.png]]


### Approach

- https://en.wikipedia.org/wiki/Ladder_logic

__Rung input__

Checkers (contacts)

- `—[ ]—` Normally open contact, closed whenever its corresponding coil or an input which controls it is energized. (Open contact at rest.)
- `—[\]—` Normally closed ("not") contact, closed whenever its corresponding coil or an input which controls it is not energized. (Closed contact at rest.)

__Rung output__

Actuators (coils)

- `—( )—` Normally inactive coil, energized whenever its rung is closed. (Inactive at rest.)
- `—(\)—` Normally active ("not") coil, energized whenever its rung is open. (Active at rest.)


```
We need to regain control and empty the water tank before it overflows.
```

`in_valve` should be 0 and `out_valve` should be 1. Currently, its the opposite.

I think activating `manual_mode` instead of `auto_mode` and reversing valve values through modbus should do the trick.

Can't get the format correctly. 
```
The following is an example of a Modbus RTU request for obtaining the AI value of the holding registers from registers # 40108 to 40110 with the address of the device 17.
```

__11 03 006B 0003 7687__

|   |   |
|---|---|
|**11**|THE ADDRESS OF THE SLAVEID DEVICE (17 = **11** HEX)|
|**03**|Functional code Function Code|
|**006B**|The address of the first register (40108-40001 = 107 = **6B** hex)|
|**0003**|The number of required registers (reading 3 registers from 40108 to 40110)|
|**7687**|CRC checksum|

No need for checksum so tried :

```
1. Get status of system
2. Send modbus command
3. Exit
Select: 2
Modbus command: 82 03 006B 0003
Invalid command: command is not the right length
1. Get status of system
2. Send modbus command
3. Exit
Select:
```

Apparently spaces are not needed
```
Modbus command: 0x41B15C2922
Invalid command: String contains non hex characters
```

82 -> 0x52
3   -> 0x03

`5203006B0003` is in the correct format.


**Function 05 (05hex) Write Single Coil**

Writes a single coil to either ON or OFF.

**Request** 
The request message specifies the coil reference to be written. Coils are addressed starting at zero-coil 1 is addressed as 0.

The requested ON / OFF state is specified by a constant in the request data field. A value of FF 00 hex requests the coil to be ON. A value of 00 00 requests it to be OFF. All other values are illegal and will not affect the coil.

Here is an example of a request to write coil 173 ON in slave device 17:

|   |   |   |
|---|---|---|
|**Field Name**|**RTU (hex)**|**ASCII Characters**|
|Header|None|: (Colon)|
|Slave Address|11|1 1|
|Function|05|0 5|
|Coil Address Hi|00|0 0|
|Coil Address Lo|AC|A C|
|Write Data Hi|FF|0 0|
|Write Data Lo|00|F F|
|Error Check Lo|4E|LRC (3 F)|
|Error Check Hi|8B|None|
|Trailer|None|CR LF|
|Total Bytes|8|17|
https://www.modbustools.com/modbus.html#function05

![[Pasted image 20230828225736.png]]


Setting `manual_mode_control` to 1:

hex(9947) = 26DB
hex(82) = 52 
`520526DBFF00` worked.

```
1. Get status of system
2. Send modbus command
3. Exit
Select: 1
{"auto_mode": 1, "manual_mode": 0, "stop_out": 0, "stop_in": 0, "low_sensor": 0, "high_sesnor": 0, "in_valve": 1, "out_valve": 0, "flag": "HTB{}"}
1. Get status of system
2. Send modbus command
3. Exit
Select: 2
Modbus command: 520526DBFF00
Modbus command sent to the network!
1. Get status of system
2. Send modbus command
3. Exit
Select: 1
{"auto_mode": 0, "manual_mode": 1, "stop_out": 0, "stop_in": 0, "low_sensor": 0, "high_sesnor": 0, "in_valve": 1, "out_valve": 0, "flag": "HTB{}"}
```

### Deactivating `in_valve`:

`52 05 000C 0000`

Trying to make `in_valve` 0 through the above doesn't work.

Hmmm...

![[Pasted image 20230828230607.png]]

Since `manual_mode` is activated, need to enable `cutoff_in` to disable `in_valve`.

![[Pasted image 20230828230751.png]]

```
{"auto_mode": 0, "manual_mode": 1, "stop_out": 0, "stop_in": 1, "low_sensor": 0, "high_sesnor": 0, "in_valve": 0, "out_valve": 0, "flag": "HTB{}"}
```

Need to enable `out_valve` now.

![[Pasted image 20230828231134.png]]

Enabling `force_start_out` should do the trick.

```
Select: 2
Modbus command: 52050034FF00
Modbus command sent to the network!
1. Get status of system
2. Send modbus command
3. Exit
Select: 1
{"auto_mode": 0, "manual_mode": 1, "stop_out": 0, "stop_in": 1, "low_sensor": 0, "high_sesnor": 0, "in_valve": 0, "out_valve": 1, "flag": "HTB{14dd32_1091c_15_7h3_1091c_c12cu175_f02_1ndu572141_5y573m5}"}

```

