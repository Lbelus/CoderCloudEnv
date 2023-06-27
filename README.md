# How to put your coding environment in the cloud

## Introduction
### General overview
### Project benefits:
## Create your server with kamatera
## Setup your Codebase on cloud

## Introduction

This solution is suitable for students who want to create a setup that enhances their working conditions.

-> Current cost in 2023 : 6$ or 4$ per month.

### General overview
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

- Your codebase via cloud on a Ubuntu server using utility like ``rclone`` or ``google-drive-ocamlfuse``;
- Server to compile and run your code;
- Docker will allow to have a clean and reproductible environment;
- Git (to push on selected projects using a milestone logic);
- You can use any pc or mac with vscode (using a synced account);

You can use any PC and be operational in a matter of minutes, your code is remote and safe, your coding environment is clean and reproductible.


### Containers and server relation 

```
  ┌────────┬────────────────┬───────────────────────────────────┐
  │        │                │                                   │
  │        │                │                                   │
  │   PC   │     User       │     Modify file                   │
  │        │                │                                   │
  ├────────┼────────────────┼──────   │                         │
  │        │                │         ▼                         │
  │ Server │  Cloud volume  │     Update cloud                  │
  │        │                │                                   │
  │        │                │         │                         │
  │        │                │         ▼                         │
  │        │                │     Perform checksum  ◄──────┐    │
  │        │                │                              │    │
  │        │                │         │                    │No  │
  │        │                │         ▼                    │    │
  │        │                │     CheckSum has changed ?───┘    │
  │        │                │                                   │
  │        ├────────────────┼─────    │ Yes                     │
  │        │Container volume│         ▼                         │
  │        │                │     perform Sync                  │
  │        │                │                                   │
  │        │                │                                   │
  │        │                │                                   │
  └────────┴────────────────┴───────────────────────────────────┘
```


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
        - or setup an SSH key : https://www.linuxbabe.com/linux-server/ssh-windows 


Alternative to kamatera :

- https://www.paperspace.com/pricing
- https://www.vultr.com/pricing/
- AWS
- Azure
- Google

## Setup your Codebase on cloud

```
For this part we are going to mount OneDrive and google drive on your Ubuntu server using rclone and google-drive-ocamlfuse.
This is an example, OneDrive has some limitation and was not made for this case usage, same for google drive.
Please feel free to use any other solution.
```

### Google drive with google-drive-ocamlfuse

#### <u>Recommanded usage : Working directory</u>

Useful links:
- https://github.com/astrada/google-drive-ocamlfuse/wiki/Installation
- https://github.com/astrada/google-drive-ocamlfuse/wiki/Headless-Usage-&-Authorization

```bash
sudo add-apt-repository ppa:alessandro-strada/ppa
sudo apt-get update
sudo apt-get install google-drive-ocamlfuse
```
- Follow steps on https://github.com/astrada/google-drive-ocamlfuse/wiki/Headless-Usage-&-Authorization you will have to create Oauth client ID.

```bash
google-drive-ocamlfuse -headless -label me -id XXyourClientIDXX.apps.googleusercontent.com -secret XXXyoursecretXXXX
``` 
- Click on link and authorize 
- Copy verification code into terminal
- Create a dir
```bash
mkdir ~/google-drive
```
- Mount the drive
```bash
google-drive-ocamlfuse -label me ~/google-drive
```

### OneDrive with rclone

Recommanded usage : archive for old project and files

``rclone`` works with a manner of cloud storage solution including ``google drive``. 

Useful links:
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

# Client id
$

# Client secret
$

# Option region
$ 1

# Edit advanced config
$

#edit config
	# If you are on headless machine abd FOLLOW THE PROCEDURE INDICATED ON YOUR TERMINAL https://rclone.org/remote_setup/
	$ n
	# If you are on a desktop 
	$ y

# ON YOUR DESKTOP
# Procedure at https://rclone.org/remote_setup/
# Need to use your desktop connect using the command 
$ rclone authorize "OneDrive"

