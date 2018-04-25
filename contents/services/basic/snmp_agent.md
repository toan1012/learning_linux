Cài đặt snmp agent and server

https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-an-snmp-daemon-and-client-on-ubuntu-14-04
https://www.liquidweb.com/kb/how-to-install-and-configure-snmp-on-centos/
https://www.linuxhelp.com/how-to-install-and-configure-snmp-in-centos/

The SNMP service is available in the standard CentOS repository.
1. Install net-snmp with yum:

yum install net-snmp

 2. Create a simple SNMP configuration:

mv /etc/snmp/snmpd.conf /etc/snmp/snmpd.conf.org
vim /etc/snmp/snmpd.conf

Add the following to /etc/snmp/snmpd.conf ; Replace 1.2.3.4 with the IP address of the server that you want to allow SNMP lookups from:

rocommunity public 1.2.3.4

 3. Start the SNMP service, and set it to auto-start on reboot:

/etc/init.d/snmpd start
chkconfig snmpd on

If you have a firewall configured, ensure that you have UDP port 161 open to your SNMP lookup server.

 

On your SNMP lookup server, you can do the following to perform a quick SNMP test to ensure that it’s working. Replace 1.2.3.3 with the server’s address that you just configured SNMP on:

snmpwalk -v 2c -c public 1.2.3.3



---------------------------------------------
Lý thuyết

snmp
https://www.manageengine.com/network-monitoring/what-is-snmp.html#what-is-snmp

snmp communition

https://www.manageengine.com/network-monitoring/what-is-snmp.html#what-is-snmp


