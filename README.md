

## NTP Configure on Linux:

In RHEL Linux 8, the **`ntp` package is no longer supported** and it is implemented by the `chronyd` (a daemon that runs in user-space) which is provided in the `chrony` package. 

`chrony` works both as an **NTP server** and as an **NTP client**, which is used to synchronize the system clock with NTP servers. 



Note that **by default NTP clients act as servers** for those systems in the stratum below them. Here is a summary of the NTP `Strata`:  

- `Stratum 0`: Atomic Clocks and their signals broadcast over Radio and GPS  

	- GPS (Global Positioning System)
	- Mobile Phone Systems
	- Low Frequency Radio Broadcasts WWVB (Colorado, USA.), JJY-40 and JJY-60 (Japan), DCF77 (Germany), and MSF (United Kingdom)  

These signals can be received by dedicated devices and are usually connected by RS-232 to a system used as an organizational or site-wide time server.

- `Stratum 1`: Computer with radio clock, GPS clock, or atomic clock attached
- `Stratum 2`: Reads from stratum 1; Serves to lower strata
- `Stratum 3`: Reads from stratum 2; Serves to lower strata
- `Stratum n+1`: Reads from stratum n; Serves to lower strata
- `Stratum 15`: Reads from stratum 14; This is the lowest stratum.  
  
This process continues down to Stratum 15 which is the lowest valid stratum. The label Stratum 16 is used to indicated an unsynchronized state.




#### Environment:
- NTP Server - RHEL 8:  192.168.10.190
- NTP Client - rocky 8:  192.168.10.191 



### Configure NTP Server:

```
yum install chrony
```


```
rpm -qa | grep chrony

chrony-4.5-1.0.1.el8.x86_64
```



```
timedatectl set-time 2024-08-22
timedatectl set-time 12:26:00
```


```
timedatectl list-timezones | grep Dhaka
```


```
timedatectl set-timezone Asia/Dhaka
```



_On NTP server configure the `/etc/chrony.conf` file:_

```
vim /etc/chrony.conf


#pool 2.pool.ntp.org iburst

#server time1.google.com iburst
#server time2.google.com iburst
#server time3.google.com iburst
#server time4.google.com iburst

server 192.168.10.190

# Allow NTP client access from local network.
allow 192.168.10.0/24
```


```
systemctl restart chronyd
systemctl enable chronyd
systemctl status chronyd
```




On the server, run the following command to display NTP Servers information: 

```
chronyc sources


MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^? orcl1                         0   6     0     -     +0ns[   +0ns] +/-    0ns
```



On the server, run the following command to display information about NTP clients assessing the NTP server.

```
chronyc clients


Hostname                      NTP   Drop Int IntL Last     Cmd   Drop Int  Last
===============================================================================
orcl1                           8      0   6   -    27       0      0   -     -
192.168.10.191                 11      0   6   -    21       0      0   -     -
```





### Configure NTP Client:


```
yum install chrony
```


```
rpm -qa | grep chrony

chrony-4.5-1.el8.x86_64
```


Comment out the **default NTP servers specified** as the value of the `server` or `pool` directive, and set your RHEL-8 server’s IP address instead.

_On NTP Client configure the `/etc/chrony.conf` file:_
```
vim /etc/chrony.conf


#pool 2.rocky.pool.ntp.org iburst

#server your_ntp_server_ip iburst
server 192.168.10.190 iburst
```


```
systemctl restart chronyd
systemctl enable chronyd
systemctl status chronyd
```





```
timedatectl


               Local time: Thu 2024-08-22 12:26:10 +06
           Universal time: Thu 2024-08-22 06:26:10 UTC
                 RTC time: Wed 2024-08-21 18:26:09
                Time zone: Asia/Dhaka (+06, +0600)
System clock synchronized: no
              NTP service: inactive
          RTC in local TZ: no
```


```
timedatectl set-ntp on
```


```
timedatectl


               Local time: Thu 2024-08-22 12:28:12 +06
           Universal time: Thu 2024-08-22 06:28:12 UTC
                 RTC time: Thu 2024-08-22 06:28:12
                Time zone: Asia/Dhaka (+06, +0600)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```



Now, run the following command to show the current time sources (NTP server) that chronyd is accessing, which should be your NTP server address.

```
chronyc sources


MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^? 192.168.10.190                0   7     0     -     +0ns[   +0ns] +/-    0ns
```



```
chronyc tracking
Reference ID    : C0A80ABE (192.168.10.190)
Stratum         : 3
Ref time (UTC)  : Thu Aug 22 16:18:31 2024
System time     : 0.000000000 seconds fast of NTP time
Last offset     : -123.245330811 seconds
RMS offset      : 123.245330811 seconds
Frequency       : 8.733 ppm fast
Residual freq   : -11.969 ppm
Skew            : 2.185 ppm
Root delay      : 0.115271755 seconds
Root dispersion : 0.003325702 seconds
Update interval : 0.0 seconds
Leap status     : Normal
```



With these steps, your Linux 8 system should be configured as an NTP server, ready to serve time to clients on your network.



### Links:
- [NTP client or server in Linux](https://www.redhat.com/sysadmin/chrony-time-services-linux)
- [NTP in RHEL 8](https://www.tecmint.com/install-ntp-in-rhel-8/)