# Authenticate into website using web browser
# Copy the result and paste it in your remote server

# Finish install on server

# Create folder to Mount drive
$ mkdir ~/OneDrive

# Mount the drive 
# Command may vary depending on your cloud storage solution
# Consult :  https://rclone.org/remote_setup/

# For OneDrive only :
$ rclone --vfs-cache-mode writes mount onedrive: ~/OneDrive

# Create .sh file to hold that last command, you will have to run each time your reboot your server
```

## Create a shared volume on your server with samba


### Create a shared volume
Useful links :
- https://ubuntu.com/server/docs/samba-file-server

1. Update your server 
  ```bash
  sudo apt update
  sudo apt upgrade
  ```

2. Install samba
  ```bash
  sudo apt install samba
  ```

3. Modify /etc/samba/smb.conf with vim or nano 
  ```bash
  vim /etc/samba/smb.conf
  ```
  ```bash
  sudo nano /etc/samba/smb.conf
  ```
  - ctrl+o, enter and then ctrl+x;
4. Add this text at the end of the file.
  ```
  [share]
    comment = Ubuntu File Server Share
    path = /srv/samba/share
    browsable = yes
    guest ok = yes
    read only = no
    create mask = 0755
  ```
5. Create a shared folder and manage its permissions.
  ```bash
  sudo mkdir -p /srv/samba/share
  sudo chown nobody:nogroup /srv/samba/share/
  ```
6. Restart services to enable new configuration.
  ```
  sudo systemctl restart smbd.service nmbd.service
  ```

### Manage user and access

1. Create a new user
  ```bash
  sudo adduser sambauser
  ```
2. Create an access to samba
  ```bash
  sudo smbpasswd -a sambauser
  ```
3. Modify the /etc/samba/smb.conf file
```
[share]
  comment = Ubuntu File Server Share
  path = /srv/samba/share
  browsable = yes
  guest ok = no
  read only = no
  create mask = 0664
  directory mask = 2775
  valid users = sambauser
