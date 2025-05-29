# AWS-X-Databases
##  Project Overview

---
##  Table Of Content
-  [Visualize a Relational Database](#visualize-a-relational-database)
-  [Aurora Database with EC2](#aurora-database-with-ec2)
-  [Connect a Web App with Aurora](#connect-a-web-app-with-aurora)
-  [Load Data into DynamoDB](#load-data-into-dynamoDB)
-  [Query Data with DynamoDB](#query-data-with-dynamoDB)

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

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-1.png)

-  For the console password, choose **Custom password**.
-  Type in a password that you will be able to remember/access in the future.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-2.png)

-  Deselect the checkbox for **Users must create a new password at next sign-in - Recommended**.
-  Choose **Next**.
-  In the permissions set up page, choose **Attach policies directly**.
-  From the list of **Permissions policies**, select **AdministratorAccess**.
-  Choose **Next**.
-  Choose **Create user**.
-  Voilà - you've just created your new user! **Stay on this page**.
-  Choose **Download .csv file**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-3.png)

-  Copy the **Console sign-in URL**.
-  **Now you're ready to start using your IAM user**.
-  Log out of your root user's AWS Account.
-  Paste and go to your copied console sign-in URL.
-  Open your downloaded .csv file containing your user's access instructions.
-  Log in using your IAM user's username and password in the .csv file.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-4.png)

**Step - 2 : Create a Relational Database**

A relational database is a type of database that organizes data into tables, which are collections of rows and columns. Kind of like a spreadsheet! We call it "relational" because the rows relate to the columns and vice-versa. When a database is relational we can query it using a special language called **SQL** (Structured Query Language)

Strangely enough, there isn't really such a thing as a "normal" database. We have relational databases and non-relational databases (also known as NoSQL).

-  Take note of your **Region** in the top right of your AWS Console.
-  It doesn't matter exactly which Region you have, as long as you know it.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-5.png)

-  Head to your **Aurora and RDS console** - search for `rds` in search bar at the top of the screen.
-  In the left navigation bar, select **Databases**.
-  In the Database section, select **Create Database**.
-  On the Create database page, choose **Easy Create**.
-  In the Configuration section, make the following changes:
    -  For **Engine type**, choose **MySQL**.
    -  For **DB instance size**, choose **Free tier**.
 
**MySQL** is a relational database management system (RDBMS) that uses SQL as the language for database interaction. If databases were libraries, think of RDBMS as the librarians who know exactly where each book is located, how to organize new books that arrive, and how to maintain the library's order. MySQL is software used to create, manage, and interact with relational databases but is only one of many options for using SQL to manage relational data. Other options include PostgreSQL, MariaDB, or SQLite.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-6.png)

-  For **DB instance identifier**, type `QuickSightDatabase`.
-  For **Master username**, enter `admin`.
-  In the **Credentials management** section, select **Self managed**.
-  For **Master password**, type a unique password, and confirm password.
-  Make sure you save your database login details somewhere safe! You'll need them later on.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-7.png)

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

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-8.png)

-  Choose **Continue**.
-  Choose **Apply immediately**.
-  Select **Modify DB instance**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-9.png)

This can take some time, so don't be concerned if it takes five or so minutes for the modifcations to come through!

Next we want to modify the security group attached to your RDS instance so that your local machine can access your RDS instance.
-  In Amazon RDS in your AWS console, go to the left-hand navigation, and select **Databases**.
-  Then, choose **quicksightdatabase**.
-  On the **quicksightdatabase** page, in the Connectivity & security section, choose the **VPC security groups** link.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-10.png)

-  On the **Security groups** page, choose the **Security group ID**.
-  On the sg-default page, in the Inbound rules section, choose **Edit inbound rules**.
-  On the **Edit inbound rules** page, in the Inbound rules section, choose **Add rule**, and make the following changes:
    -  For **Type**, choose **All TCP** from the drop-down list.
    -  For **Source**, choose **My IP**.
 
![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-11.png)

-  Then, choose **Save rules**.

**What did we just change? And why?** Every RDS instance we create has a default security group attached to it. This security group controls the traffic going into and out of the database.

We need MySQL Workbench to access our database, so we need to change the inbound traffic rules in the security group. By saying that we only allow traffic from our current IP address, we are only allowing our machine to connect to the database - this will enable MySQL Workbench to connect when we use it from our local computer, which makes it good for security!

-  Verify that MySQL Workbench has downloaded successfully. Then, install and open the software.
-  Select **Databases** in the top menu bar, then **Manage Connections...**

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-12.png)

-  Select **New** in the bottom left.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-13.png)

-  Enter the following details:
    -  For **Connection Name**, paste `AWS-QuickSightDatabase`
    -  For **Hostname**, return to your RDS database page for your QuickSightDatabase.
    -  Look under **Connectivity and security** to find the **Endpoint**. Example: `QuickSightDatabase.abc.us-east-1.rds.amazonaws.com,1433`
    -  Copy this into **Hostname** in your MySQL Workbench.
    -  Copy the **Port** from the same place in AWS to the **Port** field in MySQL Workbench.
 
![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-14.png)

-  For **Username**, type the username you entered when creating the **QuickSightDatabase** (probably `admin` unless you changed it).
-  For **Password**, select **Store in Keychain** ... then enter in your database password.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-15.png)

-  Then, choose **Test Connection**.
-  You should get a pop-up that say's **Successfully made the MySQL connection**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-16.png)

**Step - 4 : Create Database Tables and Load Data**

Now that we've got our MySQL Workbench connected with our RDS instance, we can actually start to add our own tables and data.
-  Close any pop-ups that are still open in MySQL Workbench.
-  Select your newly created connection to open the MySQL Workbench Query Editor.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-17.png)

-  Select **Schemas** as the tab in the top left, next to **Administration**.

A database **schema** is like a blueprint or structure of how the data is organized in a database. It sets up how the database is structured, including the tables, what fields should go in those tables, the relationships between tables, and other advaced components of a database. Think of a schema as your design plan for how your data is stored and organized!

-  Right click on the blank space under the **Schemas** menu.
-  Select **Create Schema**.
-  Name your schema `QuickSightDatabase`.
-  Leave everything else as is, and select **Apply**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-18.png)

-  Review the SQL Script pop-up; notice that it's showing you how the command looks in SQL.
-  Select **Apply**.
-  Once it's finished running, select **Close**.
-  Select the tab marked **Query 1** near the top of the screen.

**Can't find the 'Query 1' tab?** Try selecting the first file icon in the top tool bar. When you hover over the icon it says "Create a new SQL tab for executing queries". Click this icon to open a new query script! 

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-19.png)

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

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-20.png)

-  To see the results from our query, delete the current query and replace it with the following:

```sql
SELECT * FROM newhire;
```

