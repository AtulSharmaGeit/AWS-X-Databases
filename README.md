# AWS-X-Databases
##  Project Overview

---
##  Table Of Content
-  [Visualize a Relational Database](Visualize-a-Relational-Database)
-  [Aurora Database with EC2](Aurora-Database-with-EC2)

---
##  Visualize a Relational Database

In this project we'll create our own relational database, populate it with data, and connect it to QuickSight, a tool in AWS for visualizing data. The **goal of this project** is to set up our own relational database and visualize it using some fun charts... all using AWS.

**Step - 1 : Login with your IAM user**

In AWS, a user is a person or a computer that can do things on the AWS cloud. When we create an AWS account for the first time, the login we get is called the root user of the AWS account. AWS actually recommends to not use root user for everyday tasks to protect it from security breaches. We should create IAM users instead. If a root user is a master key to AWS account, think of IAM users as key copies. IAM users have separate usernames and passwords to root user, and we can set them to have limited access to our account's resources.

-  [Head to your AWS Account](https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Fconsole.aws.amazon.com%2Fconsole%2Fhome%3FhashArgs%3D%2523%26isauthcode%3Dtrue%26state%3DhashArgsFromTB_ap-southeast-2_fffdf5be4bb1a27e&client_id=arn%3Aaws%3Asignin%3A%3A%3Aconsole%2Fcanvas&forceMobileApp=0&code_challenge=m-aiqeB2UZeXTGXNyugMP8L64zd_AGUxJl4HLnA-X1o&code_challenge_method=SHA-256) as the root user.
-  Open the **AWS** `IAM` console.
-  From the left hand navigation panel, choose **Users**.
-  Choose **Create user**.
-  For the User name, name it: `YourName-IAM-Admin`.
-  Make sure to select the checkbox next to **Provide user access to the AWS Management Console - optional**.‍

This does not apply to all accounts, but if you're prompted with a pop up panel that says **Are you providing access to a person?**, choose **I want to create an IAM user**.

![image alt](Databases-1)

-  For the console password, choose **Custom password**.
-  Type in a password that you will be able to remember/access in the future.

![image alt](Databases-2)

-  Deselect the checkbox for **Users must create a new password at next sign-in - Recommended**.
-  Choose **Next**.
-  In the permissions set up page, choose **Attach policies directly**.
-  From the list of **Permissions policies**, select **AdministratorAccess**.
-  Choose **Next**.
-  Choose **Create user**.
-  Voilà - you've just created your new user! **Stay on this page**.
-  Choose **Download .csv file**.

![image alt](Databases-3)

-  Copy the **Console sign-in URL**.
-  **Now you're ready to start using your IAM user**.
-  Log out of your root user's AWS Account.
-  Paste and go to your copied console sign-in URL.
-  Open your downloaded .csv file containing your user's access instructions.
-  Log in using your IAM user's username and password in the .csv file.

![image alt](Databases-4)

**Step - 2 : Create a Relational Database**

A relational database is a type of database that organizes data into tables, which are collections of rows and columns. Kind of like a spreadsheet! We call it "relational" because the rows relate to the columns and vice-versa. When a database is relational we can query it using a special language called **SQL** (Structured Query Language)

Strangely enough, there isn't really such a thing as a "normal" database. We have relational databases and non-relational databases (also known as NoSQL).

-  Take note of your **Region** in the top right of your AWS Console.
-  It doesn't matter exactly which Region you have, as long as you know it.

![image alt](Databases-5)

-  Head to your **Aurora and RDS console** - search for `rds` in search bar at the top of the screen.
-  In the left navigation bar, select **Databases**.
-  In the Database section, select **Create Database**.
-  On the Create database page, choose **Easy Create**.
-  In the Configuration section, make the following changes:
    -  For **Engine type**, choose **MySQL**.
    -  For **DB instance size**, choose **Free tier**.
 
**MySQL** is a relational database management system (RDBMS) that uses SQL as the language for database interaction. If databases were libraries, think of RDBMS as the librarians who know exactly where each book is located, how to organize new books that arrive, and how to maintain the library's order. MySQL is software used to create, manage, and interact with relational databases but is only one of many options for using SQL to manage relational data. Other options include PostgreSQL, MariaDB, or SQLite.

