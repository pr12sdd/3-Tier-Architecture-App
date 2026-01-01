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

 The reason for having the above info is our App Servers running in Private Subnets should be able to connect to the DB, for that   connectivity it is going to use these credentials provided in DbConfig.js file.
 Update the above code and upload the Dbconfig.js file in the S3 bucket of 'app-tier' folder.

 

