# Node-k8s-setup
This repository walks through, step by step, how I configure and manage Kubernetes nodes in my private lab environment.

## Hardware setup
For hardware, I’m using four refurbished PCs. I bought four HP Compaq 6200 Pro SFF units (i3-2100 4gb ram) for $15 each. These machines aren’t intended to be used as servers, but for my personal Kubernetes lab they’re more than enough.

To set up the entire hardware infrastructure, I connected all four PCs to a switch, and then connected the switch to my home router. Simple and easy.

## OS setup
I created a bootable USB stick with Ubuntu server on it and then installed the OS on each node.

During installation, it’s worth installing the OpenSSH server. This will allow you to access the machine via SSH from local host maachine.

I named each node consecutively: node01, node02, node03, and node04.

## Authorization & Authentication setup

### Turn on temporaly authentication by password on the node

Run below command on a node:

`sudo vi /etc/ssh/sshd_config`

Then look for a *PasswordAuthentication* entry. Uncomment it (if commented) and set it value to *yes*

`PasswordAuthentication yes`

Save and exit *vi*. 

Run below command on a node:

`sudo vi /etc/ssh/ssh_cofnig.d/50-cloud-init.conf`

Then look for a *PasswordAuthentication* entry. Uncomment it (if commented) and set it value to *yes*

`PasswordAuthentication yes`

Save and exit *vi*.

Restart ssh service by running below command:

`sudo service ssh restart`

### Setup ssh keys

On the node, generate private and public key running below command:

`ssh_keygen`

Then few times accept default values.

### Add public key to authorized keys list

Check the name of the public keyfile. 

Run below command on a node, and lookg for *\*.pub* file. (You can also get the neme of the file from previous command output):

`ls ~/.ssh/id_ed25519.pub`

In my case its *id_ed25519.pub*. Add the public key to the authorized keys by running below command:

`cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys`

### Check the ip address of the node

Run below command on a node:

`ip a`

Look for *inet* ip value. For example *192.168.0.157*

### Access the node via ssh from local host
On the local host, enter terminal and then access the node. In my example it's just *michal*. I set it during OS setup. Ip from previous step is *192.168.0.157*

`ssh michal@192.168.0.157`

Provide the password and login to the node.

### Copy private key to local host machine

From local host machine, using ssh connection from previous step - Read the private key on a node, and copy it to local host machine. The name of the private key file is the same like for *\*.pub* but without *\*.pub* at the end.

You can run below command to read and copy value of private key.

`cat ~/.ssh/id_ed25519`

### Turn off authentication by password on the node

The process of turning authentication by password is the same like turning on. Just set *PasswordAuthentication* entry to *no*.

Remember to restart ssh service.

`sudo service ssh restart`

From this moment, you should be able to authenticate to the node using the private key and password authentication should be turned off.

## Kubernetes cluster setup