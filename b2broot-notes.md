#create new group
sudo groupadd group_name

#check if group has been created / list users on group
getent group group_name

#check all local users
cut -d: -f1 /etc/passwd
#or
1- cd into /etc that is on ~/../../
2- nano passwd

#add new user
sudo adduser new_user

#assing user to group
sudo usermod -aG groupName userName

#see in what groups logged user is assigned to
groups

#check if passwd rules are working
chage -l userName

#sudo log
1-created a sudo.log file under /var/log from ~/../../
2-sudo nano /etc/sudoers from ~/../../
3-add the log file path under Defaults	logfile="/var/log/sudo/sudo.log"

#crontab to execute script in intervals
1-crontab seems to be on the net-tools installed using apt
2-bash script is undere /usr/local/bin/monitoring.sh
3-sudo visudo, where set under my username permission to run without passwd the script
3- sudo crontab -u root -e   opens the crontab config file
4- on the last line added the rule to execute the monitoring.sh every 10min

#ufw Uncomplicated firewall
	#check status
	sudo ufw status numbered

#SSH
 #check status
 sudo systemctl status ssh

 #configure port
 1-sudo vim /etc/ssh/sshd_config
 2-change line: Port "portNumber"
 3-sudo service ssh restart

 #check configured port
 sudo grep Port /etc/ssh/sshd_config

#Passwd Quality Checking Library 
 #installed from sudo apt-get install libpam-pwquality

 #configure rules
 sudo nano /etc/pam.d/common-password

 #configure ttl
 sudo nano /etc/login.defs

 #pwquality flags meanings


#Unit - run script on interval detached from system clock

1-the script done and setting the permissions
2-create a service and timer files on /etc/systemd/system

 #monitoring.service
 [Unit]
 Description=Monitoring Service
 
 [Service]
 ExecStart=/usr/local/bin/monitoring.sh
 Type=oneshot

 #monitoring.timer
 [Unit]
 Description=Run monitoring on a timer

 [Timer] #type 1
 OnBootSec=1min
 OnUnitActiveSec=1min
 Unit=monitoring.service

 [Timer] #type 2
 OnCalendar=*:*:00/30 #30 seconds
 Unit=monitoring.service

 [Install]
 WantedBy=timers.target

 #enable and start timer
 sudo systemctl enable monitoring.timer

 #start immediately
 sudo systemctl start monitoring.timer

 #check status of unit timer
 sudo systemctl status monitoring.timer

 #check when the timer last triggered the service
 systemctl list-timers
 
 #observations
 - sometimes the system timer might not be precise, could be because it prevents overlaping execution of timer services, delaying the next timers, the system might skew or drift in instability, to prevent it it is possible to use:
	RandomizedDelaySec=10 # adds a maximum of 10 seconds delay over the timer, giving more room and reducing load on the system
