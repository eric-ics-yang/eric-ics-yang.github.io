###Can not ssh to a server
Problem: ssh xxx hangs, but ping xxx success and other server can ssh to xxx.

    ssh -v xxx
    debug1: Reading configuration data /etc/ssh/ssh_config
    debug1: /etc/ssh/ssh_config line 19: Applying options for *
    debug1: Connecting to xxx [xxx] port 22.
    debug1: Connection established.
    debug1: permanently_set_uid: 0/0
    debug1: identity file /root/.ssh/id_rsa type 1
    debug1: identity file /root/.ssh/id_rsa-cert type -1
    debug1: identity file /root/.ssh/id_dsa type -1
    debug1: identity file /root/.ssh/id_dsa-cert type -1
    debug1: identity file /root/.ssh/id_ecdsa type -1
    debug1: identity file /root/.ssh/id_ecdsa-cert type -1
    debug1: identity file /root/.ssh/id_ed25519 type -1
    debug1: identity file /root/.ssh/id_ed25519-cert type -1
    debug1: Enabling compatibility mode for protocol 2.0
    debug1: Local version string SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2
    debug1: Remote protocol version 2.0, remote software version dropbear_2012.55
    debug1: no match: dropbear_2012.55
    debug1: SSH2_MSG_KEXINIT sent
    
Solution:
- solution 1

update mtu through ifconfig

    ifconfig eth0 mtu 1700
    1700 depends you network's mtu and server configuration
    
- solution 2

update mtu through sysfs

    cat /sys/class/net/eth0/mtu
    echo "1500" > /sys/class/net/eth0/mtu
    1500 depends you network's mtu and server configuration
