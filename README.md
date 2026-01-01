# 3-Tier-Architecture-App

### Phase 1: VPC Creation:
- Design and create a Virtual Private Cloud (VPC) to serve as the foundation for the project infrastructure.
### Phase 2: S3 Bucket and IAM Role Setup:
- Create an S3 bucket and upload the application code.
- Set up an IAM role with the necessary permissions and attach it to the EC2 instance.
### Phase 3: Database Configuration:
- Launch and configure an RDS instance to serve as the backend database.
### Phase 4: Application Tier Setup:
Deploy application-tier resources, including the configuration of an internal load balancer for traffic distribution within the tier.
 **Step 1: Go into the following path of cloned code "application-code/app-tier/DbConfig.js" and open 'Dbconfig.js' file and change the things accordingly as shown below:**
 ```module.exports = Object.freeze({
    DB_HOST: 'YOUR-DATABASE-ENDPOINT.ap-south-1.rds.amazonaws.com',
    DB_USER: 'admin',
    DB_PWD: 'Prakash1234',
    DB_DATABASE: 'webappdb'
 });
 ```
 The reason for having the above info is our App Servers running in Private Subnets should be able to connect to the DB, for that   connectivity it is going to use these credentials provided in DbConfig.js file.
 Update the above code and upload the Dbconfig.js file in the S3 bucket of 'app-tier' folder.

**Step 2: Creation of App Tier Resources:**

- **2.1 : In this instance we will do the App Server Setup and DB Server Configuration. Execute the below commands**
```bash

Install MySQL
sudo yum install mysql -y
```
- **2.2 : Configure MySQL Database**
  Connect to the database and perform basic configuration: Replace below info with your DB information
```bash
mysql -h <DB EndPoint> -u admin -p ----> Enter the Password i.e Prakash1234 (this is DB password).
```
- **2.3 : Lets create a database. The database name i'm creating is "webappdb" (This is same name that you should give in DvConfig.js file):**
```bash
CREATE DATABASE webappdb;
SHOW DATABASES;
USE webappdb;
```
- **2.4 : Execute the below code as a single code. Here we are creating a table with the name 'transactions':**
```bash
CREATE TABLE IF NOT EXISTS transactions(
  id INT NOT NULL AUTO_INCREMENT, 
  amount DECIMAL(10,2), 
  description VARCHAR(100), 
  PRIMARY KEY(id)
);
```
- **2.5 : To verify whether table got created or not:**
```bash
SHOW TABLES;
```
- **2.6 : Lets insert some info into the table:**
```bash
INSERT INTO transactions (amount, description) VALUES ('400', 'groceries');
```
- **2.7 : To verify whether the entry is really created or not:**
```bash
SELECT * FROM transactions;
```
You will see the info you have written

- **2.8 : To come out of the DB:**
```bash
exit
```
- **2.9 : Update Application Configuration to with DB information:**
Update the **application-code/app-tier/DbConfig.js** file with your database credentials.

- **2.10 : Install and Configure Node.js and PM2:**
```bash
curl -o- https://raw.githubusercontent.com/avizway1/aws_3tier_architecture/main/install.sh | bash
source ~/.bashrc
nvm install 16
nvm use 16 (You will see 'Now using node v16.20.2)
```
- **2.11 : To run node as a service, we will install pm2:**
```bash
npm install -g pm2
```
- **2.12 : Download application code from S3 and start the application:**
```bash
cd ~/ 
sudo aws s3 cp s3://<S3BucketName>/application-code/app-tier/ app-tier --recursive
ls ---> You will see 'app-tier' folder
cd app-tier/ 
npm install
ls ----> You will see 'index.js' file. We have to start that.
pm2 start index.js (You will see the status as 'online')
To verify:
pm2 list (or) pm2 status
pm2 logs (You will not see anything in red colour, everything in white colour you should see)
At the end you will see something like; http://localhost:4000
pm2 startup
pm2 save ---> To save the configuration
Verify that the application is running by executing
curl http://localhost:4000/health
It should return: This is the health check.
```
With this we have completed the application configuration.

### Phase 5: Creation of Internal Load Balancer for App Tier:
Goto the downloaded code folder in local system ----> Open nginx.conf file and in the end of the file you will see something like below:
```bash
 #proxy for internal lb
        location /api/{
                proxy_pass http://[REPLACE-WITH-INTERNAL-LB-DNS]:80/;
        }
```
Replace the LB DNS in the above

Upload the updated nginx.conf file to the S3 bucket

This one we are going to copy to the webserver in sometime.

### Phase 6: Web Tier Setup:
Provision web-tier resources and set up an external load balancer to manage incoming traffic from users.

```bash
sudo -su ec2-user (To work as an ec2-user)    
cd /home/ec2-user
curl -o- https://raw.githubusercontent.com/avizway1/aws_3tier_architecture/main/install.sh | bash
source ~/.bashrc
nvm install 16
nvm use 16

aws s3 cp s3://<S3 Bucker Name>/application-code/web-tier/ web-tier --recursive
 
ls ----> You will see 'web-tier'

cd web-tier
npm install
npm run build

sudo amazon-linux-extras install nginx1 -y

Update Nginx configuration:
cd /etc/nginx (Your are in nginx path)
ls ----> You will see 'nginx.conf' file

sudo rm nginx.conf
sudo aws s3 cp s3://<S3 Bucker Name>/application-code/nginx.conf .
Ex: sudo aws s3 cp s3://demo-3tier-project/application-code/nginx.conf .
sudo service nginx restart

chmod -R 755 /home/ec2-user

sudo chkconfig nginx on

To check the output of the App, we can check using the Web-Tier-Instance public IP. But before checking lets open port no 80 with http, Anywhere IPv4, 0.0.0.0/0 ---> Save rules ----> Now paste the pubic ip of Web-Tier-Instance in new tab of browser ----> You will see the app ----> Enter the data in the app
```
This is about 3 tier architecture with HA and Fault Tolerance.



This is about 3 tier architecture with HA and Fault Tolerance.



 

