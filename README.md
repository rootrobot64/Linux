Meta -secure Linux 


Automatically install Linux update 

 - sudo apt update && apt upgrade -y 

 -sudo apt install unattended-upgrade -y 

Don't use root , use sudo , if its has  many user create one 

- adduser Brown 
-usermod -aG sudo Brown 

*command to list the user allow with sudo

-getent group sudo 

3. enforce password complexity 


  -passwd "usrname"
*Test logging in as the new user before doing anything else. Optionally lock the root password afterwards:
 -sudo passwd -l root

*check if any user has an empty password 

  -awk -F: '($2 == "") {print $1}' /etc/shadow

4. install pluggable Authentication modules 

  -sudo apt-get install libpam-pwquality
change the minumun to 16 char

 - sudo  nano /etc/security/pwquality.conf


5.command to remove user 

   -sudo userdel -rf "user"

6.use ssh key not a password 

   -ssh-keygen -t ed25519
*copy the key into machine 

  -ssh-copy-id -i ~/.ssh/id_ed25519.pub "user@ip"

7. Disable password base Authentication for ssh 

  - sudo nano /etc/ssh/sshd_config

   *PasswordAuthentication no
   *PermitRootLogin no
   *PermitEmptyPasswords no
   *PubkeyAuthentication yes
   *MaxAuthTries 3
   *AddressFamily inet

*Restart SSh 
 -sudo sshd -t 
 -sudo systemctl restart ssh 

*disable ipv6 
  -sudo nano /etc/ssh/sshd_config 

   AddressFamily inet
#disable Ipv6 on the system 
-sudo nano /etc/sysctl.conf
 add this line :
# disable IPv6 on the system
net.ipv6.conf.all.disable_ipv6=1
net.ipv6.conf.default.disable_ipv6=1 

-sudo sshd -t

* disable the anonymous 
  -sudo nano /etc/vsftpd.conf 
    anonymous_enable=NO
    local_enable=YES
    write_enable=YES
    local_umask=022
    chroot_local_user=YES
    allow_writeable_chroot=YES

# only listed users may log in
   userlist_enable=YES
   userlist_deny=NO
   userlist_file=/etc/vsftpd.userlist

# encryption
    ssl_enable=YES
    force_local_logins_ssl=YES
    force_local_data_ssl=YES
   ssl_tlsv1=NO
   ssl_tlsv1_1=NO
   ssl_tlsv1_2=YES
   require_ssl_reuse=NO
 
8. samba (windows file sharing) guest access 
   
   -sudo nano /etc/samba/smb.conf 


  
 9. Set the defaults a firewall
  
  -sudo ufw default deny incoming
  -sudo ufw default allow outgoing

  *Allow only what you need , Do this before enabling the firewall, or you'll lock yourself out of SSH.
 - sudo ufw limit 444/tcp
 -sudo ufw  allow 80/tcp         
 -sudo ufw  allow 443/tcp  

   * Open as few ports as possible. Check with
 -sudo ss -tulpn

  
   *Add fail2ban,It automatically bans IPs that keep failing logins:


   -sudo apt install fail2ban
   -sudo systemctl enable --now fail2ban
   -sudo nano /etc/fail2ban/jail.local

   add : [sshd]
         enabled = true
         maxretry = 5
         bantime = 1h
   -sudo systemctl enable --now fail2ban
   -sudo systemctl restart fail2ban
   -sudo fail2ban-client status sshd


   *Enable ufw 
   
   -sudo ufw enable 
   -sudo ufw status verbose          








