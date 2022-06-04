# Midigate-DDoS-DoS-TCP-Sockets-Handshakes
Maybe one day you will be affected by a TCP socket/handshake flood that doesn't let you connect to application anymore but that is still active, the flood doesn't generate much traffic so it is hard to filter by DDo-S protection, here I show you how to take care of that, the man is himself

Note: if you have questions about any entry which is made here just use google

Step 1 (netfilter changes)
----
    #modprobe ip_conntrack
    #sysctl -q -w net.ipv4.tcp_synack_retries=1
    #sysctl -q -w net.ipv4.tcp_syncookies=1
    #sysctl -q -w net.ipv4.tcp_timestamps=1
    #sysctl -q -w net.netfilter.nf_conntrack_max=10000000
    #sysctl -q -w net.netfilter.nf_conntrack_tcp_loose=0

Step 2 (modify user files & max user processes)
----
    #ulimit -n 999999  <- open files - highest which you can set
    #ulimit -u 999999  <- highest which you can set
    #ulimit unlimited

In most cases you application should not reject/drop any incomming connections anymore which is caused by the flood. but this goes to the CPU maybe it raises the capacity up and you server lags a bit or uses more cpu than usually, if thats the case here some extra steps # USE WITH OWN CAUTION - GOOGLE THEIRE USSAGE #

Step 3 (Iptables - TCP-Options )
----
    #ulimit -n 999999  <- open files - highest which you can set
    #ulimit -u 999999  <- highest which you can set
    #ulimit unlimited
