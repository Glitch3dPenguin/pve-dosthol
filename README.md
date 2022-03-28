# pve-dosthol-klabsdev
WakeOnLAN (WOL) and more for ProxmoxVE without Python

This fork is designed to **ONLY** work on the Kulik Labs Devolopment servers and is inteded to work alongside Apache Guacamole. The intent is to send a WOL packet via the Guacamole server to the proxmox server. When the Proxmox server finds the WOL packet, it will translate the packet in order to start VM/LXC. After the Guacamole connection to the server has ended pam will execute a shutdown comand to close the server. 

This allows for users to log in to Guacamole and start/stop game servers without the need for me to do anything. Gotta love automation. 

See: https://forum.proxmox.com/threads/update-wake-and-other-on-lan-for-vms-v0-3.26381/

### Deploy dosthold to Proxmox 
1. Install daemon dependencies: `apt install gawk socat xxd`
2. Copy `dosthold.sh` to `/usr/local/bin`
3. Run `chmod a+x dosthold.sh` to allow the system to run the script
4. Copy `dosthol.service` to `/etc/systemd/system`
5. Start and enable the new service: `systemctl enable|start dosthol.service`

### Set up VM/LXC 
Set up Container/VM to shutdown when the Guacamole connection is closed:
1. Edit /etc/pam.d to include this line
    
        #Added by max
        session     optional    pam_exec.so quiet /etc/pam_session.sh
2. Now create the script that will run when the connection is closed:
`nano /etc/pam_session.sh`
    
        #!/bin/sh
        if [ "$PAM_TYPE" = "close_session" ]; then
          shutdown -h 0 SHUTDOWN
        fi

`chmod a+x pam_session.sh`

3.  Now make sure to set up Apache Guacamole properly this is an example based off the KLabsDev servers:

![](https://share.klabsdev.com/files//mbtKmDa.png)


If you are trying to set up Apache Guacamole to work with WoL in this way and have questions feel free to ask me questions on Discord Glitch3dPenguin#8857
