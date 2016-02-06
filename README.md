# NodeTweetAnsible

## Setting up the Master Load Balancer from scratch:
1. SETUP ANSIBLE:
    - Update cache to be able to find repositories: apt-get update
    - Get the "add-apt-repository”: sudo apt-get install -y software-properties-common
    - Add Ansible's official repository: sudo add-apt-repository -y ppa:ansible/ansible
    - Update repositories: apt-get update
    - Install Ansible: sudo apt-get install -y ansible
    - Find ansible hosts & config file  cd /etc/ansible

2. SETUP GIT:
     sudo apt-get update
     sudo apt-get install git
     git config --global user.name “Roman Scher"
     git config --global user.email “scher.roman@gmail.com"

3. GIT CLONE nodeAnsible REPO INTO /etc/ansible FOLDER:
     git clone https://github.com/scherroman/nodeAnsible /etc/ansible/nodeAnsible
          copy config file & hosts files from nodeAnsible repo into ansible folder
      cp -i hosts ..
           cp -i ansible.cfg ..
          (assuming you are in repo folder)

4. GENERATE SSH KEY ON MASTER SERVER AND ADD PUBLIC KEY TO GITHUB nodeWiki DEPLOY KEYS (AS WELL AS OPEN NEBULA SO YOU CAN USE TO SSH TO PRIVATE MACHINES AND ACCESS APP REPO):
     ssh-keygen -t rsa
     cat /root/.ssh/id_rsa.pub

5. MAKE SURE SSH AGENT IS SETUP/READY
Startup ssh-agent:  eval $(ssh-agent -s)
Add ssh keys to agent:  ssh-add

5. MAKE SURE HOST FILE CONTAINS LIST OF CURRENT VM IP ADDRESSES UNDER CORRECT GROUPS
     [localServer]
     [frontEndServers]
     [databaseServers]

6. ANISIBLE COMMANDS TO DEPLOY APP:
     Testing all desired server ssh connections for hosts defined in hosts file:
               ansible all -m ping
SETUP ALL SERVERS:
     ansible-playbook setupAllServers.yml
UPDATE ALL SERVERS:
     ansible-playbook updateAllServers.yml
RESTART WEB APP:
     ansible-playbook restartWebApp.yml
RESTART WEB APP HARD:
     ansible-playbook restartWebAppHard.yml

7. TESTING THAT NGINX IS SETUP PROPERLY:
     ssh 10.0.0.xx
sudo service nginx configtest
if [FAIL], check nginx error logs

8. SET SYSTEM & NGINX FILE LIMITS
cd /etc/security/limits.conf copy text below into bottom of file
# This is added for Open File Limit Increase
*               hard    nofile          199680
*               soft    nofile          65535

root            hard    nofile          65536
root            soft    nofile          32768

# This is added for Nginx User
nginx           hard    nofile          199680
nginx           soft    nofile          65535

EVERY TIME YOU EXIT AN SSH SESSION AND THEN SSH BACK INTO THE MASTER VM, TO SSH-FORWARD PROPERLY TO THE PRIVATE VMS, YOU MUST START UP SSH-AGENT AGAIN WITH (haven’t been able to automate it without errors):
Startup ssh-agent:  eval $(ssh-agent -s)
Add ssh keys to agent:  ssh-add

NOTE: SOMETIMES TAKES A COUPLE OF MINUTES FOR SSH-AGENT FORWARDING TO START WORKING PROPERLY.

***USE ‘connection: local' IN PLAYBOOKS WHEN RUNNING TASKS ON LOCAL SERVER

CHECKING LOAD BALANCER NGINX SERVER ACCESS & ERROR LOGS:
nano /var/log/nginx/access.log
nano /var/log/nginx/error.log

CHECK PERFORMANCE OF CPUS ON MACHINES
top
CHECK NUMBER OF CPUs
lscpu
CHECK SPACE AVAILABLE ON DISK
df -h

WHAT HELPS RETHINKDB WRITE MORE INPUTS/SEC
- durability soft helps
- increasing CPUs per machine helps
- more shards helps
- having noreply in input can possibly cause problems with submission script

"Tunnelception":

ssh -L 9000:10.0.0.17:8080 130.245.168.239 -l root

where 10.0.0.17 is the private IP of the final destination machine and 130.245.168.146 is the IP of the load balancer.

reference: https://piazza.com/class/ic2s17jp8v4is?cid=35

Afterwards, go to localhost:9000, click on "tables", select table to shard, click on "reconfigure" and change as pleased.

Useful Commands:
Ansible Syntax Checking: ansible-playbook --syntax-check nginx.yml

Creating an Ansible Role:
    mkdir -p roles/xxxx
   cd roles/xxxx
  mkdir files handlers meta templates tasks vars
  mkdir handlers/main.yml meta/main.yml tasks/main.yml vars/main.yml

Directly tunneling to localhost:80 or localhost:3000

ssh -L 9000:10.0.0.3:8080 130.245.168.239 -l root

where 10.0.0.17 is the private IP of the final destination machine, 130.245.168.146 is the IP of the load balancer and 8080 is the port to connect to (this can be changed to either 80 or 3000)

Regular Expression Helper:

https://regex101.com/#javascript

cURL to Node.js converter (not 100% accurate but almost there):

http://curl.trillworks.com/#node