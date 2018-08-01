# sudo sh block.sh
iptables -I INPUT 1 -p tcp --dport 80 -m string --string "ads.yahoo.com" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "ads.fcmrktplace.com" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "ads.creafi-online-media.com" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "ib.reachjunction.com" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "www.indeed.com" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "ib.adnxs.com" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "ad.tagjunction.com" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "ad.globe7.com" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "ads.clicksor.com" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "ad.bharatstudent.com" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "webnewline.com" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "ad.smxchange.com" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "lm.logicalmedia.com" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "ads.lzjl.com" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "media.fastclick.net" --algo kmp -j DROP

iptables -I INPUT 1 -p tcp --dport 80 -m string --string "cdn.fastclick.net" --algo kmp -j DROP




###How to mitigate attacks with Iptables

# sync
iptables -A INPUT -i eth1 -p tcp ! --syn -m state --state NEW  -m limit --limit 5/m --limit-burst 7 -j LOG --log-level 4 --log-prefix "Drop Syn"

iptables -A INPUT -i eth1 -p tcp ! --syn -m state --state NEW -j DROP
 
# Fragments
iptables -A INPUT -i eth1 -f  -m limit --limit 5/m --limit-burst 7 -j LOG --log-level 4 --log-prefix "Fragments Packets"

iptables -A INPUT -i eth1 -f -j DROP
 
 
# block bad stuff
iptables  -A INPUT -i eth1 -p tcp --tcp-flags ALL FIN,URG,PSH -j DROP

iptables  -A INPUT -i eth1 -p tcp --tcp-flags ALL ALL -j DROP
 
iptables  -A INPUT -i eth1 -p tcp --tcp-flags ALL NONE -m limit --limit 5/m --limit-burst 7 -j LOG --log-level 4 --log-prefix "NULL Packets"

iptables  -A INPUT -i eth1 -p tcp --tcp-flags ALL NONE -j DROP # NULL packets
 
iptables  -A INPUT -i eth1 -p tcp --tcp-flags SYN,RST SYN,RST -j DROP
 
iptables  -A INPUT -i eth1 -p tcp --tcp-flags SYN,FIN SYN,FIN -m limit --limit 5/m --limit-burst 7 -j LOG --log-level 4 --log-prefix "XMAS Packets"

iptables  -A INPUT -i eth1 -p tcp --tcp-flags SYN,FIN SYN,FIN -j DROP #XMAS
 
iptables  -A INPUT -i eth1 -p tcp --tcp-flags FIN,ACK FIN -m limit --limit 5/m --limit-burst 7 -j LOG --log-level 4 --log-prefix "Fin Packets Scan"

iptables  -A INPUT -i eth1 -p tcp --tcp-flags FIN,ACK FIN -j DROP # FIN packet scans
 
iptables  -A INPUT -i eth1 -p tcp --tcp-flags ALL SYN,RST,ACK,FIN,URG -j DROP
 
 
# Allow full outgoing connection but no incomming stuff
iptables -A INPUT -i eth1 -m state --state ESTABLISHED,RELATED -j ACCEPT

iptables -A OUTPUT -o eth1 -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT


# allow ssh only
iptables -A INPUT -p tcp --destination-port 22 -j ACCEPT

iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT
 
# allow incoming ICMP ping pong stuff
iptables -A INPUT -p icmp --icmp-type 8 -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT

iptables -A OUTPUT -p icmp --icmp-type 0 -m state --state ESTABLISHED,RELATED -j ACCEPT
 
# No smb/windows sharing packets - too much logging
iptables -A INPUT -p tcp -i eth1 --dport 137:139 -j REJECT

iptables -A INPUT -p udp -i eth1 --dport 137:139 -j REJECT

### 1: Drop invalid packets ### 
/sbin/iptables -t mangle -A PREROUTING -m conntrack --ctstate INVALID -j DROP  

### 2: Drop TCP packets that are new and are not SYN ### 
/sbin/iptables -t mangle -A PREROUTING -p tcp ! --syn -m conntrack --ctstate NEW -j DROP 
 
### 3: Drop SYN packets with suspicious MSS value ### 
/sbin/iptables -t mangle -A PREROUTING -p tcp -m conntrack --ctstate NEW -m tcpmss ! --mss 536:65535 -j DROP  

### 4: Block packets with bogus TCP flags ### 
/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags FIN,SYN,RST,PSH,ACK,URG NONE -j DROP 

/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags FIN,SYN FIN,SYN -j DROP 

/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags SYN,RST SYN,RST -j DROP 

/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags FIN,RST FIN,RST -j DROP 

/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags FIN,ACK FIN -j DROP 

/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags ACK,URG URG -j DROP 

/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags ACK,FIN FIN -j DROP 

/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags ACK,PSH PSH -j DROP 

/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags ALL ALL -j DROP 

/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags ALL NONE -j DROP 

/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags ALL FIN,PSH,URG -j DROP 

/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags ALL SYN,FIN,PSH,URG -j DROP 

/sbin/iptables -t mangle -A PREROUTING -p tcp --tcp-flags ALL SYN,RST,ACK,FIN,URG -j DROP

### SSH brute-force protection ### 
/sbin/iptables -A INPUT -p tcp --dport ssh -m conntrack --ctstate NEW -m recent --set 

/sbin/iptables -A INPUT -p tcp --dport ssh -m conntrack --ctstate NEW -m recent --update --seconds 60 --hitcount 10 -j DROP  

### Protection against port scanning ### 
/sbin/iptables -N port-scanning 

/sbin/iptables -A port-scanning -p tcp --tcp-flags SYN,ACK,FIN,RST RST -m limit --limit 1/s --limit-burst 2 -j RETURN 

/sbin/iptables -A port-scanning -j DROP

https://cariagiovannib.wordpress.com/2017/08/24/how-to-mitigate-attacks-with-iptables-fail2ban-and-ipset-and-monitor-with-ss/

### allow only ip access https##
-A INPUT -s ip-address -p tcp -m tcp --dport 443 -j ACCEPT

-A INPUT -p tcp -m tcp --dport 443 -j DROP

### allow smtp ##
-A INPUT -p tcp -m tcp --dport 25 -j ACCEPT

-A INPUT -p tcp -m tcp --dport 587 -j ACCEPT

-A INPUT -p tcp -m tcp --dport 465 -j ACCEPT
 
