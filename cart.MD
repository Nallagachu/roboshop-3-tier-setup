 Cart

Cart is a microservice that is responsible for Cart Service in RobotShop e-commerce portal.

**Developer has chosen NodeJs, Check with developer which version of NodeJS is needed.**
**Developer has set a context that it can work with NodeJS >20**

Install NodeJS, By default NodeJS 16 is available, We would like to enable 20 version and install list.

**You can list modules by using `dnf module list`**

```shell 
dnf module disable nodejs -y
dnf module enable nodejs:20 -y
```

Install NodeJS 

```shell 
dnf install nodejs -y
```

Configure the application. Here
 
Our application developed by the own developer is not having any RPM software just like other softwares. So we need to configure every step manually

caution 
We already discussed in Linux basics section that applications should run as nonroot user.

Add application User

```shell 
useradd --system --home /app --shell /sbin/nologin --comment "roboshop system user" roboshop
```
 
User **roboshop** is a function / daemon user to run the application. Apart from that we dont use this user to login to server.

Also, username **roboshop** has been picked because it more suits to our project name.

We keep application in one standard location. This is a usual practice that runs in the organization.

Lets setup an app directory. 

```shell
mkdir /app 
```

Download the application code to created app directory. 

```shell
curl -L -o /tmp/cart.zip https://roboshop-artifacts.s3.amazonaws.com/cart-v3.zip
cd /app 
unzip /tmp/cart.zip
```

Every application is developed by development team will have some common softwares that they use as libraries. This application also have the same way of defined dependencies in the application configuration.

Lets download the dependencies. 

```shell 
cd /app 
npm install 
```

We need to setup a new service in **systemd** so `systemctl` can manage this service

We already discussed in linux basics that advantages of systemctl managing services, Hence we are taking that approach. Which is also a standard way in the OS. 

Setup SystemD Cart Service 

```unit file (systemd) title=/etc/systemd/system/cart.service
[Unit]
Description = Cart Service
[Service]
User=roboshop
// highlight-start
Environment=REDIS_HOST=<REDIS-SERVER-IP>
Environment=CATALOGUE_HOST=<CATALOGUE-SERVER-IP>
Environment=CATALOGUE_PORT=8080
// highlight-end
ExecStart=/bin/node /app/server.js
SyslogIdentifier=cart

[Install]
WantedBy=multi-user.target
```
 
You can create file by using **`vim /etc/systemd/system/cart.service`**

Load the service.

```shell 
systemctl daemon-reload
```
 
This above command is because we added a new service, We are telling systemd to reload so it will detect new service.

Start the service.

```shell 
systemctl enable cart 
systemctl start cart
```