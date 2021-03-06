# G3Pot

This honeypot logs all connections done to the port 22! 
The following commands need to be followed to set up this project in a brand new EC2 with the Ubuntu 20.4 AMI on AWS.

# install Docker

sudo apt update

sudo apt install apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"

sudo apt update

sudo apt install docker-ce

sudo systemctl status docker

sudo usermod -aG docker ${USER}

# Install Docker-compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.28.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

docker-compose --version

# Install Git
sudo apt install git

git --version

# Redirect Traffic with IPTables from port 22 to 2222

sudo iptables -A PREROUTING -t nat -p tcp --dport 22 -j REDIRECT --to-port 2222

# Clone the repository

git clone https://github.com/jonan18/G3Pot.git

cd G3Pot/

ssh-keygen -t rsa -f server.key

sudo docker build -t g3_honeypot .

sudo docker run -v ${PWD}:/usr/src/app -p 2222:2222 g3_honeypot

# Test
You can test it by simply running an SSH command to your EC2 : "ssh test@<EC2.Public.IP.Address>".

# Important !
You will need to delete the prerouting rule you stablished before when you want to exit your EC2, in order to be able to connect back later on. for that, you will have to use the following command

sudo iptables -t nat -v -L -n --line-number

sudo iptables -t nat -D PREROUTING {rule-number-here} 

Normally you will need to delete the first rule ince it's the only one we added.


# Bibliography
This simple Honeypot was done by following this tutorial: https://securehoney.net/blog/how-to-build-an-ssh-honeypot-in-python-and-docker-part-1.html