-  Run this new query to see the empty table you just created!

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-21.png)

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

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-22.png)

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

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-23.png)

**Step - 5 : Connect RDS to QuickSight**

We've got a database with some data in it. Now let's connect it up to QuickSight so we can start seeing some visual charts of our data.
-  Navigate back into your RDS instance from the **RDS console** in AWS.
-  Open your RDS instance.
-  Under **Connectivity & security**, select the link in **VPC security groups** to open the related security group.
-  Open the security group by selecting the **Security group ID**.
-  Select **Edit inbound rules** to add a new rule with the following details:
    -  Type: **All Traffic**
    -  Source: **Custom**, then `0.0.0.0/0` in the next box
 
![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-24.png)

This inbound rule let's any external service access our RDS instance. `0.0.0.0/0` means "**all addresses**" so applies to anyone trying to access our RDS instance.

-  Select **Save rules**
-  Navigate to QuickSight by searching `Amazon QuickSight` in the search bar at the top of your AWS console.
-  If this is your first time using QuickSight, follow the sign-up flow;
    -  PLEASE make sure to **untick** the offer to upgrade with the optional add-on **Add Paginated Reports**. No getting charged today!
    -  Make sure you select the same Region as the one you've been doing this project in.
-  Once you're in QuickSight, select **Datasets** from the left menu.
-  In the top right of the screen, select **New dataset**

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-25.png)

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

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-26.png)

**Step - 6 : Secure QuickSight**

This solution kind of sucks because **our RDS instance is publicly available**. This is bad because it means anyone can access it, making us vulnerable from hackers and malicious people trying to get our data. We definitely need to make this more secure. Ideally, we can adjust it so only QuickSight can access our RDS instance. No one else!

We can put QuickSight in a Security Group and our RDS in a Security Group, then let our RDS Security accept requests from the QuickSight security group only. This is going to help us to connect QuickSight to our RDS instance securly, without RDS being publicly accessible.

-  Close your current QuickSight data source pop up and return to your AWS console.
-  Search for `security groups` in the search bar in your AWS console
-  Select **Create security group**
-  For **Security group name** enter `QuickSight_SecGp`
-  For **Description** enter `Security Group that contains QuickSight`
-  Select the **default VPC** as your VPC option. We haven't created our own VPC so the default one is what our RDS and QuickSight will be living in.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-27.png)

-  Leave the inbound and outbound rules as they are.
-  Select **Create security group**
-  Take note of our new **QuickSight_SecGp ID** we'll need it so we can attach our new security group to QuickSight.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-28.png)

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

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-29.png)

-  Select **Add** to confirm your VPC

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-30.png)

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

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-31.png)

Now let's attach it to our RDS instance.
-  Return to your RDS instance and select **Modify**
-  Under the **Connectivity** section, look for **Security group**
-  Select your newly created **RDS_SecGp** and remove any existing one.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-32.png)

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

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-33.png)

-  Select **Create data source**
-  Select **newhire** as the table to visualize.
-  Click **Select**
-  Select **Directly query your data** and then **Visualize**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-34.png)

**Step - 9 : Make some charts!**

-  Cancel any pop-up that shows and select the **Vertical Bar Chart** from the left hand **Visuals** section.
-  Drag **jobs** into the **x-axis**.
-  Drag **salary** into the **Value** measure.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-35.png)

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

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-36.png)

-  **Toggle off** account termination, and then type **confirm** before clicking **Delete account**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-37.png)

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

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-38.png)

-  In the **Settings** section, set these values:
    -  **DB cluster identifier**: `nextwork-db-cluster`
    -  **Master username**: `admin`
    -  For **Credentials management** select **Self managed**.
    -  **Master password**: Type a password (eg. `n3xtw0rk`)
    -  **Confirm password**: Retype the password.
    -  Make sure you save your database login details somewhere safe! You'll need them later on.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-39.png)

-  Leave the **Cluster storage configuration** settings as default.
-  In the **Instance configuration** section, set these values:
    - **Burstable classes (includes t classes)**
    -  **db.t3.medium**

**Instance configuration** is where we can select the type of virtual computer that our Aurora database will run on.

**Instance type** that we chose determines how powerful, and how much memory, our virtual computer has. The more powerful it is, the better it will perform - but also the more expensive the price!

**Burstable classes (includes t classess)** are cost-effective types of database instances that are best when you have a consistent baseline level of traffic with occassional, random spikes in demand...a sudden "burst" of traffic. By choosing burstable classes, our database can perform well under high traffic, but also save us costs when things are quiet.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-40.png)

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

 ![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-41.png)

-  Under **Instance type**, choose **t2.micro**.
-  Under **Key pair (login)**, choose a **Create new key pair**
-  For your **Key pair name**, enter `NextWorkAuroraApp`
-  Leave your **Key pair type** as **RSA**
-  Leave your **Private key file format** as **.pem** since we're using SSH later on to access our EC2 instance.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-42.png)

-    Select **Create key pair**

A new file will automatically download to your computer - This is your new key pair. We'll need it for later in the project to access our EC2 instance. Notice that it's a .pem file.

-    Back in our EC2 creation, under **Network settings**, set these values and keep the other values as their defaults:
        -    For **Allow SSH traffic from**, choose your IP address if it's correct. Otherwise select **Anywhere**.
-    Check the boxes for **Allow HTTP traffic from the internet**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-43.png)

-    Leave the default values for the remaining sections.
-    Review the **Summary panel** of your instance configuration
-    When you're ready, choose **Launch instance**.
-    Navigate back to your list of EC2 instances, and then select the checkbox next to your new instance.
-    In the **Details** tab, note the following important details:
        -    In **Instance summary**, note the **Public IPv4 DNS**.
        -    Note the value for **Key pair name**.
 
![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-44.png)

-    Wait until **Instance state** for your instance is **Running** before continuing.

**Step - 3 : Finish creating your Aurora Database**

Now that we've got our EC2 instance ready to go, we can go back to creating our Aurora database. This time, we'll connect it to our new EC2 instance.

-    Navigate back to your open tab that you were creating our Aurora database in.
-    We were up to the **Connectivity** section. But this time we have one big advantage...we have an EC2 instance!
        -    For **Compute resource**, choose **Connect to an EC2 compute resource**.
        -    Now select the drop down for **EC2 instance**, and choose **nextwork-ec2-instance-web-server**.
        -    **NOTE**: We may need to select the refresh button to the right of the EC2 instance drop-down.
 
![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-45.png)

-    Scroll down and open the **Additional configuration** section.
-    Enter `sample` for **Initial database name**.
-    Keep the default settings for the other options.
-    Select **Create database**.

**Did you notice some scary looking monthly costs?** Don't worry! As long as we delete our database as soon as we've finished with this project, we'll be absolutely fine.