```
4. Restart services to enable new configuration.
  ```
  sudo systemctl restart smbd.service nmbd.service
  ```
### TROUBLESHOOT: I don't have the permissions to modify the files from windows

1. Try running :
```
sudo find /srv/samba/share -type f -exec chmod 664 {} \;
sudo find /srv/samba/share -type d -exec chmod 2775 {} \;
```
2. If the problem persist, check the group that own the dir.
```bash
ls -ld /srv/samba/share
``` 
3. Check samba user group 
```bash
sudo pdbedit -u sambauser -v
```
4. If needed change samba user group 
```bash
sudo usermod -a -G groupname sambauser
```
5. Restart services to enable new configuration.
  ```
  sudo systemctl restart smbd.service nmbd.service
  ```
6. Disconnect and reconnect to he shared folder on windows


### connect on windows

open the file explorer and type your ip
type your credential if you created a user with an access password


## Setup Docker on your remote server

1. Follow this: https://docs.docker.com/engine/install/ubuntu/

2. Simple test using ruby: https://www.javatpoint.com/docker-ruby-example

3. Manage your image and containers : https://www.freecodecamp.org/news/where-are-docker-images-stored-docker-container-paths-explained/#:~:text=The%20storage%20location%20of%20Docker%20images%20and%20containers&text=Ubuntu%3A%20%2Fvar%2Flib%2F,Windows%3A%20C%3A%5CProgramData%5CDockerDesktop

4. Get quickly started with ruby using Docker on your remote server : 
    
    - Pull the Ruby Docker Image
      ```bash
      sudo docker pull ruby
      ```
    - Create a directory on your host machine to access from container
      ```bash
      mkdir /path_to_directory
      ```
    - Create a test ruby my_file.rb with a puts "hello"
    - Run the Ruby Docker container
      ```bash
      sudo docker run -it --name my-ruby-app -v /path_to_directory:/app ruby /bin/bash
      ```
    - Check inside volume container
      ```bash
      ls /app
      ```
    - Test your program 
      ```bash
      ruby my_file.rb
      ```
    - Leave with ctrl+d or
      ```bash
      exit
      ```
    - Add new files to /path_to_directory (work also while container is running)
    - Re enter container
      ```bash
      sudo docker start -i my-ruby-app
      ```
    - Clean unused  containers and volumes
      ```bash 
      docker system prune -a
      docker volume prune
      ```

5. Get quickly started with llvm and c using Docker on your remote server : 
	- Create a ``my_test.c`` file with a ``printf("hello world")``;
	- Create a ``dockerfile``;
	    ```Dockerfile
		  FROM ubuntu:latest
		  RUN apt-get update && apt-get install -y llvm clang
	    ```
	From directory run: 
	  - Build the Docker image
      ```bash
      docker build -t my-llvm-image .
      ```
    - If you encounter an issue check image names
      ```bash
		  docker images
      ```
    - Compile your C programs by running a new container
	    ```bash	
      docker run -v ~/path_to_directory:/c my_llvm_image clang /c/my_test.c -o /c/my_test
	    ```	
    - Find container ID and copy it
	    ```bash
      docker ps -a
      ```
    - Extract the executable file
	    ```bash
		  docker cp <container_id>:/c/my_test /path_to_directory
	    ```

    
6. Get quickly started with llvm and c using Docker on your remote server : 
  - create a Dockerfile
	    
      ```Dockerfile
		  FROM ubuntu:latest
		  RUN apt-get update && apt-get install -y llvm clang build-essential
	  ```
  From directory run:
  - Build the Docker image

      ```bash
		  docker build -t my-llvm-image .
      ```
	
    - If you encounter an issue check image names
      ```bash
		  docker images
      ```
    - Create a container with a linked volume
	    ```bash
		  docker run -it -v ~/path_to_directory:/c my-llvm-image /bin/bash
		  ```
    - Go to directory
		  ```bash
      cd c/
		  ```
    - Compile using your Makefile
      ```bash
      make
      ```

6. Get quickly started with python and langchain using Docker on your remote server :
  - create a Dockerfile
	    
      ```Dockerfile
		# Use the official image as a parent image
		FROM python:latest

		# Set the working directory in the container
		WORKDIR /usr/src/app

		# Copy the requirements file into the container
		COPY requirements.txt .

		# Install any needed packages specified in requirements.txt
		RUN pip install --no-cache-dir -r requirements.txt
	  ```
          - If you encounter an issue check image names
      ```bash
		  docker images
      ```
      from Dockefile dir, build the Docker image, run docker "build -t your-image-name ." in your terminal. 
    - Create a container with a linked volume
	    ```bash
		  docker run -it -v /path/to/your/local/dir:/usr/src/app your-image-name /bin/bash
		  ```
    - run python
      ```bash
      python3
      ```


## Mirror your Google-drive work_folder with your docker host volume
*******RSYNC CAUSE KERNEL MEM LEAK******* NEED TO INVESTIGATE
1. Create my_mirror.sh file and copy following code 
```bash
#!/bin/bash

# Define the source and target directories
SRC_DIR="google-drive/work_folder/"
TARGET_DIR="Dockerfolder/host/"

# Create the target directory if it doesn't exist
if [ ! -d "$TARGET_DIR" ]; then
  mkdir -p "$TARGET_DIR"
fi

# Mirror the repository
rsync -av --delete --relative "$SRC_DIR" "$TARGET_DIR"

# rsync options:
# -a: archive mode, preserves permissions, ownership, timestamps, etc.
# -v: verbose mode, shows the files being copied
# --delete: delete files in the target directory that are not present in the source directory
# --relative: use relative paths to preserve the directory structure
# "$SRC_DIR": the source directory (only includes the work_folder directory)
# "$TARGET_DIR": the target directory
```
make you my_mirror_loop_.sh an executable
```
chmod +x my_mirror.sh 
```
run it
```
./my_mirror.sh.
```
run the executable each time you need to sync your work_folder with your container volume. 

alternative : tested 
https://github.com/rsnapshot/rsnapshot

## Ongoing project automate sync 

tmp fix 

create my_mirror_loop.sh file

```bash
#!/bin/bash

