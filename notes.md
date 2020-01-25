## Get version of debian
cat /etc/debian_version
https://linuxconfig.org/check-what-debian-version-you-are-running-on-your-linux-system

## Update and upgrade

sudo apt-get update
sudo apt-get upgrade

https://techreviewpro.com/sudo-apt-get-update/


## switch user

Find out what user you are
whoami 

Switch
su root

## see what's installed

dpkgs --get-selections | grep ssh

## see architecture and system stats
lscpu

## uninstall packages

apt-get remove --purge nameOfPackage
apt-get clean

# Steps to go from nothing to ssh ing into a virtual box debian server
Created a .net core 3.0 MVC application
Linked it to GitHub

Installed VirtualBox with all the default settings

Downloaded small installation image of debian 10.2

Installed debian onto VirtualBox with a dynamic virtual hard disk (chose the ISO file as the startup disk)

hostname: debian
domain name: christianlowry.com
password:
full name: Christian Lowry
username: christian
password:

All default values, until Software Selection. Then I had these selected: web server, SSH server, standard system utitlities

Within Virtual Box, go to the virtual machine, then settings, then network, and then switch "Attached to: NAT" to "Attached to: Bridged Adapter"

On the vm, type ip a
Should be 2 entries: a local loopback and a second. Use the second's ip address

nano /etc/ssh/sshd_config

Uncomment the PasswordAuthentication yes line. Save

Should be all set. Now use PuTTY to ssh into the machine.


# Install Jenkins (https://www.howtoforge.com/tutorial/how-to-install-java-jdk-jre-on-debian-10/)

wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -

nano /etc/apt/sources.list
deb https://pkg.jenkins.io/debian-stable binary/


## First install Java

sudo apt install -y default-jdk

## TThen install jenkins

sudo apt-get update
sudo apt-get install jenkins

it defaults to port 8080. so go to the ip:8080


# installed Git

sudo apt install git

# Install dotnet sdk (https://docs.microsoft.com/en-us/dotnet/core/install/linux-package-manager-debian10)

wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.asc.gpg

sudo mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/

wget -q https://packages.microsoft.com/config/debian/10/prod.list

sudo mv prod.list /etc/apt/sources.list.d/microsoft-prod.list

sudo chown root:root /etc/apt/trusted.gpg.d/microsoft.asc.gpg

sudo chown root:root /etc/apt/sources.list.d/microsoft-prod.list


sudo apt-get update
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install dotnet-sdk-3.1

sudo apt-get update
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install aspnetcore-runtime-3.1

<!-- As I read the instructions, it seems like the above are necessary, and the aspnetcore runtime includes the dotnet runtime. So we'll see. I removed the below -->
<!-- sudo apt-get update
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install dotnet-runtime-3.1 -->


# Configure Jenkins

selected the following:

Folders
OWASP Markup Formatter
Build Timeout
Credentials Binding
Timestamper
Workspace Cleanup
MSBuild
xUnit
Pipeline
GitHub Branch Source
Pipeline: Stage View
Git
GitHub
SSH Slavew
Matrix Authorization STrategy
PAM Authentication
LDAP
Email Extension
Mailer


If you get this error message, then you likely need to just go back to ip:8080 and reenter your credentials

# Jenkins is configured, but to get GitHub webhooks to work with local virtual machines, need a way for GitHub to contact local vm. ngrok is the tool for that
(https://medium.com/@developerwakeling/setting-up-github-webhooks-jenkins-and-ngrok-for-local-development-f4b2c1ab5b6)

Downloaded it https://dashboard.ngrok.com/get-started

Followed those instructions (extracted the zip file, opened the exe, ran this command: ngrok authtoken theAuthTokenFromTheSite)

start jenkins, if not already started:
sudo systemctl start jenkins

make sure it's running:
sudo systemctl status jenkins


Back on your local machine (not virtual), do this in cmd

ngrok http 8080

That should show you the URL to forward with. Put that into GitHub settings' Payload URL. Keep other settings default.

On vm, do this:

sudo su -s /bin/bash jenkins
ssh-keygen (do all the defaults, leaving whatever blank)
cat /var/lib/jenkins/.ssh/id_rsa.pub

put that into GitHub's Settings -> Deploy Keys as the Key. Left "Allow write access" unchecked.

whoami (verify you're still the Jenkins user)
ssh git@github.com (this confirms that the key was setup correctly. As long as it says you've successfully authenticated, you're set)

