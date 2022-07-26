# Installing SONARQUBE in Ubuntu 20.04 Focal

#### Step1: install pre-req
apt install openjdk-11-jdk

sudo sysctl -w vm.max_map_count=524288
sudo sysctl -w fs.file-max=131072
ulimit -n 131072
ulimit -u 8192


#### Step2: As non-root user execute below to add queues
Install Postgre 
wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O- | sudo apt-key add -
echo "deb [arch=amd64] http://apt.postgresql.org/pub/repos/apt/ focal-pgdg main" | sudo tee /etc/apt/sources.list.d/postgresql.list

#### Step2: Install POstgres13
sudo apt update

sudo apt install postgresql-13
sudo systemctl status postgresql

sudo systemctl start postgresql@13-main
sudo systemctl status postgresql@13-main

#### Step3:create postgres user and change password
sudo passwd postgres

postgres123$$

#### Step3:switch as postgres user created above and create DB
su - postgres

createuser sonaruser

Psql
ALTER USER sonaruser WITH ENCRYPTED password sonaruser123$$
CREATE DATABASE sonardb OWNER sonaruser

#### Step4:Make sure DB created with \l command and quit
\q
Exit


#### Step5: Install SonarQube

wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.0.1.46107.zip
sudo apt -y install unzip

sudo unzip sonarqube-*.zip -d /opt
sudo mv /opt/sonarqube-* /opt/sonarqube

#### Step5: Config DB parameters in /opt/sonarqube/conf/sonar.properties

sudo vi /opt/sonarqube/conf/sonar.properties
sonar.jdbc.username=sonaruser
sonar.jdbc.password=sonaruser123$$
sonar.jdbc.url=jdbc:postgresql://localhost/sonardb


#### Step5: SonarQube service

sudo vi  /etc/systemd/system/sonar.service
Change user and group
[Unit]
Description=SonarQube service
After=syslog.target network.target
[Service]
Type=forking
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
LimitNOFILE=131072
LimitNPROC=8192
User=bcadmin
Group=bcadmin
Restart=on-failure
[Install]
WantedBy=multi-user.target

#### Step6: id any errors with vm.max_map_count set again

sudo chmod -R 777  /opt/sonarqube/
sudo sysctl -w vm.max_map_count=262144

#### Step7: SonarQube restart commands using systemctl and script

sudo systemctl daemon-reload
sudo systemctl enable sonar 
sudo systemctl start sonar
sudo systemctl status sonar

sudo -Hu sonar /opt/sonarqube/bin/linux-x86-64/sonar.sh start
sudo -Hu sonar /opt/sonarqube/bin/linux-x86-64/sonar.sh stop
sudo -Hu sonar /opt/sonarqube/bin/linux-x86-64/sonar.sh status
sudo -Hu sonar /opt/sonarqube/bin/linux-x86-64/sonar.sh console

sudo ufw allows 9000/tcp

#### Step7:Access Application and change dafault password

http://hostname.dubaixxxxx.ae:9000
username/password: admin/ChangedPass$$