![image alt](Databases-6)

-  For **DB instance identifier**, type `QuickSightDatabase`.
-  For **Master username**, enter `admin`.
-  In the **Credentials management** section, select **Self managed**.
-  For **Master password**, type a unique password, and confirm password.
-  Make sure you save your database login details somewhere safe! You'll need them later on.

![image alt](Databases-7)

-  Leave the rest as the default settings and select choose **Create database**.
-  It may take five or more minutes for the database to be created.
-  Yay! We've created a new relational database.

**Step - 3 : Connect RDS to MySQL Workbench**

Now that we've got our relational database shell in AWS (this is what we call our 'RDS instance'), we need to create a few tables and enter in some data. To do this we'll be using one of the most popular database tools in the world: **MySQL Workbench from Oracle**.

AWS RDS can actually import data from S3 using tools like AWS Database Migration Service or SQL commands, but these methods can require more setup and configuration. If we're doing a large-scale data import then we might consider more AWS native workflows and CI/CD pipelines...but for this project MySQL Workbench suits us perfectly.

-  Open the [MySQL Workbench download page](https://dev.mysql.com/downloads/workbench/), select your operating system, and then download the top option.
-  While that's downloading, let's make our RDS public so that we can modify it from MySQL Workbench.
-  Open the Amazon RDS console, in the left-hand navigation pane, choose **Databases**.
-  Then, choose **QuickSightDatabase**.
-  On the **QuickSightDatabase** page, choose **Modify**.
-  Scroll down to the Connectivity section, choose **Additional Configuration**.
-  Then, choose **Publicly accessible**.

![image alt](Databases-8)

-  Choose **Continue**.
-  Choose **Apply immediately**.
-  Select **Modify DB instance**.

![image alt](Databases-9)

This can take some time, so don't be concerned if it takes five or so minutes for the modifcations to come through!

Next we want to modify the security group attached to your RDS instance so that your local machine can access your RDS instance.
-  In Amazon RDS in your AWS console, go to the left-hand navigation, and select **Databases**.
-  Then, choose **quicksightdatabase**.
-  On the **quicksightdatabase** page, in the Connectivity & security section, choose the **VPC security groups** link.

![image alt](Databases-10)

-  On the **Security groups** page, choose the **Security group ID**.
-  On the sg-default page, in the Inbound rules section, choose **Edit inbound rules**.
-  On the **Edit inbound rules** page, in the Inbound rules section, choose **Add rule**, and make the following changes:
    -  For **Type**, choose **All TCP** from the drop-down list.
    -  For **Source**, choose **My IP**.
 
![image alt](Databases-11)

-  Then, choose **Save rules**.

**What did we just change? And why?** Every RDS instance we create has a default security group attached to it. This security group controls the traffic going into and out of the database.

We need MySQL Workbench to access our database, so we need to change the inbound traffic rules in the security group. By saying that we only allow traffic from our current IP address, we are only allowing our machine to connect to the database - this will enable MySQL Workbench to connect when we use it from our local computer, which makes it good for security!

-  Verify that MySQL Workbench has downloaded successfully. Then, install and open the software.
-  Select **Databases** in the top menu bar, then **Manage Connections...**

![image alt](Databases-12)

-  Select **New** in the bottom left.

![image alt](Databases-13)

-  Enter the following details:
    -  For **Connection Name**, paste `AWS-QuickSightDatabase`
    -  For **Hostname**, return to your RDS database page for your QuickSightDatabase.
    -  Look under **Connectivity and security** to find the **Endpoint**. Example: `QuickSightDatabase.abc.us-east-1.rds.amazonaws.com,1433`
    -  Copy this into **Hostname** in your MySQL Workbench.
    -  Copy the **Port** from the same place in AWS to the **Port** field in MySQL Workbench.
 
![image alt](Databases-14)

-  For **Username**, type the username you entered when creating the **QuickSightDatabase** (probably `admin` unless you changed it).
-  For **Password**, select **Store in Keychain** ... then enter in your database password.

![image alt](Databases-15)

-  Then, choose **Test Connection**.
-  You should get a pop-up that say's **Successfully made the MySQL connection**.

![image alt](Databases-16)

**Step - 4 : Create Database Tables and Load Data**

Now that we've got our MySQL Workbench connected with our RDS instance, we can actually start to add our own tables and data.
-  Close any pop-ups that are still open in MySQL Workbench.
-  Select your newly created connection to open the MySQL Workbench Query Editor.

![image alt](Databases-17)

-  Select **Schemas** as the tab in the top left, next to **Administration**.

A database **schema** is like a blueprint or structure of how the data is organized in a database. It sets up how the database is structured, including the tables, what fields should go in those tables, the relationships between tables, and other advaced components of a database. Think of a schema as your design plan for how your data is stored and organized!

-  Right click on the blank space under the **Schemas** menu.
-  Select **Create Schema**.
-  Name your schema `QuickSightDatabase`.
-  Leave everything else as is, and select **Apply**.

![image alt](Databases-18)

-  Review the SQL Script pop-up; notice that it's showing you how the command looks in SQL.
-  Select **Apply**.
-  Once it's finished running, select **Close**.
-  Select the tab marked **Query 1** near the top of the screen.

**Can't find the 'Query 1' tab?** Try selecting the first file icon in the top tool bar. When you hover over the icon it says "Create a new SQL tab for executing queries". Click this icon to open a new query script! 

![image alt](Databases-19)

In our new Query script, copy and paste the following SQL query.

```sql
CREATE TABLE newhire(
empno INT PRIMARY KEY,
ename VARCHAR(10),
job VARCHAR(9),
manager INT NULL,
hiredate DATETIME,
salary NUMERIC(7,2),
comm NUMERIC(7,2) NULL,
department INT)
```

-  Run your Query script by selecting the lightning button above your script.

**Did you get an error?** If you get an error it might be because you haven't selected your schema in the right menu. Make sure that you **double-click** your schema **'QuickSightDatabase'**. Then run the query again!

![image alt](Databases-20)

-  To see the results from our query, delete the current query and replace it with the following:

```sql
SELECT * FROM newhire;
```

-  Run this new query to see the empty table you just created!

![image alt](Databases-21)

-  Now let's populate our new table by running another query.
-  Delete the current contents of your query script and paste in the following:

```sql
INSERT INTO newhire (empno, ename, job, manager, hiredate, salary, comm, department) VALUES
(1, 'JOHNSON', 'ADMIN', 6, '1990-12-17', 18000, NULL, 4),
(2, 'HARDING', 'MANAGER', 9, '1998-02-02', 52000, 300, 3),
(3, 'TAFT', 'SALES I', 2, '1996-01-02', 25000, 500, 3),
(4, 'HOOVER', 'SALES I', 2, '1990-04-02', 27000, NULL, 3),
(5, 'LINCOLN', 'TECH', 6, '1994-06-23', 22500, 1400, 4),
(6, 'GARFIELD', 'MANAGER', 9, '1993-05-01', 54000, NULL, 4),
(7, 'POLK', 'TECH', 6, '1997-09-22', 25000, NULL, 4),
(8, 'GRANT', 'ENGINEER', 10, '1997-03-30', 32000, NULL, 2),
(9, 'JACKSON', 'CEO', NULL, '1990-01-01', 75000, NULL, 4),
(10, 'FILLMORE', 'MANAGER', 9, '1994-08-09', 56000, NULL, 2),
(11, 'ADAMS', 'ENGINEER', 10, '1996-03-15', 34000, NULL, 2),
(12, 'WASHINGTON', 'ADMIN', 6, '1998-04-16', 18000, NULL, 4),
(13, 'MONROE', 'ENGINEER', 10, '2000-12-03', 30000, NULL, 2),
(14, 'ROOSEVELT', 'CPA', 9, '1995-10-12', 35000, NULL, 1);
```

-  Run the script by select the button again!
-  Once it's run, remove the query and run the following to see your results:

```sql
SELECT * FROM newhire;
```

![image alt](Databases-22)

Remove the current query and run the following to create and populate a second table:

```sql
CREATE TABLE department(
deptno INT NOT NULL,
dname VARCHAR(14),
loc VARCHAR(13));

INSERT INTO department (deptno, dname, loc) VALUES 
(1, 'ACCOUNTING', 'ST LOUIS'),
(2, 'RESEARCH', 'NEW YORK'),
(3, 'SALES', 'ATLANTA'),
(4, 'OPERATIONS', 'SEATTLE');
```

-  Once it's run, remove the query and run the following to see your results:

```sql
SELECT * FROM department;
```

![image alt](Databases-23)

**Step - 5 : Connect RDS to QuickSight**

We've got a database with some data in it. Now let's connect it up to QuickSight so we can start seeing some visual charts of our data.
-  Navigate back into your RDS instance from the **RDS console** in AWS.
-  Open your RDS instance.
-  Under **Connectivity & security**, select the link in **VPC security groups** to open the related security group.
-  Open the security group by selecting the **Security group ID**.
-  Select **Edit inbound rules** to add a new rule with the following details:
    -  Type: **All Traffic**
    -  Source: **Custom**, then `0.0.0.0/0` in the next box
 
![image alt](Databases-24)

This inbound rule let's any external service access our RDS instance. `0.0.0.0/0` means "**all addresses**" so applies to anyone trying to access our RDS instance.

-  Select **Save rules**
-  Navigate to QuickSight by searching `Amazon QuickSight` in the search bar at the top of your AWS console.
-  If this is your first time using QuickSight, follow the sign-up flow;
    -  PLEASE make sure to **untick** the offer to upgrade with the optional add-on **Add Paginated Reports**. No getting charged today!
    -  Make sure you select the same Region as the one you've been doing this project in.
-  Once you're in QuickSight, select **Datasets** from the left menu.
-  In the top right of the screen, select **New dataset**

![image alt](Databases-25)

-  WOW! Look at all those options! So many things we could build in the future.
-  For now, let's stick to the mission; select **RDS**
-  Fill out the following values:
    -  **Data source name**: `RDS_Public_Database`
    -  **Instance ID**: select your database from the drop-down
    -  **Connection type**: `Public network`
    -  **Database name**: `QuickSightDatabase`
    -  **Username**: `admin` (or the username you created when you set up your RDS instance)
    -  **Password**: enter in your RDS instance password

**It's critical that our Database name matches exactly to the name of our database.**

-  Select **Validate connection**
-  Volia! It works!

![image alt](Databases-26)

**Step - 6 : Secure QuickSight**

This solution kind of sucks because **our RDS instance is publicly available**. This is bad because it means anyone can access it, making us vulnerable from hackers and malicious people trying to get our data. We definitely need to make this more secure. Ideally, we can adjust it so only QuickSight can access our RDS instance. No one else!

We can put QuickSight in a Security Group and our RDS in a Security Group, then let our RDS Security accept requests from the QuickSight security group only. This is going to help us to connect QuickSight to our RDS instance securly, without RDS being publicly accessible.

-  Close your current QuickSight data source pop up and return to your AWS console.
-  Search for `security groups` in the search bar in your AWS console
-  Select **Create security group**
-  For **Security group name** enter `QuickSight_SecGp`
-  For **Description** enter `Security Group that contains QuickSight`
-  Select the **default VPC** as your VPC option. We haven't created our own VPC so the default one is what our RDS and QuickSight will be living in.

![image alt](Databases-27)

-  Leave the inbound and outbound rules as they are.
-  Select **Create security group**
-  Take note of our new **QuickSight_SecGp ID** we'll need it so we can attach our new security group to QuickSight.

![image alt](Databases-28)

We have an empty QuickSight Security Group living inside the same VPC as our RDS instance. Now that we've created our Security Group we need to actually associate it with to QuickSight.

-  Navigate to `QuickSight` using the search bar.
-  Select the profile icon in the top right and select **Manage QuickSight** from the dropdown.
-  Select **Manage VPC connections**
-  Select the **Add VPC connection** button
-  For **VPC connection name**, enter `RDS_VPC`
-  Select the VPC from the dropdown that matches the one we added to our QuickSight security group. If you only see one VPC in the dropdown, that'll be it!
-  For **Execution role**, select **aws-quicksight-service-role-v0**.

Execution role is the IAM role which will give us the permissions to create this VPC connection.

-  Select the default dropdown options for the **Subnet ID** fields
-  For **Security Group IDs** select the same ID as your **QuickSight_SecGp**.

![image alt](Databases-29)

-  Select **Add** to confirm your VPC

![image alt](Databases-30)

-  Whoops! An error!
-  If we read our error we can see it tells us what to do:
    -  "The role provided is unauthorized to perform the required actions".
    -  Which role? Aha! It must be the **aws-quicksight-service-role-v0** role we selected as the **Execution role**. We need to edit it to have the proper permissions to execute this task.
 
**Update role**
-  Open a new tab to your AWS console and search for `IAM`
-  In the left menu, select **Roles**
-  In the Roles search bar, search for our role; `aws-quicksight-service-role-v0`
-  Click into the **aws-quicksight-service-role-v0**
-  In the **Permissions policies** section, select **Add permissions**
-  From the dropdown, select **Create inline policy**
-  Select the **JSON** option as a policy editor
-  Paste in the following IAM policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeVpcs",
        "ec2:DescribeSubnets",
        "ec2:DescribeSecurityGroups",
        "ec2:DescribeNetworkInterfaces",
        "ec2:CreateNetworkInterface",
        "ec2:DeleteNetworkInterface",
        "ec2:ModifyNetworkInterfaceAttribute",
        "iam:PassRole"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "iam:PassRole"
      ],
      "Resource": "*"
    }
  ]
}
```

**How did you know what policies to put in here?** One of the fastest ways to find out which policies to add is to use [ChatGPT](https://chatgpt.com/) and explain the situation.
A prompt along the lines of :

" I'm creating a VPC Connection in QuickSight and my Execution Role doesn't have the right permissions to perform this task. Please create the necessary JSON policy to attach to my Execution Role in IAM " 

should do the trick!

-  Select **Next**
-  For **Policy name**, enter `QuickSightAllowVPC`
-  Select **Create policy**

**Let's try again!**
-  Return to your QuickSight VPC connection.
-  Select **Add** one more time.
-  WOOHOO! It worked! We now have attached QuickSight to our new security group.

**Step - 7 : Secure RDS**

Alright - so our QuickSight service is now safely in our own security group. Let's do the same for our RDS instance and stop using the default security group.

-  Open the Amazon RDS console, in the left-hand navigation, select **Databases**.
-  Then, choose **QuickSightDatabase**
-  On the **QuickSightDatabase** page, choose **Modify**.
-  On the **ModifyDB instance: QuickSightDatabase** page, in the Connectivity section, choose **Additional Configuration**.
-  Select **Not publicly accessible**, and choose **Continue**.
-  Select **Apply immediately**.
-  Select **Modify DB instance**.

**Create a security group for RDS**
-  Search for `security groups` in the search bar in your AWS console
-  Select **Create security group**
-  For **Security group name** enter `RDS_SecGp`
-  For **Description** enter `Security Group that contains RDS`
-  Select the **default VPC** as your VPC option. Our RDS security group will live in the same VPC as our QuickSight security group. Perfect!
-  Add inbound rules to allow QuickSight to query our RDS instance;
    -  Under **Inbound rules**, select **Add rule**
    -  For **Type** select **MYSQL/Aurora**
    -  For **Source** select **Custom** and then search for the security group ID of your **QuickSight_SecGp**
-  Select **Create security group**

![image alt](Databases-31)

Now let's attach it to our RDS instance.
-  Return to your RDS instance and select **Modify**
-  Under the **Connectivity** section, look for **Security group**
-  Select your newly created **RDS_SecGp** and remove any existing one.

![image alt](Databases-32)

-  Select **Continue**
-  Select **Apply immediately**
-  Select **Modify DB instance**

We've created our own RDS security group, added inbound rules to allow QuickSight in, and attached it to our RDS instance.

**Step - 8 : Reconnect RDS with QuickSight**

Now that everything is wired up, secure, and ready to go, we can jump back into QuickSight and add our new secure data source.
-  Return to the **QuickSight** console (you may need to click the QuickSight logo in the top left to leave the QuickSight VPC settings).
-  Select **Datasets** from the left menu
    -  Select **New dataset**
    -  Select **RDS**
    -  Fill out the following values:
        -  **Data source name**: `RDS_VPC_Database`
        -  **Instance ID**: select your database from the drop-down
        -  **Connection type**: `RDS_VPC` (not 'Public network' - yay!)
        -  **Database name**: `QuickSightDatabase`
        -  **Username**: `admin` (or the username you created when you set up your RDS instance)
        -  **Password**: enter in your RDS instance password
-  Select **Validate connection**
-  Nice work - a secure connection!

![image alt](Databases-33)

-  Select **Create data source**
-  Select **newhire** as the table to visualize.
-  Click **Select**
-  Select **Directly query your data** and then **Visualize**.

![image alt](Databases-34)

**Step - 9 : Make some charts!**

-  Cancel any pop-up that shows and select the **Vertical Bar Chart** from the left hand **Visuals** section.
-  Drag **jobs** into the **x-axis**.
-  Drag **salary** into the **Value** measure.

![image alt](Databases-35)

-  Continue adding any other charts you feel like!
-  When you're ready, select **Publish** in the top right
-  Name your dashboard `RDS New Hire Data`
-  Select **Publish Dashboard**

**Step - 10 : Delete Your Resources**

Make sure you delete all your resources to avoid getting charged. Deleting resources that are not actively being used stops you getting charged and is a best practice. Not deleting your resources will result in charges to your account.

**Delete the dashboard**
-  On the QuickSight home page, choose **Dashboards** from the left menu.
-  Choose the details icon (...) of the dashboard you published, and choose **Delete**.
-  When prompted to confirm, choose **Delete**.

**Delete the analysis**
-  In the left menu, select **Analyses**.
-  Choose the details icon (...) of the `newhire` analysis and choose **Delete**.
-  When prompted to confirm, choose **Delete**.

**Delete the data**
-  In the left menu, select **Datasets**.
-  Choose the `newhire` data set.
-  Choose **Delete dataset**.
-  When prompted to confirm, choose **Delete**.

**Delete the database instance**
-  Open the Amazon RDS console, select **Databases**.
-  Choose `QuickSightDatabase`. Then, from the Actions drop-down menu, choose **Delete**.
-  When deleting your database, make sure to uncheck **Create final snapshot** and uncheck **Retain automated backups** so you don't get charged.
-  Check the box **"I acknowledge that upon instance deletion, automated backups, including system snapshots and point-in-time recovery, will no longer be available."**

**Delete your security groups**
-  Open the Amazon EC2 console, select **Security Groups**
-  Select the **two** security groups you created. Click **Actions** at the top, then **Delete security groups**.

**Delete your IAM policy**
-  Open the AWS IAM console, select **Roles**.
-  Select **aws-quicksight-service-role-v0**.
-  Under **Permissions policies**, select **QuickSightAllowVPC** then **Remove**.

**Terminate your QuickSight account**
-  After returning to the home page, select the user icon on the top right corner.
-  Select **Manage QuickSight**.
-  Select **Manage VPC Connections**.
-  Select **Delete VPC Connections**.
-  Under **Account termination**, select **Manage**
-  Select `Account Settings` from the left hand navigation panel, and then **Manage** at the bottom of the page.

![image alt](Databases-36)

-  **Toggle off** account termination, and then type **confirm** before clicking **Delete account**.

![image alt](Databases-37)

**Optional** : Uninstall the MySQL Workbench on your local machine.

---
##  Aurora Database with EC2

In this project, we're creating an Amazon Aurora database to store and display data for our very own web application! We won't create the web app itself just yet. But, this project is going to teach us how to connect your Aurora database to the web server (EC2 instance) hosting that web app!

**Step - 1 : Create an Aurora MySQL Database**

**Aurora** is a type of relational database that we can use in AWS. Most of the time we use databases to hold information we want to store, update, and use. A relational database is a type of database that organizes data into tables, which are collections of rows and columns. Kind of like a spreadhsheet! We call it "relational" because the rows relate to the columns and vice-versa. When a database is relational we can query it using a special language called SQL (Structured Query Language).

-  Take note of your **Region** in the top right of your AWS Console.
-  Head to **RDS console** - search for `rds` in search bar at the top of the screen.
-  Notice that even if you search for `Aurora`, the same result shows up!
-  In the left navigation bar, select **Databases**.
-  In the Database section, select **Create Database**.
-  On the Create database page, choose **Standard Create**.
-  In the Configuration section, make the following changes:
    -  For **Engine type**, choose **Aurora (MySQL Compatible)**.

**Engine type** is like the core software that powers our database. Imagine it as the operating system of a computer, but for databases.

AWS Aurora is a type of relational database. As you can tell from the first few steps of creating a relational database, there are plenty of options to choose from! We'd use AWS Aurora if we needed something large-scale, with peak performance and uptime. This is because Aurora databases use **clusters**. Ordinary relational databases, like MySQL and Oracle are more generic and cost-effective. They suit smaller databases and less demanding workloads.

-  For **Engine Version**, choose **Aurora MySQL 3.05.2 (compatible with MySQL 8.0.32) - default for major version 8.0**
-  For **Templates**, choose **Dev/Test**

**Templates** are pre-set settings that help you quickly set up your database environment according to your needs. It's basically AWS helping you make selections for the rest of this set-up page! The Dev/Test template is designed for development and testing environments, helping you pick lower cost options.

![image alt](Databases-38)

-  In the **Settings** section, set these values:
    -  **DB cluster identifier**: `nextwork-db-cluster`
    -  **Master username**: `admin`
    -  For **Credentials management** select **Self managed**.
    -  **Master password**: Type a password (eg. `n3xtw0rk`)
    -  **Confirm password**: Retype the password.
    -  Make sure you save your database login details somewhere safe! You'll need them later on.

![image alt](Databases-39)

-  Leave the **Cluster storage configuration** settings as default.
-  In the **Instance configuration** section, set these values:
    - **Burstable classes (includes t classes)**
    -  **db.t3.medium**

**Instance configuration** is where we can select the type of virtual computer that our Aurora database will run on.

**Instance type** that we chose determines how powerful, and how much memory, our virtual computer has. The more powerful it is, the better it will perform - but also the more expensive the price!

**Burstable classes (includes t classess)** are cost-effective types of database instances that are best when you have a consistent baseline level of traffic with occassional, random spikes in demand...a sudden "burst" of traffic. By choosing burstable classes, our database can perform well under high traffic, but also save us costs when things are quiet.

![image alt](Databases-40)

-  In the **Availability and durability** section, use the default values. We'll jump into what this means later!
-  In the **Connectivity** section, the first thing it asks us is whether we need to connect to an EC2 instance...
-  For **Compute resource**, choose **Connect to an EC2 compute resource**.
-  Now select the drop down for **EC2 instance**, and choose.... hold on a minute! We haven't even created an EC2 instance. Let's do that first, then come back to this.

**Step - 2 : Launch an EC2 instance**

In this step, we're going to create an Amazon EC2 instance using our default VPC and default subnets.
-  Open a new tab in your web browser and go to your **AWS console** (this means we can keep our Aurora database set-up in progress!)
-  In the upper-right corner of the AWS Management Console, make sure your AWS Region is the same as that in your Aurora database creation.
-  In the AWS console search bar, search for **EC2**
-  Select **Instances** in the left hand menu and choose **Launch instances**
-  Choose the following settings in the **Launch an instance** page.
    -  Under **Name and tags**, for **Name**, enter `nextwork-ec2-instance-web-server`
    -  Under **Application and OS Images (Amazon Machine Image)**, choose **Amazon Linux**.
    -  Choose the **Amazon Linux 2023 AMI**.
    -  Keep the defaults for the other choices.

 ![image alt](Databases-41)

-  Under **Instance type**, choose **t2.micro**.
-  Under **Key pair (login)**, choose a **Create new key pair**
-  For your **Key pair name**, enter NextWorkAuroraApp
-  Leave your Key pair type as RSA
-  Leave your Private key file format as .pem since we're using SSH later on to access our EC2 instance.

