# Midigate-DDoS-DoS-TCP-Sockets-Handshakes
Maybe one day you will be affected by a TCP socket/handshake flood that doesn't let you connect to application anymore but that is still active, the flood doesn't generate much traffic so it is hard to filter by DDo-S protection, here I show you how to take care of that, the man is himself


Step 1
----
    #sysctl -q -w net.ipv4.tcp_synack_retries=1
    #sysctl -q -w net.ipv4.tcp_syncookies=1
    #sysctl -q -w net.ipv4.tcp_timestamps=1
    #sysctl -q -w net.netfilter.nf_conntrack_max=10000000
    #sysctl -q -w net.netfilter.nf_conntrack_tcp_loose=0
