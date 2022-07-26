##### Certification creation

openssl genrsa -out ca.key 4096
openssl req -x509 -new -nodes -sha512 -days 3650 \
 -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=mydomain.com" \
 -key ca.key \
 -out ca.crt
 
 openssl genrsa -out mydomain.com.key 4096
 
 openssl req -sha512 -new \
    -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=mydomain.com" \
    -key mydomain.com.key \
    -out mydomain.com.csr


cat > v3.ext <<-EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1=mydomain.com
DNS.2=hostname.mydomain.com
DNS.3=hostname
EOF

openssl x509 -req -sha512 -days 3650 \
    -extfile v3.ext \
    -CA ca.crt -CAkey ca.key -CAcreateserial \
    -in mydomain.com.csr \
    -out mydomain.com.crt
	
	
	
cp mydomain.com.crt /data/cert/
cp mydomain.com.key /data/cert/

openssl x509 -inform PEM -in mydomain.com.crt -out mydomain.com.cert

sudo cp mydomain.com.cert /etc/docker/certs.d/mydomain.com/
sudo cp mydomain.com.key /etc/docker/certs.d/mydomain.com/
sudo cp ca.crt /etc/docker/certs.d/mydomain.com/

sudo cp mydomain.com.crt /usr/local/share/ca-certificates/mydomain.com.crt 
sudo update-ca-certificates


##### Harbor Components

Harbor primarily consists of eight different components:-

    Postgresql : It is the database used by the Harbor.
    Redis: It is an in-memory database used for storing the login information.
    Beego: It is an open source web app framework over which Harbor web service is being built and developed.
    Chartmuseum: It is used to manage Helm Charts.
    Docker/distribution: It is used for pushing and pulling of docker images.
    Docker/notary: It is used for signing and verifying docker images.
    Helm: It uses a packaging format called charts. Since version 1.6.0 Harbor is now a composite cloud-native registry which supports both container image management and Helm charts management.
    Swagger-ui: It is used to call and test RESTful API.

#### Step 1: Prerequisites
- Ubuntu 20.04 LTS Server
- sudo or root access
- apt or apt-get 
- curl, gpg and tar

#### Step 2: Install Docker
	apt update
    
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    
    apt update
    
    apt-get install docker-ce docker-ce-cli containerd.io
    
    curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    
    chmod +x /usr/local/bin/docker-compose

#### Step 3: Download Harbor

	wget https://github.com/goharbor/harbor/releases/download/v2.4.1/harbor-offline-installer-v2.4.1.tgz
    
    tar -xvzf harbor-offline-installer-v2.4.1.tgz
    
    cd harbor
    
    ls
    
    cp harbor.yml.tmpl harbor.yml
    
    vi harbor.yml
    change below:
    		hostname: hostname.mydomain.com
            	certificate: /data/cert/mydomain.com.crt
  				private_key: /data/cert/mydomain.com.key
     
      sudo ./install.sh --with-notary --with-trivy --with-chartmuseum

#### Step 4: Open Harbor UI
	https://hostname.mydomain.com
    
    username/pssword: admin/Harbor12345 (refer harbor.yml)
    
    



##### if you get below error 

ERROR: for chartmuseum  Cannot start service chartmuseum: failed to initialize logging driver: dial tcp [::1]:1514: connect: connection refused

Creating redis         ... error

ERROR: for registryctl  Cannot start service registryctl: failed to initialize logging driver: dial tcp [::1]:1514: connect: connection refused

ERROR: for registry  Cannot start service registry: failed to initialize logging driver: dial tcp [::1]:1514: connect: connection refused
Creating harbor-db     ... error
ERROR: for redis  Cannot start service redis: failed to initialize logging driver: dial tcp [::1]:1514: connect: connection refused

ERROR: for harbor-db  Cannot start service postgresql: failed to initialize logging driver: dial tcp [::1]:1514: connect: connection refused

ERROR: for chartmuseum  Cannot start service chartmuseum: failed to initialize logging driver: dial tcp [::1]:1514: connect: connection refused

ERROR: for portal  Cannot start service portal: failed to initialize logging driver: dial tcp [::1]:1514: connect: connection refused

ERROR: for registryctl  Cannot start service registryctl: failed to initialize logging driver: dial tcp [::1]:1514: connect: connection refused

ERROR: for registry  Cannot start service registry: failed to initialize logging driver: dial tcp [::1]:1514: connect: connection refused

ERROR: for redis  Cannot start service redis: failed to initialize logging driver: dial tcp [::1]:1514: connect: connection refused

##### Solution: Change the port Number in docker-compose.yml
	change port from 127.0.0.1:1514:10514 to 1514:10514 for container_name: harbor-log

no need to run install.sh script again. just give below coomands

	sudo docker-compose -f docker-compose.yml down
    sudo docker-compose -f docker-compose.yml up -d