script_to_execute="./my_mirror.sh"

while true; do
  $script_to_execute
  sleep 2
done
```

change persmissions for file. 
```
chmod +x my_mirror_loop.sh
```
run it
```
./my_mirror_loop.sh
```
```
  ┌────────┬────────────────┬───────────────────────────────────┐
  │        │                │                                   │
  │        │                │                                   │
  │   PC   │     User       │     Modify file                   │
  │        │                │                                   │
  ├────────┼────────────────┼──────   │                         │
  │        │                │         ▼                         │
  │ Server │  Cloud volume  │     Update cloud                  │
  │        │                │                                   │
  │        │                │         │                         │
  │        │                │         ▼                         │
  │        │                │     Perform checksum  ◄──────┐    │
  │        │                │                              │    │
  │        │                │         │                    │No  │
  │        │                │         ▼                    │    │
  │        │                │     CheckSum has changed ?───┘    │
  │        │                │                                   │
  │        ├────────────────┼─────    │ Yes                     │
  │        │Container volume│         ▼                         │
  │        │                │     perform Sync                  │
  │        │                │                                   │
  │        │                │                                   │
  │        │                │                                   │
  └────────┴────────────────┴───────────────────────────────────┘
 ```


## Setup your SSH

### PERSONAL SSH for github

``$ `` means press enter
On your Ubuntu server:
```bash
$ ssh-keygen -t rsa
$ y
$
$

# Copy from: 
$ cat ~/.ssh/id_rsa.pub

# go to Github account
# ->Settings
# ->*SSH and GPG section 
# ->New SSH key button
# and paste
```

### Access you server from windows without password: 

``$ ``  means press enter
```bash
# open powershell and generate ssh
$ ssh-keygen -t rsa -b 4096
$
$
$

# Copy output from
$ cat .ssh/id_rsa.pub

# Create authorized host list
$ sudo nano ~/.ssh/authorized_keys

# Copy SSH public key into nano
# Ctrl+O then press Enter to save. Ctrl+X to close.

# Permission on file (IMPORTANT)
$ sudo chmod 600 ~/.ssh/authorized_keys

$ Exit
```
## Manage your server and its process

shorten prompt : 
```bash
PROMPT_DIRTRIM=1 
```
or copy inside ``.bashrc`` file

https://www.cyberciti.biz/faq/how-to-check-running-process-in-ubuntu-linux-using-command-line/

# On your desktop

## Set up Vscode 

1. Download vscode
2. Set up Sync via github account to synchronise extension and parameters 

## Set up OneDrive on windows

1. Ddl onedrive
1. Run setup
1. Connect with account

## Set up Google drive on windows
1. Ddl Google Drive desktop client 
1. run setup
1. Connect with account
1. Go into preferences
    - tab ``google drive``
    - select ``mirror files``

## Symlink to navigate into vscode on windows
1. Create a dir to hold symlinks to every cloud storage solution
2. Open prowershell as an administrator 
3. ``$ New-Item -Path new_symlink_name -ItemType SymbolicLink -Value path_to_directory``
4. Do the same with cmdprompt as administrator for a shared directory. 
5.  ``mklink /D C:\Users\lbs_h\Codebase\new_work_dir \\IP_ADRESS\share``
## 

mklink /D C:\Users\YourUsername\Documents\MySharedFolder \\servername\share

### The Core Team
* [Lorris BELUS](//github.com/Lbelus) - Developer

### Contributor
* [@kahala](//github.com/kahala) - Developer

<span><i>Made at <a href='https://qwasar.io'>Qwasar SV -- Software Engineering School</a></i></span>
<span><img alt='Qwasar SV -- Software Engineering School's Logo' src='https://storage.googleapis.com/qwasar-public/
