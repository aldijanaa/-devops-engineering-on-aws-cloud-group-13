
# Building a Highly Available, Scalable Web Application

## Objective
This project aims to plan, design, build, and deploy the web application to the AWS Cloud by staying consistent with the best practices of the AWS Well-Architected Framework. 
The goal is to ensure high availability, scalability, security and performance of Example University's student records web application, which should be able to handle thousands of users simultaneously. 




## Key Requirements

1. __Functional__: The solution should meet the functional requirements, such as the ability to view, add, delete, or modify the student records, without any perceivable delay.
2. __Load balanced__: The solution should properly balance user traffic to avoid overloaded or underutilized resources.
4. __Scalable__: The solution should be designed to scale to meet the demands that are placed on the application.
5. __Highly available__: The solution should be designed to have limited downtime when a web server becomes unavailable.
6. __Secure__:
   - The database should be secured and can’t be accessed directly from public networks.
   - The web servers and database can be accessed only over the appropriate ports.
    - The web application should be accessible over the internet.
    - The database credentials shouldn’t be hardcoded into the web application.
7. __Cost optimized__: The solution should be designed to keep costs low.
7. __High performing__: The routine operations should be performed without a perceivable delay under normal, variable, and peak loads.



## Phase 1: Planning the design and estimating the cost 

### Task 1: Creating an architectural diagram

In the scope of this task, we have created an initial architectural diagram to illustrate what we plan to build, as seen on the image below. 

![Architectural Diagram](docs/architectural-diagram.jpg)


### Task 2: Developing a cost estimate

In the scope of this task, we have created a cost estimate using AWS Pricing Calculator that shows the cost to run the solution in the us-east-1 Region for 12 months.

To view cost estimate documentation click [HERE](./docs/cost-estimate.pdf)       
<<<<<<< Updated upstream
=======


## Phase 2: Creating a basic functional web application
In this phase, the goal was to develop a highly available, highly scalabe and secure web application.

### Task 1: Creating a virtual network

We first set up our VPC, and following our initial architectural diagram, the VPC contains 2 Availability Zones, each having a public and a private subnet (2 public, 2 private subnets in total). To allow public access to our application, we had to set up an Internet Gateway to connect our VPC. We also created a public route table to define where the traffic should be directed, and attached our previously created Internet Gateway to it. 


### Task 2: Creating a virtual machine

We started off by defining a Security Group to enchance security of our application, and to define Inbound and Outbound Rules. 
Afterwards, we created a EC2 instance for the first AZ (us-east-1a).  


### Task 3: Testing the deployment

During this task, we tested the deployment of the application on the EC2 instance by entering the public IPv4 addresses of our instance, and then performing various operations like viewing, adding, deleting, and modifying records to ensure everything works as expected. We found that these operations work correctly on the instance. 


At this point in the project, after Phase 2, we achieved the following structure as shown on the diagram:

![Phase 2 Diagram](docs/phase-two-diagram.png)



## Phase 3: Decoupling The Application Components
In this phase, the goal was to seperate the database and web server infrastructure so that they run independently.


### Task 1: Changing the VPC configuration

As our original architecture already included two AZs with public and private subnets each, we did not need to do any updating or reconfiguration of the VPC, therefore we skipped task 1 and immediately went to task 2.


### Task 2: Creating and configurating the Amazon RDS Databse

During this task we created an Amazon RDS Database that runs on MySQL Engine, during this we have also defined a Security Group allowing only the web application to access the database. The database is deployed on the private subnets for improved security.


### Task 3: Configuring the development environment

Next step was to setup Cloud9 IDE which we have done sucessfuly, then we made sure that Cloud9 has access to both EC2 Instances and RDS Database by defining Security Groups. 


### Task 4:  Provisioning SecretsManager 

After setting up the Cloud9 environment, we had moved on to creating a secret to store the database credentials using AWS SecretsManager and running the following command:

```bash
aws secretsmanager create-secret \
    --name Mydbsecret \
    --description "Database secret for web app" \
    --secret-string "{\"user\":\"admin\",\"password\":\"group13password\",\"host\":\"group-13-db.cgfhxaeheliy.us-east-1.rds.amazonaws.com\",\"db\":\"STUDENTS\"}"
```

Previous code can also be found inside the cloud9-scripts.yml file, which is available [here](./helper-scripts/cloud9-scripts.yml).


### Task 5: Provisioning a new instance for the web server

In this task, we had created a new EC2 Instance inside the public subnet of the second AZ (us-east-1b). Upon creation, we set up the EC2 instance with user data, located in the helper-scripts folder, which is available on the following [link](./helper-scripts/UserdataScript-phase-2.sh).
Afterwards, by modifying the Security Details of the newly created instance, we attached the existing _LabInstanceProfile_ to it, which attaches an IAM role (_LabRole_) to the instance so that it can fetch the secret securely. 

### Task 6: Migrating the database 

After successfuly creating the EC2 instance, next step was to migrate database from _Phase 2_ into the newly created RDS. 
We achieved this by running these two commands:

1. Following command exports data from existing server
```bash
mysqldump -h 10.0.4.180 -u nodeapp -p --databases STUDENTS > data.sql
```
10.0.4.180 is the Internal IP Address of our EC2 Instance that was created in _Phase 2_

2. Following command exports data into RDS.
```bash
mysql -h group-13-db.cgfhxaeheliy.us-east-1.rds.amazonaws.com -u admin -p STUDENTS < data.sql
```
group-13-db.cgfhxaeheliy.us-east-1.rds.amazonaws.com is the RDS Endpoint.

These commands can also be found inside the cloud9-scripts.yml file, which is available [here](./helper-scripts/cloud9-scripts.yml).


### Task 7: Testing the application

Upon completing the previous tasks, the final step was to test the application. We tested the application by performing basic operations including viewing, adding, deleting and modifying student records. We concluded that the database was successfuly migrated from the old EC2 Instance into RDS.

After _Phase 3_, following stucture was achieved as shown on diagram:

![Phase 3 Diagram](docs/phase-three-diagram.jpg)
>>>>>>> Stashed changes