-    Close any pop-ups that appear.
-    Your new DB cluster will show in the **Databases** list with the status **Creating**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-46.png)

-    Do you notice that your database has the name nextwork-db-cluster and that's there's two of them? What's with that?

Remember how we said that Aurora is really good for the big jobs? The reason for this is **clusters**. A database cluster in Aurora is a group of database copies that work together so your data is always available.
Each cluster consists of a primary instance (where all write operations occur) and multiple read replicas as back-ups. If your database's primary instance fails, one of the replicas can be promoted to primary automatically.

-    Wait for the **Status** of your new DB cluster to show as **Available**.
-    Select the **DB identifier** of your top database to take a look at the details.
-    Notice that there are **two** Endpoints in our Database. Cool! This is our **cluster** in action.

Our **writer** database instance is our primary instance that handles all the "write" operations like INSERT, UPDATE, and DELETE.
Our **reader**database instance is our backup instance that can do very basic operations like SELECT. This is used to get data, but not to add or change data.

**Why separate read and write?** We only want one writer instance at a time so that things stay focused and controlled...but we want multiple reader instances so that we can share the workload for read requests as our database grows and have a backup if our writer instance fails.

**What is an endpoint?** In general, endpoints are like contact points where data flows in and out. A super popular example is a website URL! When you enter a website's address (like nextwork.org), your browser uses that endpoint to receive data and load the page. For databases, an endpoint is how your app finds a database to ask for data or update it.

-    We've created a new relational database and have an EC2 instance waiting for our beautiful new web app. Can't wait to build that web app and connect it to our database in the next project!

---
##    Connect a Web App with Aurora

There are countless web apps on the Internet today, and engineers use databases all the time to store the web app's data e.g. login credentials, e-commerce product information, movie reviews... Let's build a mini version of this pattern, by setting up a web app from scratch and connecting it with a relational database (Amazon Aurora) to store things that users input.

**Step - 1 : Create your web app**

Open your local Terminal
-    On a **Mac**:
        -    Press **Cmd + Space** to open Spotlight.
        -    Type `Terminal` and press **Enter**.
-    On a **Windows/Linux**:
        -    Press **Windows + R** to open the Run dialog.
        -    Type `cmd` or `powershell` and press **Enter**.
 
![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-47.png)

Connect to your EC2 instance
-    We need to access our **.pem** file in order to login successfully to our EC2 instance - remember, the **.pem** file is like our keys to our EC2 instance!
-    Find **.pem** file on our local computer and put it in a new folder on Desktop labelled `nextwork`
-    Now we need to navigate to that folder from our terminal, so we can use it.
-    Run the command `ls` in terminal - this shows all the folders that we can see from your current terminal position.
-    If you can see the **desktop** folder when you run `ls` then you're in the right place.
        -    If you can't see the **desktop** folder, then use the following commands to navigate up and down your folders until you can see **desktop**... 
        -    To go back one folder run the command `cd ../`.
        -    To go into a folder, run the command `cd folder-name` and replace **folder-name** with the name of the folder you'd like to enter.
-    Once you can see your **Desktop** folder, navigate into that folder by running `cd Desktop`.
-    Then navigate into your **nextwork** folder by running `cd nextwork`.
-    Run `ls` to make sure your **.pem** file is there!

Great! We have found our "keys" to get into our EC2 instance. Now we just need the "address" of our EC2 instance and then we get inside.
-    Back in your AWS console, navigate to the details page of your **nextwork-ec2-instance-web-server** EC2 instance.
-    Copy your **Public IPv4 DNS** address.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-48.png)

-    Back in your terminal run the following command:

```bash
ssh -i YOUR_PEM_FILE_NAME ec2-user@YOUR_EC2_ADDRESS
```

-    Make sure you replace **'YOUR_PEM_FILE_NAME'** with the name of your .pem file, and **'YOUR_EC2_ADDRESS'** with the copied EC2 address.
-    Did you get an error telling you permissions denied?
-    That's because you need the right permissions to access your **.pem** file. 
-    Try running this command if you're on **Mac** or **Linux**:

```bash
chmod 400 YOUR_PEM_FILE_NAME
```

-    Make sure you replace **'YOUR_PEM_FILE_NAME'** with the name of your .pem file
-    Once you've given your computer access to your **.pem** file, run the command to connect to our EC2 instance again:

```bash
ssh -i YOUR_PEM_FILE_NAME ec2-user@YOUR_EC2_ADDRESS
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-49.png)

-    Now remember that our EC2 instance is just another computer. First thing we need to do is make sure all the software on it is up to date!
-    Run the following command:

```bash
sudo dnf update -y
```

-    After the updates complete, we're going to install a whole bunch of stuff needed to run your web app. In particular...
        -    an **Apache web server** - the most widely used web server in the world. A web server is a software that gets your content (e.g. web pages) to users via the internet.
        -    **PHP** - a programming language used for writing beautiful app pages.
        -    **php-mysqli** - a PHP library (i.e. a collection of pre-written code to help you save time) for establishing a MySQL connection to your database.
        -    **MariaDB** - a relational database management system. Our Aurora database knows how to manage its data (e.g. how to add a new row or retrieve data), but our web server doesn't know how to send instructions to and understand the responses from our Aurora database. Our web server would need MySQL-compatible client libraries i.e. software designed for servers to communicate with a MySQL database. Installing MariaDB in our EC2 instance is one of the many ways to install these client libraries so it can interact with Aurora.
 
```bash
sudo dnf install -y httpd php php-mysqli mariadb105
```

-    All systems go! Let's start the most basic version of our web app with the following command:

```bash
sudo systemctl start httpd
```

-    Test that your web app is running by entering your **IPv4 DNS** address into your browser.
        -    Eg. `http://ec2-42-8-168-21.us-west-1.compute.amazonaws.com` - just make sure it's actually our one!
        -    Make sure to change https to http - no 's'!
 
![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-50.png)

**Step - 2 : Make your Web App Cool**

We've got a working database, EC2 instance, and now a basic web app running. But we still haven't actually connected our web app with our database. Wouldn't it be cool if we could update and see the changes of our database from our web app?

Connect your EC2 instance to your Database
-    While still connected to your EC2 instance, navigate to the **www** folder (this is where we store all our files for our web app!).

```bash
cd /var/www
```

-    Create a new sub-folder named **inc**.

```bash
mkdir inc
```

-    Whoops! Did you get another **permission denied** error?
-    Let's find out what's going on here. Who actually has permissions in this folder? Run the following command to find out:

```bash
ls -ld
```

Command **ls** means list, and **-ld** means "list the details". Does the details of our folder look like this?

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-51.png)

