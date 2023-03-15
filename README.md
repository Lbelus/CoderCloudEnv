# How to put your coding environment in the cloud

## introduction

This solution works for students who wish to setup an environment to improve their working condition.

-> Current cost in 2023 : 6$ or 4$ per month.

### General overview:

```
      ┌──►Codebase on cloud───┐
      │                       │
      │                       │
      │                       ▼
      │   git◄────────Ubuntu server  ◄──────── docker
      │
      │
      └──►Pc with IDE
```
### Project benefits:

- Your codebase via cloud on a Ubuntu server using the utility ``rclone``;
- Server to compile and run your code;
- Docker will allow to have a clean and reproductible environment;
- Git (to push on selected projects using a milestone logic);
- You can use any pc or mac with vscode (using a synced account);

You can use whatever PC and be operational in a matter of minutes, your code is remote and safe, your coding environment clean and reproductible.

## Create your server with kamatera

Go to ``https://www.kamatera.com/express/compute/``:
- Create an account;
- Setup your remote server;
- Go to ``create new server``;
- Choose ``zone``;
- Choose ``OS image`` : 
    1. ``server OS images`` 
    2. Choose ``ubuntu`` 
    3. Choose ``latest version``;
- Choose server spec: 
    - ``A for avalability, 1cpu, 2 gb ram, 20gb storage``;
- Setup password and server name; 
- Create server;

Connect to server (root is default user) :
- If on ``windows`` open ``Powershell`` and type in : 
    - ssh root@server_Ip_Adress
        - Password
        - or setup an ssh key : https://www.linuxbabe.com/linux-server/ssh-windows 

Alternative to kamatera :

- https://www.paperspace.com/pricing
- AWS
- Azure
- Google

## Setup your Codebase on cloud

```
For this part we are going to mount OneDrive on your Ubuntu server using rclone
This is an example, OneDrive has some limitation and was not made for this case usage.
Feel free to use any other solution.
```
``rclone`` works with a manner of cloud storage solution including ``google drive``. 

Usefull links:
- https://rclone.org/onedrive/
- https://askubuntu.com/questions/804421/mounting-onedrive-on-ubuntu-linux-command-line

To mount your cloud storage using ``Rclone`` on your server you need to install it two times:
On your desktop and on your server.

``rclone`` require access to web browser in order to authenticate.

On your desktop follow installation instruction on this link https://rclone.org/install/ 

On your server : 

``$ `` means press enter

```bash
# Download the latest version, current is 1.62.0
$ wget https://downloads.rclone.org/v1.xx.x/rclone-v1.xx.x-linux-amd64.deb
# Install 
$ sudo apt install ./rclone-v1.xx.x-linux-amd64.deb

# Configue your drive
$ rclone config
# Name your drive
$ onedrive

# Seek Microsoft OneDrive and its number: XX type the number in the terminal

$ XX
# client id
$
# client secret
$
#option region
$ 1
#edit advanced config
$

#edit config
	#if you are on headless machine abd FOLLOW THE PROCEDURE INDICATED ON YOUR TERMINAL https://rclone.org/remote_setup/
	$ n
	#if you are on remote desktop 
	$ y

# ON YOUR DESKTOP
# procedure at https://rclone.org/remote_setup/
# Need to use your desktop connect using hte command 
$ rclone authorize "OneDrive"
# authenticate into website using web browser
# copy the result and paste it in your remote server

# finish install on server

# create folder to Mount drive
$ mkdir ~/OneDrive

# Mount the drive 
# command may vary depending on your cloud storage solution
# consult :  https://rclone.org/remote_setup/
# for OneDrive only :
$ rclone --vfs-cache-mode writes mount onedrive: ~/OneDrive

# Congratulation, it is done. 
```

## Setup Docker on your remote server

1. follow this: https://docs.docker.com/engine/install/ubuntu/

2. Simple test using ruby: https://www.javatpoint.com/docker-ruby-example

3. Manage your image and containers : https://www.freecodecamp.org/news/where-are-docker-images-stored-docker-container-paths-explained/#:~:text=The%20storage%20location%20of%20Docker%20images%20and%20containers&text=Ubuntu%3A%20%2Fvar%2Flib%2F,Windows%3A%20C%3A%5CProgramData%5CDockerDesktop

4. Get quickly started with ruby on Docker on your remote server : 
    ```bash
    # Pull the Ruby Docker Image
    $ sudo docker pull ruby
    
    # Create a directory on your host machine to access from container
    $ mkdir /path_to_directory
    
    # Create a test ruby my_file.rb with a puts "hello"
    
    # Run the Ruby Docker container
    $ sudo docker run -it --name my-ruby-app -v /path_to_directory:/app ruby /bin/bash
    
    # check inside volume container
    $ ls /app
    
    # test your program 
    $ ruby my_file.rb

    #leave with ctrl+d or
    $ exit

    # add new files to /path_to_directory

    #re enter container
    $ sudo docker start -i my-ruby-app

    # clean unused  containers and volumes
    $ docker system prune -a
    $ docker volume prune
    ```



## Setup your SSH
### QWASAR

1. Download SSH Keys from upskill account settings
2. Unzip and move them into ``~/.ssh``
3. Run ``chmod 600 ~/.ssh/ssh_key_priv``
4. Add ``~/.ssh/config``
```
Host git.us.qwasar.io
  User git
  IdentityFile ~/.ssh/ssh_key_priv
```
5. Try git clone git@git.us.qwasar.io:xxx/xxx.git


### PERSONAL SSH for github

``$ `` means press enter

```bash
$ ssh-keygen -t rsa
$ y
$
$

#copy from: 
$ cat ~/.ssh/id_rsa.pub

# go to Github account
# ->Settings
# ->*SSH and GPG section 
# ->New SSH key button
# and paste
```

### set up Vscode 

1. Download vscode
2. Set up Sync via github account to synchronise extension and parameters 


### The Core Team
* [Lorris BELUS](//github.com/Lbelus) - Developer


<span><i>Made at <a href='https://qwasar.io'>Qwasar SV -- Software Engineering School</a></i></span>
<span><img alt='Qwasar SV -- Software Engineering School's Logo' src='https://storage.googleapis.com/qwasar-public/qwasar-logo_50x50.png' width='20px'></span>
