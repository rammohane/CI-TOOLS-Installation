#Installing Gitlab in Ubuntu 20.04 Focal

####Step1: install pre-reqs

sudo apt update && sudo apt upgrade
sudo apt install curl openssh-server ca-certificates tzdata perl
sudo apt install postfix -y

####Step2: install Keys
curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash

####Step3: If error in installation (unable to find gitlab-ce)

sudo tee /etc/apt/sources.list.d/gitlab_gitlab-ce.list<<EOF

deb https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ focal main

deb-src https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ focal main

EOF

####Step3: Install Gitlab

sudo apt install gitlab-ce

#### Step3: change external URL

vi /etc/gitlab/gitlab.rb

external_url 'http://hostname.dubaixxxx.ae/gitlab'

sudo ufw allow http
sudo ufw allow https
sudo ufw allow OpenSSH


gitlab-ctl reconfigure

#### Step4: Get default password

cat /etc/gitlab/initial_root_password

#### Step5: login to application and change default password

http://hostname.dubaixxxx.ae/gitlab
root/RootXXXXX