This means that the **root** user is the owner of this folder, and **only** the owner can edit this folder. When we access an EC2 instance using a key pair, we're by default logging in as an admin user (called ec2-user) which is not the root user! Let's change the owner of this folder so we (ec2-user) can edit it.

-    Let's navigate back to where we were at the start:

```bash
cd ../../
```

-    Run the following command:

```bash
sudo chown ec2-user:ec2-user /var/www
```

-    Now let's see the details of that folder again...

```bash
ls -ld var/www
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-52.png)

Let's try it again.
-    Navigate to your **www** folder

```bash
cd /var/www
```

-    Create a new sub-folder named **inc**.

```bash
mkdir inc
```

-    No errors! Navigate into your new **inc** folder.

```bash
cd inc
```

-    Create a new file in the **inc** directory named **dbinfo.inc**

```bash
>dbinfo.inc
```

-  Now we have a blank file. We can edit our new file by calling **nano**.

```bash
nano dbinfo.inc
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-53.png)

**Nano** is a way we can edit files in the terminal. We've just opened our blank **dbinfo.inc** file, and now we're going to add some code that connects it to our AWS database.

-    **dbinfo.inc** is a settings file that stores the connection details our EC2 instance will need to connect to our Aurora database. To complete this file, we need our Aurora database **endpoint**.
-    Navigate to our Aurora database details in AWS and copy the **Endpoint** of our **Writer** instance.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-54.png)

-    Copy and paste the following code to connect our EC2 instance to our Aurora database to the **dbinfo.inc** file.
-    Make sure to replace **YOUR_ENDPOINT** with our actual Aurora Endpoint!
-    Make sure to update **DB_PASSWORD** too.

```bash
<?php

define('DB_SERVER', 'YOUR_ENDPOINT');
define('DB_USERNAME', 'admin');
define('DB_PASSWORD', 'password');
define('DB_DATABASE', 'sample');
?>
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-55.png)

-    Save and close the **dbinfo.inc** file by using **Ctrl+S** to save and then **Ctrl+X** to exit.

**Now that our database is all connected, let's upgrade our web app page.**
-    Navigate to your **html** folder:

```bash
cd /var/www/html
```

-    Create a new file in the **html** directory named **SamplePage.php**, and then edit the file by calling **nano**

```bash
>SamplePage.php
nano SamplePage.php
```

-    Copy and paste the following script into your **SamplePage.php** file:

```php
<?php include "../inc/dbinfo.inc"; ?>
<html>
<body>
<h1>Sample page</h1>
<?php

  /* Connect to MySQL and select the database. */
  $connection = mysqli_connect(DB_SERVER, DB_USERNAME, DB_PASSWORD);

  if (mysqli_connect_errno()) echo "Failed to connect to MySQL: " . mysqli_connect_error();

  $database = mysqli_select_db($connection, DB_DATABASE);

  /* Ensure that the EMPLOYEES table exists. */
  VerifyEmployeesTable($connection, DB_DATABASE);

  /* If input fields are populated, add a row to the EMPLOYEES table. */
  $employee_name = htmlentities($_POST['NAME']);
  $employee_address = htmlentities($_POST['ADDRESS']);

  if (strlen($employee_name) || strlen($employee_address)) {
    AddEmployee($connection, $employee_name, $employee_address);
  }
?>

<!-- Input form -->
<form action="<?PHP echo $_SERVER['SCRIPT_NAME'] ?>" method="POST">
  <table border="0">
    <tr>
      <td>NAME</td>
      <td>ADDRESS</td>
    </tr>
    <tr>
      <td>
        <input type="text" name="NAME" maxlength="45" size="30" />
      </td>
      <td>
        <input type="text" name="ADDRESS" maxlength="90" size="60" />
      </td>
      <td>
        <input type="submit" value="Add Data" />
      </td>
    </tr>
  </table>
</form>

<!-- Display table data. -->
<table border="1" cellpadding="2" cellspacing="2">
  <tr>
    <td>ID</td>
    <td>NAME</td>
    <td>ADDRESS</td>
  </tr>

<?php

$result = mysqli_query($connection, "SELECT * FROM EMPLOYEES");

while($query_data = mysqli_fetch_row($result)) {
  echo "<tr>";
  echo "<td>",$query_data[0], "</td>",
       "<td>",$query_data[1], "</td>",
       "<td>",$query_data[2], "</td>";
  echo "</tr>";
}
?>

</table>

<!-- Clean up. -->
<?php

  mysqli_free_result($result);
  mysqli_close($connection);

?>

</body>
</html>


<?php

/* Add an employee to the table. */
function AddEmployee($connection, $name, $address) {
   $n = mysqli_real_escape_string($connection, $name);
   $a = mysqli_real_escape_string($connection, $address);

   $query = "INSERT INTO EMPLOYEES (NAME, ADDRESS) VALUES ('$n', '$a');";

   if(!mysqli_query($connection, $query)) echo("<p>Error adding employee data.</p>");
}

/* Check whether the table exists and, if not, create it. */
function VerifyEmployeesTable($connection, $dbName) {
  if(!TableExists("EMPLOYEES", $connection, $dbName))
  {
     $query = "CREATE TABLE EMPLOYEES (
         ID int(11) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
         NAME VARCHAR(45),
         ADDRESS VARCHAR(90)
       )";

     if(!mysqli_query($connection, $query)) echo("<p>Error creating table.</p>");
  }
}

/* Check for the existence of a table. */
function TableExists($tableName, $connection, $dbName) {
  $t = mysqli_real_escape_string($connection, $tableName);
  $d = mysqli_real_escape_string($connection, $dbName);

  $checktable = mysqli_query($connection,
      "SELECT TABLE_NAME FROM information_schema.TABLES WHERE TABLE_NAME = '$t' AND TABLE_SCHEMA = '$d'");

  if(mysqli_num_rows($checktable) > 0) return true;

  return false;
}
?>                        
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-56.png)

This big block of code is what makes our new web app look so cool! It's pulling in the details from our dbinfo.inc file we created earlier and using it to display up-to-date changes directly from our website.

-    Save and close the **SamplePage.php** file by using **Ctrl+S** to save and then **Ctrl+X** to exit.
-    Verify that your web server successfully connects to your DB cluster by opening a web browser and browsing to `http://EC2-instance-endpoint/SamplePage.php`. 

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-57.png)

-    In browser, where our new web app is running, add some new data.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-58.png)

-    Notice how your results show on the page? If you're wondering how that happens, go back and have a look at your **SamplePage.php** file. All the magic is there!

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-59.png)

-    To access our database, we're going to use **MySQL**.
-    Download the MySQL repository into your EC2 instance:

```bash
sudo yum install https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm -y
```

-    Install MySQL:

```bash
sudo yum install mysql-community-client -y
```

-   Connect to your Aurora MySQL Database:

