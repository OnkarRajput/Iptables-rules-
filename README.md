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
