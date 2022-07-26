# Installing Jenkins in Ubuntu 20.04 Focal

#### Step1: install pre-req
sudo apt-get install openjdk-8-jdk

#### Step2: get keys
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -

sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'


#### Step2: Install Jenkins

sudo apt-get update

sudo apt-get install jenkins

#### Step3:Login and chnage default Password

http://hostname.dubxxxx.ae:8080/

username: admin
initial password path : cat /var/lib/jenkins/secrets/initialAdminPassword