```bash
mysql -h YOUR_ENDPOINT -P YOUR_PORT -u YOUR_AURORA_USERNAME -p
```

-    Make sure to replace:
        -    **YOUR_ENDPOINT** with the Endpoint from our Aurora Writer instance
        -    **YOUR_PORT** with the Port from our Aurora Writer instance; `3306`
        -    **YOUR_AURORA_USERNAME** with our Aurora username; `admin`
-    Enter in your Aurora password when prompted.
-    Run `SHOW DATABASES`;
        -    This shows us the databases that are in our MYSQL server.
-    Run `USE sample`;
        -    **sample** is the name we gave our first database schema.
-    Run `SHOW TABLES`;
        -    This shows us the tables in our **sample** schema.
        -    If wondering where **Employees** came from, check our **SamplePage.php** file. It's all there! (Hint: this is also where we can change it if we're feeling creative)

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-60.png)

-    Run `DESCRIBE EMPLOYEES`;
        -    This tells you how the **Employees** table is structured.
 
![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-61.png)

-    Run `SELECT * FROM EMPLOYEES`;
        -    This shows you the actual data in your table!
 
![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-62.png)

-    Have a play around adding more data in our web app, then re-running the commands above to check the data in your database.

**Step - 3 : Delete Your Resources**

Deleting resources that are not actively being used stops us getting charged and is a best practice. Not deleting our resources will result in charges to your account.

-    Delete our database cluster: Open the Amazon RDS console, select **Databases**, and choose `nextwork-db-cluster`. Then, from the Actions drop-down menu, choose Delete.
        -    When deleting our database cluster, make sure to uncheck **Create final snapshot** and uncheck **Retain automated backups** so we don't get charged.
-    Delete your EC2 instance: Open the Amazon EC2 console, select **Instances**, and choose the instance you created. Click **Actions** at the top, then **Terminate instance**.
-    Delete your key pair: Open the Amazon EC2 console, select **Key Pairs**, and choose the key pair you created. Click **Actions** at the top, then **Delete Key Pair**.

---
##  Load Data into DynamoDB

Amazon DynamoDB is a non-relational database service. Non-relational databases use structures other than rows and columns to organise data. DynamoDB can also be described as a **NoSQL** database, or a **key-value** database. A NoSQL database means we would not use SQL to query it, while key-value is a specific way to store data that's flexible and efficient.

**Step - 1 : Create your first DynamoDB table**

-    Head to the **DynamoDB console** in our AWS Management Console.
-    From the left hand navigation panel, select **Tables**.
-    Select **Create table**.

In Amazon DynamoDB, all data is organized into tables! Unlike relational databases which use rows and columns, DynamoDB tables use items and attributes. It might sound impossible to have a table that doesn't use rows and columns, but it's true that DynamoDB tables aren't like the traditional structure! Imagine if we had a table where each row had a different number of fields, and every single cell can have a different column header. Instead of the typical relational database structure (where each row has the same columns and column headers), database tables are a lot more flexible.

-    Give your table a **Table name**: `NextWorkStudents`
-    For the **Partition key**, we'll use `StudentName`

Think of a **partition key** as the filter that DynamoDB will use to split up and find data. Partition key values don't have to be unique. For example if "Color" is a partition key, items can share partition key values like "Blue", "Green", "Red" and more. Later on, partition keys are used to efficiently find and grab items we're looking for (e.g. "find all items that have the Color "Green")! That's why every item in a DynamoDB table must have a value for the table's partition key - otherwise, DynamoDB would have no way of finding that item.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-63.png)

-    Next, under **Table settings**, select **Customize settings**.
-    Expand the **Capacity calculator** section.
-    Oooo, here's a tip on how AWS charges for DynamoDB.
-    Note: we're about to see a cost estimate, but this project and the tables we create are free!

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-64.png)

AWS cost estimates always tell us how much a resource might cost us money based on our current settings, but this estimate doesn't consider the AWS Free Tier. Right before AWS sends us a bill, AWS will check our actual use and won't charge us if we haven't gone over Free Tier limits.

When we say "1 item read/second" in DynamoDB, it means our application can retrieve one item of data from our database every second. Changing this number would change our database's performance i.e. larger datasets with lots of items could want hundreds of reads each second! Just like item reads, 1 item write/second means our app can save/update one item a second.

-    We won't change anything in the **Capacity Calculator**, but that was useful to understand how DynamoDB pricing works!
-    Under **Read/write capacity settings**, let's make sure costs are as low as possible.
-    Under **Read capacity**, turn **Auto scaling** off.

Auto scaling can automatically adjust our database's performance (i.e. how fast it can return query results) based on real-time demand. Auto scaling can help engineers reduce costs in other situations, but if it's not monitored, it also has the power to boost our table's processing power (e.g. if our table suddenly needs to update lots of data). If that ever happens, auto scaling can push our table's settings to go over Free Tier limits! We're better off making our table stick to our current settings, which are already the lowest cost options.

-    Change **provisioned capacity units** to `1`.
-    Under **Write capacity**, turn **Auto scaling** off.
-    Change **provisioned capacity units** to `1`.

-    Hmm, what are capacity units?
-    Back in our **Capacity Calculator**, notice that there's something called **Read capacity units** (RCUs) at the bottom of the panel.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-65.png)

-    In your calculator, try changing the number of **Item read/second** to `2`
-    The number of **Read capacity units** in the calculator is still `1`!
-    Now try changing the number of **Item read/second** to `3` in the calculator.
-    Aha, the number of **Read capacity units** went up to `2`!

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-66.png)

Think of read capacity units (RCUs) as a way to measure how many engines DynamoDB is using to operate. **1 read capacity unit (RCU)** = DynamoDB is using a single engine to run its read operations. This lets DynamoDB perform a max of 2 reads per second. So if we decide we actually want DynamoDB to run 3 reads per second, 1 engine won't be enough! The capacity calculator updates RCUs to 2, so now there are two engines powering DynamoDB to run read operations.

-    Now try increasing **Item write/second** from `1` to `2`
-    Ooo, the number of **Write capacity units** increases to `2` right away!

Write capacity units (WCUs) are just like read capacity units - they give our DynamoDB tables the engines to edit/update/delete data!

1 WCU = 1 item write/second.

Yup, that means it costs more to run write operations than read operations. AWS charges our account based on how many RCUs and WCUs we've used each month. If our performance needs are really high, our RCUs and WCUs will go up!

To make sure we don't get charged for this project, delete all resources once we're done. The **Free Tier** for DynamoDB gives us 25GB of data storage, plus 25 Write and 25 Read Capacity Units (WCU, RCU). This is enough to handle 200M requests per month... all for free.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-67.png)

