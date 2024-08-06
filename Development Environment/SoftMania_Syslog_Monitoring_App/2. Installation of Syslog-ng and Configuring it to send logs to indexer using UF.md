# Setting up Syslog-ng Server-Client architecure in Redhat Linux

## 1. Install Syslog-ng on Linux (RHEL-9)
In the security group of the AWS instance, provide access to Custom UDP and give port number as 514.
Login as root user
```bash
sudo su
```

```bash
subscription-manager repos --enable codeready-builder-for-rhel-9-noarch-rpms
dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
```
Navigate to the below mentioned directory

```bash
cd /etc/yum.repos.d/
```

Install wget (Package installer)

```bash
yum install wget
```


```bash
wget https://copr.fedorainfracloud.org/coprs/czanik/syslog-ng336/repo/epel-8/czanik-syslog-ng41-epel-8.repo
```
```bash
yum install syslog-ng --nobest
```

Enable and start Syslog

```bash
systemctl enable syslog-ng
systemctl start syslog-ng
```
Check the status of the syslog using the below command

```bash
systemctl status syslog-ng
```
Exit
```bash
exit
```

Repeat the above steps to install Syslog for 3 more clients and 1 server

## 1.1 Configure Syslog-ng Client to send logs to Syslog-ng Server

Login as root user using below command

```bash
sudo su
```
 

Enable syslog 

```bash
systemctl enable syslog-ng
```


Go to the directory mentioned below

```bash
cd /etc/syslog-ng
```

List the files to check if syslog-ng.conf is available

```bash
ls
```

Edit the syslog-ng.conf file

```bash
vi syslog-ng.conf
```
Add the below listed lines to save the logs from client in a different file. This is configuration for **Syslog-ng Client**. 
```bash

source s_network{
        udp();
};
destination d_from_net{
file("/var/log/from_net");};


log{
source(s_network);
destination(d_from_net);
};
```


Restart syslog-ng service

```bash
systemctl restart syslog-ng
```
Repeat the client configuration steps in other 3 Syslog-ng clients

## 2.Configure Syslog-ng Server with Universal forwarder to send logs to Indexer

### Configuring Syslog-Server to send the incoming logs to a separate folder
Login as root user using below command

```bash
sudo su
```
 

Enable syslog 

```bash
systemctl enable syslog-ng
```


Go to the directory mentioned below

```bash
cd /etc/syslog-ng
```

List the files to check if syslog-ng.conf is available

```bash
ls
```

Edit the syslog-ng.conf file

```bash
vi syslog-ng.conf
```
Add the below listed lines to forward the data to server. This is configuration for **Syslog-ng Server**
```bash

destination d_network{
        udp("<ip address of Syslog-Server>" port(514));
};

log{
source(s_sys);
destination(d_network);
};
```


Restart syslog-ng service

```bash
systemctl restart syslog-ng
```



**2.1 Install Universal Forwarder in Syslog server**

Splunk Universal Forwarder Installation

```bash
https://github.com/URahuman/Splunk_Universal_Forwarder
```

**2.2 Configure the Universal forwarder in Syslog-ng server to send data to an Intermediate Forwarder**

Login as root user using below command
```bash
sudo su
```

Provide permission for the Splunk to access syslog files
```bash
chown -R splunk:splunk /var/log/messages
```

Goto inputs.conf file and configure it to monitor the the log file in syslog
```bash
cd /opt/splunkforwarder/etc/apps/SplunkUniversalForwarder/local
```

Open inputs.conf
```bash
vi inputs.conf
```

Provide the below mentioned configuration in inputs.conf
```bash
[monitor://var/log/from_net]
index = <index name that you have created for syslog app>
```

Configure outputs.conf to send to Intermediate forwarder
```bash
cd /opt/splunkforwarder/etc/system/local/
```

Open outputs.conf
```bash
vi outputs.conf
```

Provide the below mentioned configuration in outputs.conf
```bash
[tcpout]
defaultGroup = default-autolb-group

[tcpout:default-autolb-group]
server = <IP of Dev-splunk>:9997, <IP of UAT-splunk>:9997, <IP of PROD-splunk>:9997

```


### Troubleshooting steps
**Check for Status**
```bash
systemctl status syslog-ng
```
**Try this command for details**
```
journalctl -xeu syslog-ng.service
```

**Try this command to view errors in syslog-ng.conf**
```bash
sudo syslog-ng -Fdev
```

Check for logs in both server and client by check here
```bash
vi /var/log/messages
```
# Receiving data from Syslog-ng server in Splunk
