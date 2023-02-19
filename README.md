# Midigate-DDoS-DoS-TCP-Sockets-Handshakes
Maybe one day you will be affected by a TCP socket/handshake flood that doesn't let you connect to application anymore but that is still active, the flood doesn't generate much traffic so it is hard to filter by DDo-S protection, here I show you how to take care of that, the man is himself

Note: if you have questions about any entry which is made here just use google

Step 1 (netfilter changes)
----
    modprobe ip_conntrack
    sysctl -q -w net.ipv4.tcp_synack_retries=1
    sysctl -q -w net.ipv4.tcp_syncookies=1
    sysctl -q -w net.ipv4.tcp_timestamps=1
    sysctl -q -w net.netfilter.nf_conntrack_max=10000000
    sysctl -q -w net.netfilter.nf_conntrack_tcp_loose=0

Step 2 (modify user files & max user processes)
----
    ulimit -n 999999  <- open files - try the highest which you can set
    ulimit -u 999999  <- user proc- try the highest which you can set
    ulimit unlimited

In most cases you application should not reject/drop any incomming connections anymore which is caused by the flood. but this goes to the CPU maybe it raises the capacity up and you server lags a bit or uses more cpu than usually, if thats the case here some extra steps # USE WITH OWN CAUTION - GOOGLE THEIRE USSAGE #

Step 3 (Iptables - TCP-Options )
----   
    explain:
    #--tcp-option 4 = SACK Permitted can affect in some cases legit traffic by returning the first connections after reconnect it connects instantly
    #--tcp-option 5 = SACK to work with option 4
    #--tcp-option 5 = Timestamps important for handshake
    USSAGE:
    iptables -A INPUT -p tcp -m tcp --tcp-option 8 -j REJECT
    iptables -A INPUT -p tcp -m tcp --tcp-option 5 -j REJECT
    iptables -A INPUT -p tcp -m tcp --tcp-option 4 -j RETURN

Step 4 Extra (Iptables - DROP Completly Socket floods )
----  
    apt install ipset -y
    ipset create blocked hash:ip timeout 180000
    iptables -A INPUT -p tcp -m tcp -m set --match-set blocked src -j DROP

![image](https://user-images.githubusercontent.com/106752367/172026335-0c8be783-50b9-485c-a74c-81605c66f0c0.png)

    #only use it when you dont have any http application running like web-applications/sites

    #Method (1): (block at every port all HTTP Request method 1.1/1.2.. GET/POST...
    iptables -I INPUT -p tcp --dport 1:65535 -m string --algo bm --string 'HTTP' -j SET --add-set blocked src

    #Method (2): (block at every port all HTTP Request method 1.0/1 GET/POST/PUT...
    iptables -I INPUT -p tcp --dport 1:65535 -m string --algo bm --string 'HTTP/1.1' -j SET --add-set blocked src
    iptables -I INPUT -p tcp --dport 1:65535 -m string --algo bm --string 'HTTP/1.2' -j SET --add-set blocked src

    #Method (3) customize it yourself for you needs :)
    iptables -I INPUT -p tcp --dport 1:65535 -m string --algo bm --string 'GET <- Request type / HTTP/1.1 <- HTTP Version' -j SET --add-set blocked src

Step 5 Extra (Iptables - Drop Attacks to specific amplifications/applications which are not ussed properly )
   
    If you are not sure just STRG+F and look for ips which got open
---- 

    iptables -A INPUT -p tcp -m multiport --dports 135,137,138,139,445,1433,1434 -j DROP
    iptables -A INPUT -p tcp -m multiport --sports 135,137,138,139,445,1433,1434 -j DROP
    iptables -A INPUT -p tcp -m multiport --dports 853,4433,4740,5349,5684,5868,6514,6636,8232,10161 -j DROP
    iptables -A INPUT -p tcp -m multiport --sports 853,4433,4740,5349,5684,5868,6514,6636,8232,10161 -j DROP
    iptables -A INPUT -p tcp -m multiport --dports 10162,12346,12446,12546,12646,12746,12846,12946,13046 -j DROP
    iptables -A INPUT -p tcp -m multiport --sports 10162,12346,12446,12546,12646,12746,12846,12946,13046 -j DROP
    iptables -A INPUT -p tcp -m multiport --dports 1645,1812,2049,3283,2302,6481,17185,26000,26001,26002 -j DROP
    iptables -A INPUT -p tcp -m multiport --sports 1645,1812,2049,3283,2302,6481,17185,26000,26001,26002 -j DROP
    iptables -A INPUT -p tcp -m multiport --dports 26003,26004,27960,27961,27962,27963,27964,30720,30721 -j DROP
    iptables -A INPUT -p tcp -m multiport --sports 26003,26004,27960,27961,27962,27963,27964,30720,30721 -j DROP
    iptables -A INPUT -p tcp -m multiport --dports 30722,30723,30724,44400,3784,64378,8767,11211,1645,1812,520 -j DROP
    iptables -A INPUT -p tcp -m multiport --sports 30722,30723,30724,44400,3784,64378,8767,11211,1645,1812,520 -j DROP
    iptables -A INPUT -p tcp -m multiport --sports 3478,5349 -j DROP
    iptables -A INPUT -p tcp -m multiport --sports 4568,42244,49658,43512 -j DROP
    iptables -A INPUT -p tcp -m multiport --sports 37368,46007,42264,36878 -j DROP
    iptables -A INPUT -p tcp -m multiport --dports 3478,5349 -j DROP
    iptables -A INPUT -p tcp -m multiport --dports 4568,42244,49658,43512 -j DROP
    iptables -A INPUT -p tcp -m multiport --dports 37368,46007,42264,36878 -j DROP

You have not enought?
 then check [Active-VM](https://active-vm.net/) out good servers with good protection & support
    Discord https://discord.gg/9E6uX9Wmu6 [ Sponsored Server to test rules on it ]