-    Select **Create table**.
-    Once our table is created, click into our table's name.
-    Pause!
-    Take a moment to look around before moving onto the next step. Get comfortable with the screen and the different things on it - there are quite a few panels in this page!
-    When ready, select **Explore table items** on the top right hand corner.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-68.png)

-    Under **Items returned**, select **Create item**.
-    Awesome! From the DynamoDB console, we can enter data manually straight away.

DynamoDB is actually quite unique for letting us do this. All other AWS database services, except for Amazon Keyspaces, don't let us enter data directly from the console. DynamoDb is designed to be **extremely beginner and developer-friendly**. By having this feature, developers can quickly update their database structure and see how their apps will respond to these changes in real-time. This feature highlights DynamoDB's status as a fully managed AWS service i.e. we can concentrate more on building your applications instead of database management tasks like maintaining the database’s availability, durability, and scalability.

-    Let's add our very first NextWork student to the table. Next to **StudentName**, enter `Nikko`
-    Ooo, turns out we can even add new attributes! Let's select **Add new attribute**, this time selecting **Number**.
-    For the new **Attributes name**, we'll call it `ProjectsComplete`.
-    Enter in a value for the number of projects that Nikko has completed! We'll enter `4`.

In DynamoDB, an attribute is like a piece of data about an item. In this case, our item is Nikko and the attribute is the number of projects Nikko completed. Each item in DynamoDB can have multiple attributes. But, unlike relational databases where each row in a table must have the same columns, DynamoDB items can have their own unique set of attributes.

-    Select **Create item**.

Nice! Notice a green confirmation banner that tells us we've consumed 0.5 Read capacity units.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-69.png)

'Read capacity units consumed: 0.5' means DynamoDB used only 0.5 RCUs to add that item. But AWS Free Tier only provides 25 RCUs a month, this **doesn't** mean we only have 24.5 RCUs left for the rest of this project. Think of it like we're driving a car, and the RCU is our speedometer telling us how much data it's processing a second. As long as we're not asking our table to speed up and perform lots of read operations in the same second, we'll be okay!

-    Check our work. Do you see a **Table** called **NextWorkStudents** with a student **Nikko**?

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-70.png)

This DynamoDB table makes it seem like data is organized in rows and columns, but it's actually quite different from traditional relational databases. Instead of looking at this as a spreadsheet, look at a DynamoDB table as a list of items (i.e. StudentNames, like Nikko), each with their own list of attributes (e.g. ProjectsComplete).

With DynamoDB, our table can become very flexible and we can start adding new and different attributes for every item! It's like having a spreadsheet, except every row can have a different number of columns and different column headers. This level of flexibility is not possible with relational databases.

**Step - 2 : Create DynamoDB tables with AWS CloudShell**

**AWS CloudShell** is shell in our AWS Management Console, which means it's a space for us to run code! The awesome thing about AWS CloudShell is that it already has AWS CLI pre-installed. **AWS CLI** (Command Line Interface) is a software that lets us create, delete and update AWS resources with commands instead of clicking through your console.

-    At the top of your AWS Management Console, select the icon for **AWS CloudShell**.
-    Wait 30 seconds for your environment to be ready.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-71.png)

-    Run these commands to create new tables:

```bash
aws dynamodb create-table \
    --table-name ContentCatalog \
    --attribute-definitions \
        AttributeName=Id,AttributeType=N \
    --key-schema \
        AttributeName=Id,KeyType=HASH \
    --provisioned-throughput \
        ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --query "TableDescription.TableStatus"
aws dynamodb create-table \
    --table-name Forum \
    --attribute-definitions \
        AttributeName=Name,AttributeType=S \
    --key-schema \
        AttributeName=Name,KeyType=HASH \
    --provisioned-throughput \
        ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --query "TableDescription.TableStatus"
aws dynamodb create-table \
    --table-name Post \
    --attribute-definitions \
        AttributeName=ForumName,AttributeType=S \
        AttributeName=Subject,AttributeType=S \
    --key-schema \
        AttributeName=ForumName,KeyType=HASH \
        AttributeName=Subject,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --query "TableDescription.TableStatus"
aws dynamodb create-table \
    --table-name Comment \
    --attribute-definitions \
        AttributeName=Id,AttributeType=S \
        AttributeName=CommentDateTime,AttributeType=S \
    --key-schema \
        AttributeName=Id,KeyType=HASH \
        AttributeName=CommentDateTime,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --query "TableDescription.TableStatus"
```

This script includes commands to create four new tables in AWS DynamoDB, each with specific attributes and settings. The four tables created are:
    1.    **ContentCatalog Table**: This table has a numeric attribute called Id.
    2.    **Forum Table**: This table has a partition key called Name.
    3.    **Post Table**: This table has a partition key called ForumName and a sort key called Subject. We'll dive into sort keys soon!
    4.    **Comment Table**: This table has a partition key called Id and a sort key called CommentDateTime.

-    Off we goooooo!! AWS CloudShell tells AWS CLI to work with DynamoDB and create those tables for you.
-    Let's confirm those tables were actually created. Run these wait commands, and wait until they all run and end:

```bash
aws dynamodb wait table-exists --table-name ContentCatalog
aws dynamodb wait table-exists --table-name Forum
aws dynamodb wait table-exists --table-name Post
aws dynamodb wait table-exists --table-name Comment
```

When we run a wait command, we're telling our terminal to keep waiting until a condition is finally met. In our case, we're saying "keep waiting - don't finish running this command until this table has been created." Wait commands are helpful for making sure necessary resources have been created before we move on. Otherwise, future commands that depend on our resources would automatically fail!

Can you guess what might happen if we ran a wait command before creating the resource itself? The wait command will keep running and waiting... eventually it'll decide that the resource doesn't exist and fail i.e. stop!

-    Head back into our **DynamoDB** console and select the **Tables** tab.
-    Confirm that you see four new tables!
-    Tip: we might need to refresh our page if you don't see them straight away.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-72.png)

**Step - 3 : Load Data into Our Tables**

Now that we've got our DynamoDB tables set up, we can actually start to load data in.
-    Head back into our **CloudShell terminal**.
-    Download and unzip this zip file with data:

```bash
curl -O https://storage.googleapis.com/nextwork_course_resources/courses/aws/AWS%20Project%20People%20projects/Project%3A%20Query%20Data%20with%20DynamoDB/nextworksampledata.zip

unzip nextworksampledata.zip

cd nextworksampledata
```

CloudShell is an environment that can also handle 1GB of storage, so we can save files inside CloudShell. And here's a fun tip: we could write and store scripts right in CloudShell to automate repetitive tasks, so we won't need to run AWS CLI commands line by line! CloudShell's storage is persistent, meaning our files and data will stay available across sessions as long as we stay within the 1 GB limit.

