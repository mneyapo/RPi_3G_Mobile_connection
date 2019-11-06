https://www.thefanclub.co.za/how-to/how-setup-usb-3g-modem-raspberry-pi-using-usbmodeswitch-and-wvdial

sudo apt-get update
sudo apt-get install ppp usb-modeswitch wvdial

/etc/udev/rules.d/ - these do not works correctly
/lib/udev/rules.d - use these!

# Huawei E173
ATTRS{idVendor}=="12d1", ATTRS{idProduct}=="1446", RUN+="usb_modeswitch '%b/%k'"

# Huawei, newer modems

TargetVendor=  0x12d1
TargetProductList="1001,1406,140b,140c,1412,141b,1432,1433,1436,14ac,1506,1511"

MessageContent="55534243123456780000000000000011062000000100000000000000000000"

PPP
For dialing I use ppp with these files:

/etc/ppp/peers/huawei

/dev/serial/by-id/usb-HUAWEI_Technology_HUAWEI_Mobile-if00-port0
115200

nodetach
connect '/usr/sbin/chat -v -f /etc/ppp/peers/huawei-on'
disconnect '/usr/sbin/chat -v -f /etc/ppp/peers/huawei-off'
noauth
usepeerdns
local
defaultroute
replacedefaultroute
noipdefault
/etc/ppp/peers/huawei-on

ABORT BUSY ABORT 'NO CARRIER' ABORT ERROR
'' AT
AT ""
OK ATZ
OK AT+CGDCONT=1,"IP","internet"
OK "AT Q0 V1 E1 S0=0 &C1 &D2 +FCLASS=0"
OK ATX3
OK "ATDT*99#"
CONNECT \d\c
/etc/ppp/peers/huawei-off

"" "\K"
"" "+++ath"
/etc/ppp/peers/provider

# example configuration for a dialup connection authenticated with PAP or CHAP
#
# This is the default configuration used by pon(1) and poff(1).
# See the manual page pppd(8) for information on all the options.

# MUST CHANGE: replace myusername@realm with the PPP login name given to
# your by your provider.
# There should be a matching entry with the password in /etc/ppp/pap-secrets
# and/or /etc/ppp/chap-secrets.
user "myusername@realm"

# MUST CHANGE: replace ******** with the phone number of your provider.
# The /etc/chatscripts/pap chat script may be modified to change the
# modem initialization string.
connect "/usr/sbin/chat -v -f /etc/chatscripts/pap -T ********"

# Serial device to which the modem is connected.
/dev/modem

# Speed of the serial line.
115200

# Assumes that your IP address is allocated dynamically by the ISP.
noipdefault
# Try to get the name server addresses from the ISP.
usepeerdns
# Use this connection as the default route.
defaultroute

# Makes pppd "dial again" when the connection is lost.
persist

# Do not ask the remote to authenticate.
noauth
Controlling connection
Connect pppd call huawei &

Disconnect pkill pppd
