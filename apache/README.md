# learn-Salt-stack:
![image](https://user-images.githubusercontent.com/58276505/173182427-0171cead-d0ec-409a-b85b-977a91e04f8e.png)
![image](https://user-images.githubusercontent.com/58276505/173182470-037333b5-a9ff-4f0a-84d3-a36741e64548.png)
![image](https://user-images.githubusercontent.com/58276505/173182480-a97e607a-9f74-417d-88ff-2c3cb62614c4.png)
![image](https://user-images.githubusercontent.com/58276505/173182500-910f19ce-f677-4130-be08-2dd2fd8f3752.png)
![image](https://user-images.githubusercontent.com/58276505/173182512-b500a1b6-23db-467b-9399-9f98707cd7a7.png)

### Step-1: Create an ec2-instance(master)

* (a) Create Ec2-Ubuntu (SaltMaster)
* (b) In Security group=> Open port: custom TCP: 4505-4506 to public on / Describtion: [SaltMaster ports for communication]
* (c) sudo -s (to avoid repeating sudo)
* (d) Install Saltstack on Master (dependencies required: python, yaml)

```
curl -L https://bootstrap.saltstack.com -o install_salt.sh
sudo sh install_salt.sh -M -P

### sudo salt --version---------
### Check port open too---------
netstat -aon | grep 450
```
![image](https://user-images.githubusercontent.com/58276505/173183227-06debc5f-31d3-405b-ba58-4ff7af66bfd8.png)

### Step-2: Configure Salt-Master and Salt-Minion on Same Instance

```
#### Locate salt in instance(Master) & cd into it by using command
whereis salt
cd /etc/salt
ls

#### Edit connetion between inner minion to master: uncomment & give-names to {id},  {master}
sudo vi minion  =>  { id: myminion  &  master: localhost }

#### Close file {esq + :wq} and Retart salt-minion & salt-master
* sudo service salt-minion restart
* sudo service salt-master restart

#### Check salt key
* sudo salt-key
* sudo salt-key -f myminion
* sudo salt-key -a myminion -y
* sudo salt-key

#### Test the connection 
* sudo salt '*' test.ping

#### All salts state required to run salt needs a directory, so, create a directory
* sudo mkdir /srv/salt
```

![image](https://user-images.githubusercontent.com/58276505/173194382-631f5577-e857-4446-977b-64939bebee09.png)

![image](https://user-images.githubusercontent.com/58276505/173195517-7986c746-ead9-4a6c-a58f-115a0c5bfd2e.png)

### For an external Minion to connect to the master in a different instance:

#### Create new linux-ec2-instance(Minion) and install SaltStack like in step-1
#### Edit connetion between externel minion to master: uncomment & give-names to {id},  {master}

* sudo vi minion  =>  {id: <anyname_minion1> & master:<Private-ip-address-master>}
* sudo systemctl start salt-minion
* sudo systemctl start salt-master
* sudo systemctl enable salt-master.service
* sudo systemctl enable salt-minion.service

#### Repeat Step-2:
* To configure & enable connection between ext-salt-minion1 to salt-master by adding the new key-ID parameter**
* sudo salt-key -a minion1 -y
* sudo salt-key

#### Test the connection 
* sudo salt '*' test.ping

#### Now to check directory
* sudo slat '

![image](https://user-images.githubusercontent.com/58276505/173196655-f33cd7c8-5aef-4c07-bfc5-c7d5d20687ab.png)

## Executing commands in salt  (More executatble command on salt official documentation)
* https://docs.saltproject.io/en/getstarted/
* https://docs.saltproject.io/en/getstarted/overview.html
* https://docs.saltproject.io/en/latest/contents.html
 
## Manipulations in saltstacks

#### 1: View home directory in salt

* sudo salt '*' cmd.run 'ls -lah /home'  (where, '*' equals for all, & could be specified for a given directory)
* sudo salt 'minion1' cmd.run 'ls -lah /home'
* sudo salt 'master' cmd.run 'ls -lah /home'

![image](https://user-images.githubusercontent.com/58276505/173200830-12fe93d0-a9f7-4a67-9333-f304b511b278.png)

#### 2: Look at system up time
* sudo salt '*' cmd.run 'uptime'  (Systems time)

#### 3: Create and Destroy Users in salt Minion or Master

```
# For user:
cat /etc/passwd
sudo salt '*' cmd.run 'cat /etc/passwd'  ........(check users)

# Add users
sudo salt '*' user.add 'testuser1' ..............(add users)
sudo su testuser1     ........ (loggin to user)

# Delete user
sudo salt '*' user.delete remove=true force=true
sudo salt 'master' user.delete remove=true force=true  .....(remove users from master)
```

## Create configuration Environment for Salt state

```
whereis salt
cd /etc/salt
ls
cd master.d   
vi roots.conf
#-------------------------------
 file_roots:
  base:
    - /srv/salt/base
  #dev:
    #- /srv/salt/dev
  #QA:
    #- /srv/salt/QA
#--------------------------------
sudo mkdir -p /srv/salt/base

 # Now type to see environment configuration
salt
```

## Create Top sls File for Salt state

```
touch top.sls
sudo vi top.sls
'*':
'my*': # All minion with a minion_id that begins with 'my'
  - apache # Apply the state file named 'apache.sls'
```