-    Run `ls` to confirm that all files are now inside your CloudShell environment:

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-73.png)

-    Want to see what's inside these files?
-    Run `cat Forum.json`

The **cat** command opens up and lets us read files directly in the terminal! So when we run `cat Forum.json`, the terminal will show us all the data inside the **Forum.json** file right on our screen. This is such a quick way to view or verify the contents of a file without having to open it somewhere else.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-74.png)

**Forum.json** contains data that's been formatted specifically for loading into DynamoDB:
    1.    `"Forum"`: tells DynamoDB that the data relates to the Forum table.
    2.    `"PutRequest"`: tells DynamoDB to add a new item into Forum table.
    3.    Then the rest of each PutRequest includes the attributes of this new item! Notice how the first item has **five attributes** (Name, Category, Posts, Comments, Views) but the second item only has three.
    4.    This is a great example of a DynamoDB table's flexibility - every item can have any number of attributes and attribute titles.
    5.    If we were using a relational database... then both items would need to have a value for all the attribute names in the table! This makes your database bigger (and slower).

-    Load the data of all four files into DynamoDB using AWS CLI's `batch-write-item` command:

```bash
aws dynamodb batch-write-item --request-items file://ContentCatalog.json

aws dynamodb batch-write-item --request-items file://Forum.json

aws dynamodb batch-write-item --request-items file://Post.json

aws dynamodb batch-write-item --request-items file://Comment.json
```

`aws dynamodb batch-write-item` command is used to load or insert multiple items into DynamoDB tables!

`--request-items` tells DynamoDB that the items are currently stored inside a file that it'll need to retrieve from.

`file://` then tells DynamoDB that the file is stored locally in the CloudShell environment, with the name FILENAME.json.

Each .json file we upload tells DynamoDB which table the items should go to!

-    After each data load, we should get this comment saying that there were no Unprocessed Items.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-75.png)

Unprocessed items are records that weren't written to our database! If you see an unprocessed item, an error happened while loading our data into DynamoDB.

**View and update your loaded data**
-    Head back to the **DynamoDB** console.
-    Select **Tables** from the left hand navigation panel.
-    Pick the **ContentCatalog** table.
-    Select **Explore table items** on the top right.
-    Wooooohoo! Our items are now on display.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-76.png)

The data we loaded when we ran `aws dynamodb batch-write-item` is here! We can see now that the table has a partition key of Id (the very first column!), and there are 6 items in the table.

-    Scroll through all the columns, can you tell what this data is showing?
-    Take a look at the **ContentType** column. Some items are **Projects** and some items are **Videos**.
-    This means the **ContentCatalog** Table stores NextWork's entire collection of content, which includes step-by-step projects (Projects) and wide range of videos (Videos).

-    Click into a Project e.g. click into the item with the Id `1`.
-    Wow! We get to see all the attributes in this Project right away.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-77.png)

-    Click **Add new attribute**, select **String** from the dropdown.
-    Name the new attribute `StudentsComplete`
-    For the value, enter `Nikko`

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-78.png)

-    When done, click **Save and close**.
-    Nice, we've just added a new attribute - **StudentsComplete** to an item!
-    Now take a look at our Table - looks like **StudentsComplete** is now a new attribute at the top of the table.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-79.png)

-    Does that mean StudentsComplete is now an attribute for all the other items in the table?
-    Let's find out. Try opening a Video item e.g. the item with Id `203`.
-    Ooo this item has its own list of attributes...

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-80.png)

-    Is our new attribute **StudentsComplete** here?
-    Nope, it isn't!
-    This is the reason why DynamoDB is known for its flexibility - every single item can have their own set of attributes. Just because StudentsComplete is an attribute in the first item (with Id 1), that doesn't mean there's a StudentsComplete attribute in the other items in this table.

**What's the difference between this and a relational database?** Relational databases would need each row to have the same number of columns. So if we added StudentsComplete as a new column in a relational database, every item in that database would need to have a StudentsComplete value too, even if it doesn't apply.

This has huge impacts on a DynamoDB vs a relational database's flexibility and speed!
    1.    Flexibility - every item having their own unique set of attributes is a huge advantage when items in a table could look different from each other. For example, e-commerce sites and shopping carts need to store different types of products with different attributes in the same place.
    2.    Speed - DynamoDB tables can use partition keys to split up a table and quickly find the items they're looking for. Relational databases have to scan through the entire table to find data, which can slow down performance.

**When would someone pick relational databases over non-relational?** Relational databases use SQL, which makes handling complex queries a lot more straightforward! The strictness of a relational database's schema also means data is kept precise, accurate and consistent, which can be helpful for situations where the quality of data is a top priority (e.g. healthcare systems often opt for relational databases to keep patient records).

---
##    Query Data with DynamoDB

**Step - 1 : Run Our first query with the console**

We've got our DynamoDB tables AND loaded in data. Now let's extract some insights from our data using queries.
-    Stay on the **ContentCatalog** table, but this time let's select **Query** under the heading **Scan or query items**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-81.png)

-    Let's enter an **Id (Partition key)** to query for a specific item. Enter `201`
-    Select **Run**.
-    Oooo, now only the specific item you've searched for is in your table.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-82.png)

-    Are we ready to query another table?
-    From the **Tables** section on the left hand side of the console, select **Comment**.
-    Expand the **Scan or query** items arrow.
-    Select **Query**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-83.png)

-    Ooo, notice anything different?
-    There's **Id (Partition key)** like last time, but there's also a new Sort key called **CommentDataTime** underneath.

A sort key is a secondary key used to filter our query results again! Sort keys work after the partition key i.e. we still have to use the partition key to split up our data first, and then the sort key partitions our data again. Sort keys are optional, which is why our ContentCatalog table could still work without one!

A sort key is a great (and oftentimes, necessary) tool for tables where items can share a partition key value! In those situations, the **partition key + sort key** should form a unique combination. That combination is called our **primary key** so we can still query for single, unique items in that table.

-    Before we run any queries, check out the items in **Comment**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-84.png)

This table is storing comments left on posts in the NextWork community. It might help to check out the Posts table too - notice that there are two items there, and each item is a post that was made in the community. A Comment is a comment made on that post, which is why their ID is the name of the original post!

-    Now try to query this so that you're only seeing comments to the post **I have a question/Just Complete Project #7 Dependencies and CodeArtifacts**. Only show comments that were posted from the 1st of September, 2024.
-    Under **Id (Partition key)**, enter `I have a question/Just Complete Project #7 Dependencies and CodeArtifacts`
-    Under CommentDateTime (Sort key), switch the **Equal to** dropdown to **Greater than**.
-    Enter `2024-09-01` as your sort key.
-    Select **Run**.
-    Bingo!

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-85.png)

