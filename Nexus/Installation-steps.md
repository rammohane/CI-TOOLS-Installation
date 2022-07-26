# Installing  Nexus Artifactory in Ubuntu 20.04 Focal

#### Step1: install pre-req

sudo apt-get update
sudo apt install openjdk-8-jre-headless

#### Step2: Download and Unarchive
cd /opt
sudo wget https://download.sonatype.com/nexus/3/latest-unix.tar.gz
tar -zxvf latest-unix.tar.gz
sudo mv /opt/nexus-3.30.1-01 /opt/nexus

#### Step3: Add user and make No password

sudo adduser nexus
sudo visudo
Add:
nexus ALL=(ALL) NOPASSWD: ALL

#### Step4: Change permissions and make nexus as service user
sudo chown -R nexus:nexus /opt/nexus
sudo chown -R nexus:nexus /opt/sonatype-work

sudo nano /opt/nexus/bin/nexus.rc
Add:
run_as_user="nexus"

#### Step5: check file for modifications

/opt/nexus/bin/nexus.vmoptions

#### Step5: create nexus as a service

sudo nano /etc/systemd/system/nexus.service
Add below
[Unit]
Description=nexus service
After=network.target
[Service]
Type=forking
LimitNOFILE=65536
ExecStart=/opt/nexus/bin/nexus start
ExecStop=/opt/nexus/bin/nexus stop
User=nexus
Restart=on-abort
[Install]
WantedBy=multi-user.target

#### Step6:  commands to restart nexus service

sudo systemctl start nexus
sudo systemctl enable nexus
sudo systemctl status nexus

#### Step7: access application and change default password

http:/hostname:8081/
Admin/Admin123$$
