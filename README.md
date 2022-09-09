# TP-Link-Archer-AX10-V1
A proof of concept for TP-LINK router Archer AX10 v1

## Intro
The device I conducted this research on was the Archer AX10 v1 home WiFi router from TP-Link (Firmware Version 1.3.1 Build 20220401 Rel. 57450(5553)).

<img src="https://raw.githubusercontent.com/gscamelo/TP-Link-Archer-AX10-V1/main/img/1_normal_1565685514126y.jpeg" width=50% height=50%>

My first approach was to get access via the UART Interface on the board.

You can see about "How to Detect Serial Pinout (GND, VCC, TX, RX)" in this link: 
https://youtu.be/zjafMP7EgEA?t=110

RouterConverter
TX	RX
RX	TX
GND	GND

Thanks, Flashback Team you guys are the inspiration for this PoC.

I was able to access the board using the USB Uart Cp2102 Interface. 
For this I used the following UART PINOUT:

<img src="https://raw.githubusercontent.com/gscamelo/TP-Link-Archer-AX10-V1/main/img/02.jpg" width=50% height=50%>
<img src="https://raw.githubusercontent.com/gscamelo/TP-Link-Archer-AX10-V1/main/img/05.jpg" width=50% height=50%>

After a successful connection, the next step was to detect the communication baud-rate, playing around with the baud-rates I found that the correct one was: 115200 using minicom.

Before connecting find out a port number of your ttl-usb converter, usually it is /dev/ttyUSB0.
Start minicom and set up port connection.

`minicom -s`

Select Serial port setup. Then press A, input /dev/ttyUSB0. Press E set 115200 bps 8N1. After all save it Save setup as dfl.

Useful information was printed to UART Interface when the board is booting, but the most important was the Chip ID: ARM Cortex A7

<img src="https://raw.githubusercontent.com/gscamelo/TP-Link-Archer-AX10-V1/main/img/03.png" width=50% height=50%>

After Device Boot I expected an unprotected system shell, but the device asked me for username and password:

So I was forced to change the research approach. I downloaded the original device firmware from the: https://static.tp-link.com/upload/firmware/2022/202205/20220511/Archer%20AX10(US)_V1_220401.zip

And with the help of Binwalk tool, I was able to extract the Router filesystem.

$ binwalk -e ax10v1-up-ver1-2-4-P1\[20211014-rel54860\]_2021-10-14_19.19.05.bin 
<img src="https://raw.githubusercontent.com/gscamelo/TP-Link-Archer-AX10-V1/main/img/04.png" width=50% height=50%>

After some research, I realized that the same approach reported in CVE-2022-30075 works like a charm.
It just took some adjusts to the xml file to make it work and get shell.
All credits to Tomas Melicher aka aaronsvk.

<img src="https://raw.githubusercontent.com/gscamelo/TP-Link-Archer-AX10-V1/main/img/06.png" width=50% height=50%>

## Timeline

+ September 08, 2022 - Vulnerability identified
+ September 09, 2022 - Vulnerability reported


## LINKS
+ https://www.tp-link.com/br/home-networking/wifi-router/archer-ax10/
+ https://www.tp-link.com/br/support/download/archer-ax10/v1/
+ https://github.com/aaronsvk/CVE-2022-30075
+ https://teknoraver.net/software/hacks/tplink/
+ https://medium.com/@LargeCardinal/decrypting-config-bin-files-for-tp-link-wr841n-wa855re-and-probably-more-676de396d724
+ https://gist.github.com/shreve/7a6413f087c15b233a69bb46edcfec17
+ https://github.com/ret5et/tplink_backup_decrypt_2022.bin