-    Here's your next challenge: how would you look for all comments posted by **User Abdulrahman**?
-    Clear the **Id (Partition key)** and **CommentDateTime (Sort key)**.
-    Expand the **Filters** arrow.
-    Enter `PostedBy` as the **Attribute name**.
-    Enter `User Abdulrahmanas` the **Value**.
-    Select **Run**.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-86.png)

**There's an error!** That's right - we have to use the Id (Partition key) when we query items. This teaches us two things:
    1.    Data modelling is sooo important - data engineers think very carefully about what queries they should prepare and design for before they upload any data!
    2.    We probably wouldn't have this kind of issue if we could use SQL, so this is a situation where it would be beneficial to use a relational database!

**Data modeling** in DynamoDB is all about planning how to set up our tables, e.g. what should be a table's partition keys and sort keys? This planning stage is essential because it affects how easily we can get to our data and how quickly our database responds later on. If we don’t get this part right, like if we use the wrong keys in our queries, finding the data we need can become really slow or even impossible (like our scenario here!).

**Step - 2 : Run Queries with AWS CLI**

Let's see how we could run the same queries in the CLI!
-    Head back to your **AWS CloudShell** environment, this time running this AWS CLI command:

```bash
aws dynamodb get-item \
    --table-name ContentCatalog \
    --key '{"Id":{"N":"201"}}'
```

`aws dynamodb get-item` is the AWS CLI command to get a single item from a DynamoDB table.

`--table-name ContentCatalog` tells DynamoDB that the item belongs in the table called ContentCatalog.

`--key '{"Id":{"N":"201"}}'` tells DynamoDB that the item has the ID "201". The `{"N":}` means the ID is a number (N for Number).

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-87.png)

-    Next, run this command.

```bash
aws dynamodb get-item \
    --table-name ContentCatalog \
    --key '{"Id":{"N":"101"}}' \
    --consistent-read \
    --projection-expression "Title, ContentType, Services" \
    --return-consumed-capacity TOTAL
```

-    Nice - here's our output!

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-88.png)

This command is just like the get-item command we ran before, but this time we're adding extra query options that tells DynamoDB exactly how we want it to get the item:
    1.    `--consistent-read` : we want a strongly consistent read.
    2.    `--projection-expression` : we only want to know some of the item's attributes.
    3.    `--return-consumed-capacity` : we want to know how much capacity was consumed by the request.

A **strongly consistent read** means DynamoDB will give us the guaranteed most recent version of that item. By default, a read from DynamoDB uses **eventual consistency**, which is a lower cost option that is faster and retrieves a version of the data that might not be the most updated version.

**Eventual consistency** is no issue if we're using a small dataset (consistency is usually reached within a second of updating the data anyway). But, strongly consistent reads could be our choice for situations where our app needs the most recent data to make important decisions e.g. financial trading apps that need the most recent market data.

-    Before we move on, check our output again - did it return any item at all?
-    Look closely at the command we ran... is there any item in **ContentCatalog** with the **Id** `101`? (Nope!)
-    Run the command again, this time with a valid Id and back to an eventually consistent read:

```bash
aws dynamodb get-item \
    --table-name ContentCatalog \
    --key '{"Id":{"N":"202"}}' \
    --projection-expression "Title, ContentType, Services" \
    --return-consumed-capacity TOTAL
```

-    We will see that eventually consistent reads consume half as much capacity - which is why it's the default for DynamoDB read operations.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-89.png)

**Step - 3 : Set up a transaction**

We might notice that our tables contain lots of related data!

1.    Each **Forum** contains multiple **Posts**.
2.    Each **Post** contains multiple **Comments**.
    
How does that impact how we manage our data? Let's find out...

-    Check out the **Forum** table - there's a count for the number of **Posts**, and a count for the number of **comments** i.e. comments in each forum.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-90.png)

-    That means if we add a new Comment to the **Comment** table, the database needs to increase the **Comments** count in the related **Forum** item.

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-91.png)

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-91'.png)

-    How would we make sure that when a students posts a new Comment, the number of comments recorded in a forum also updates?
-    Let's try do this using AWS CLI.
-    Run this command in our AWS CloudShell environment:

```bash
aws dynamodb transact-write-items --client-request-token TRANSACTION1 --transact-items '[
    {
        "Put": {
            "TableName" : "Comment",
            "Item" : {
                "Id" : {"S": "Events/Do a Project Together - NextWork Study Session"},
                "CommentDateTime" : {"S": "2024-9-27T17:47:30Z"},
                "Comment" : {"S": "Excited to attend!"},
                "PostedBy" : {"S": "User Connor"}
            }
        }
    },
    {
        "Update": {
            "TableName" : "Forum",
            "Key" : {"Name" : {"S": "Events"}},
            "UpdateExpression": "ADD Comments :inc",
            "ExpressionAttributeValues" : { ":inc": {"N" : "1"} }
        }
    }
]'
```

This command is a **transaction**, which is a group of operations that all have to succeed - if any of the operations in the group fails, none of the changes get applied. This makes sure that any change to your database is consistent across all our tables!

In this transaction, we are recording a new comment made by **User Connor**!
    1.    The **Comment** table needs to update - we're adding a new item to the table with all the details of Connor's comment.
    2.    The **Forum** table also needs to update - Connor's comment was made on a post in the Events forum, so the number of comments in that forum should go up by 1.

That's why a transaction is so helpful to make sure the update is done consistently across both tables. This is an example situation where the AWS CLI is quite beneficial over the AWS Management Console - transactions don't exist in the console, and updating things manually with clicks can definitely lead to mistakes!

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-92.png)

-    Whoosh! We've updated both the **Comment** and **Forum** tables.
-    Let's check our work.
-    Run this command in CloudShell to view our Events forum, and we'll see the **Comments** count go up from **0** to **1**.

```bash
aws dynamodb get-item \
    --table-name Forum \
    --key '{"Name" : {"S": "Events"}}'
```

-    Here's what we'll see:

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-93.png)

-    Check the number of comments in the Events table in our console now too:

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-94.png)

Nice work! We've just learnt how to create a DynamoDB table AND create some fantastic queries.

**Step - 4 : Delete Your Resources**

Deleting resources that are not actively being used stops us getting charged and is a best practice. Not deleting our resources will result in charges to our account.

-    **DynamoDB tables** : Try delete our tables using AWS CloudShell!

This command will delete all of their items too.

```bash
aws dynamodb delete-table --table-name Comment
aws dynamodb delete-table --table-name Forum
aws dynamodb delete-table --table-name ContentCatalog
aws dynamodb delete-table --table-name Post
```

![image alt](https://github.com/AtulSharmaGeit/AWS-X-Databases/blob/22460274f1da260cbc1fffef2588d0da4e43720c/Images/Databases-95.png)

