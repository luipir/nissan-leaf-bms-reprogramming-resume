# Notes about leaf BMS reprogramming

This are notes to resume a long thread started by @safetyuggs in https://mynissanleaf.com
This thread explains all the steps that lead to Nissan Leaf LBC / BMS reprogramming.
Lack of important KWP files that have to be downloaded from Nissan Tech webpage paying a fee.

## Webography

https://mynissanleaf.com/threads/reverse-engineering-bms-firmware-reflashing-bms.36627/
https://mynissanleaf.com/threads/can-hx-battery-internal-resistance-be-improved.35235/#post-641654

## notes from threads

### Can messages to update BMS

(0x79B-0x7BB request/reply)[https://mynissanleaf.com/threads/reverse-engineering-bms-firmware-reflashing-bms.36627/#post-652306
https://mynissanleaf.com/threads/bms-details.17470

## code challenge 0x65

10s to resolve code challange request by 0x65 message https://mynissanleaf.com/threads/reverse-engineering-bms-firmware-reflashing-bms.36627/post-652602
challenge implemented in PID27_65 code.

## BMS S/N vs Model

293A0 General Leaf model

4NR4A is AZE0 30kWh BMS
6WK4A is ZE1 60kWh (not 62) Uk Magneti Marelli

3NA0A/3NA0C -24 kWh ZE0(without heater battery)
3NA1A/3NA1B - 24 kWh ZE0(battery heater)
3NF1A - 24 kWh AZE0
9RB3A - 24 kWh ( 2015 m/y )
4NP2A - 24 kWh ( 2016 m/y )
4NP4A/ - 30 kWh (old version)
4NP4C - 30 kWh (new version)
5SA2B/5SH2D - 40 kWh
6SH1A - 62 kWh

4FA2B - 24 kWh e-NV 200 (without heater)
6WX2A - 40 kW e-NV200

### Board/Firmware incompatibilities

You can reprogram a AZE0 24kwh with 30kwh firmware, but you can't put 30kwh firmware on the ZE0 BMS - It'll probably flash just fine but there's a good chance it'll brick it - None of the entry points in the code are in the correct place.


## BMS version  vs CRC

from https://mynissanleaf.com/threads/reverse-engineering-bms-firmware-reflashing-bms.36627/page-13#post-654844

BMS Firmware Version - CRC reflash IV:
5SA2AVB - A6BC
3NK2ARB - C5ED
3NK0BRB - D30E
3NA0DBE - D91C


АZЕО-24
3NF1ARBQ - 9BF7

ZE0-24
3NA0DPBE - C01D

## Flashing steps

Flashing requires an erase of the current firmware. If you do not have access to either the original firmware, or a genuine updated firmware, you cannot recover your BMS. It won't be bricked, it can still be reflashed but it will not function as a BMS without the firmware.

- KWP2BIN will convert your KWP into a Binary, ready for ghidra or patching.
- Run fixchecksums.bat on the patched binary when you're done
- BIN2KWP will convert it back to a KWP
- leafBMSflash will write the KWP to the BMS

If you're using an AZE0 firmware, substitute 3E002_checksum.exe with the AZE0 version in the batch file.

NOTE: the bin2kwp is fully compatible with the flash app, probably not compatible with Consult but untested.

Python version for kwp2bin is here: https://gist.github.com/developerfromjokela/f15d77d4c55f66c205712a2da90bee16#file-main-py

### How to dump firmware with glitch attack

see video: https://www.youtube.com/watch?v=SQbSlcoTh-I
get from: https://mynissanleaf.com/threads/reverse-engineering-bms-firmware-reflashing-bms.36627/post-657535

### Tested boards

3NA0A/B/C BMS's 

### flashing notes

tool will work directly with the bms, or via the obd2 port (evcan). I use tactrix open port mostly, but UCAN is still suppoted, just a lot slower.

### Falshing program with Tactrix

Note! this is currently only suitable for 3NA0x ECU's as it writes 3NA0D as the firmware label. I'll correct this at a later data for other BMS variants.


https://cdn.imagearchive.com/mynissanleaf/internal_data/attachments/6/6185-d3c5f71d7c47edc565ddfd6902f68e01.data?response-expires=Sun%2C%2024%20Aug%202025%2009%3A50%3A53%20GMT&response-content-disposition=attachment%3B%20filename%3D%22Tactrix_LeafBMSflash.zip%22&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=I3UPHPWOPY63ZMOGLZFM%2F20250817%2Fnyc3%2Fs3%2Faws4_request&X-Amz-Date=20250817T095053Z&X-Amz-SignedHeaders=host&X-Amz-Expires=604800&X-Amz-Signature=4149f9ee2d66173c490a05b75c06dca162b7042d7d85ee24705a334f03d4c830

Fastest flashing with  Tactrix Openport2.0 (Chinese $20 clone)

To run app in windows need msvc runtime pack if you get msvc


### Flashing program with UCAN

https://cdn.imagearchive.com/mynissanleaf/internal_data/attachments/5/5681-3b1e75d8b08987fcb50012acb7c5e2ec.data?response-expires=Sun%2C%2017%20Aug%202025%2010%3A55%3A38%20GMT&response-content-disposition=attachment%3B%20filename%3D%22BMS%20tools.zip%22&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=I3UPHPWOPY63ZMOGLZFM%2F20250810%2Fnyc3%2Fs3%2Faws4_request&X-Amz-Date=20250810T105538Z&X-Amz-SignedHeaders=host&X-Amz-Expires=604800&X-Amz-Signature=d39f9dea08d57c1245cfa5e28bb8a6c8c26cd38ce430b912367b0fbe287029ee

Above PC app requires a USB-Can adaptor running SLCAN firmware
or use use a UCAN from fysetc - They're ~$4 from aliexpress.

### Yaste KIT firmware

ask to safetyuggs as in https://mynissanleaf.com/threads/reverse-engineering-bms-firmware-reflashing-bms.36627/post-657255

useful for 3NA0* BMS and CATL modules as in Yaste kit

### Bootloader recognised PIDs

IV: C3 48
Seed: 7001DF8C
Integer value (decimal): 1879170956
iv: c3 48
Seed: 70 01 df 8c
Key: 54 82 03 22
Key Accepted!
Starting CAN PID scan...

Scanning 1 byte messages...

PID Analysis:
Bytes: 01, PID: 21
Raw Response: 03 7F 21 12 FF FF FF FF
Response Length: 3 bytes
Error Response: Yes (0x7F)
Requested PID: 0x21
Error Code: 0x12
Error Description: Subfunction not supported

PID Analysis:
Bytes: 01, PID: 31
Raw Response: 03 7F 31 12 FF FF FF FF
Response Length: 3 bytes
Error Response: Yes (0x7F)
Requested PID: 0x31
Error Code: 0x12
Error Description: Subfunction not supported

PID Analysis:
Bytes: 01, PID: 34
Raw Response: 03 7F 34 12 FF FF FF FF
Response Length: 3 bytes
Error Response: Yes (0x7F)
Requested PID: 0x34
Error Code: 0x12
Error Description: Subfunction not supported

PID Analysis:
Bytes: 01, PID: 3E
Raw Response: 03 7F 3E 12 FF FF FF FF
Response Length: 3 bytes
Error Response: Yes (0x7F)
Requested PID: 0x3E
Error Code: 0x12
Error Description: Subfunction not supported

### Lookup tables

source: https://mynissanleaf.com/threads/reverse-engineering-bms-firmware-reflashing-bms.36627/post-656788

For 3NA0D that you're working on, have a look at 0x0003EF10 for the 6x20 table values.
X axis at 0x0003EEE0
Y axis at 0x0003EEE8

#### Ah calculation and table

As for Ah, if you're looking for the Present Capacity in Ah then that isn't transmitted on CAN. There is a Ah related variable in CAN ID 0x5C0, but that's like a counter of Ah charged/discharged. To get the Present Capacity in Ah then you need to request that from LID 0x01, The response includes a bunch of interesting data, but most of that data is also already available in transmitted CAN messages. Excluding the first two bytes in the response which are flow control (0x61, 0x01), the Present Capacity in Ah should be at data bytes 32-35 (dword). Scaling is 10000 units = 1Ah.

#### Dumped lookup table images 

* SOC/V (for 24kWh)
from: https://mynissanleaf.com/threads/reverse-engineering-bms-firmware-reflashing-bms.36627/post-656897
image from:
https://cdn.imagearchive.com/mynissanleaf/internal_data/attachments/6/6144-232d16dbccc7bbe4f76e7a0101a79f07.data?response-expires=Sun%2C%2017%20Aug%202025%2011%3A09%3A49%20GMT&response-content-type=image%2Fpng&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=I3UPHPWOPY63ZMOGLZFM%2F20250810%2Fnyc3%2Fs3%2Faws4_request&X-Amz-Date=20250810T110949Z&X-Amz-SignedHeaders=host&X-Amz-Expires=604800&X-Amz-Signature=9624d382a65c3d9b7355533dcdfbb2dbcb5dd9bf5e3c4cf0036de8d6cddc3155

* V/Discharge Current (for 24kWh)
from: https://mynissanleaf.com/threads/reverse-engineering-bms-firmware-reflashing-bms.36627/post-657064
image from:
https://cdn.imagearchive.com/mynissanleaf/internal_data/attachments/6/6170-0464a4137684dbfdcbc589bef1c5c03e.data?response-expires=Sun%2C%2024%20Aug%202025%2009%3A50%3A53%20GMT&response-content-type=image%2Fpng&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=I3UPHPWOPY63ZMOGLZFM%2F20250817%2Fnyc3%2Fs3%2Faws4_request&X-Amz-Date=20250817T095053Z&X-Amz-SignedHeaders=host&X-Amz-Expires=604800&X-Amz-Signature=fd304ca28fbd488cbb213fa7b42978ad5db6c33b5f178c478b58cc30131d93d0

* Current/Temp/SOC
from: https://mynissanleaf.com/threads/reverse-engineering-bms-firmware-reflashing-bms.36627/post-658267
Image from:
https://cdn.imagearchive.com/mynissanleaf/internal_data/attachments/6/6470-58f43fdf23ba1e4d83fe78e55f589c69.data?response-expires=Thu%2C%2021%20Aug%202025%2014%3A44%3A16%20GMT&response-content-type=image%2Fjpeg&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=I3UPHPWOPY63ZMOGLZFM%2F20250814%2Fnyc3%2Fs3%2Faws4_request&X-Amz-Date=20250814T144416Z&X-Amz-SignedHeaders=host&X-Amz-Expires=604800&X-Amz-Signature=e8c481b19dafe885a287137f4bb96a10c5a820c9d9ddcdf0a4e8c05781930b3e

## Troubleshooting

### BMS flash fail case

The BMS will automatically enter a 'recovery mode' if the flash write fails

### Bricking cases

* ok) Failure mid-flash will not brick your BMS.
* ok) Flashing genuine firmware files will not brick your BMS.
* ok) Flashing corrupted firmware files will not brick you BMS.
* NO) Flashing the WRONG firmware file to your BMS will very likely brick your BMS (But untested).

### Patched firmware safe conditions

As long as you don't wreck the main loop code in the firmware, it should be more or less 'brick proof'. There's a WDT running so if your code crashes, or causes a protection fault it'll restart. As long as it doesn't execute the bad code right away, it'll be recoverable by reflashing.

## Not classified notes

### Function to dump not modified firmware

PID21_82 is a great function to modify if you wanted to extract data from your BMS - and could be done on any BMS version as long as you have a compatible firmware (you can buy them from nissan-tech)


