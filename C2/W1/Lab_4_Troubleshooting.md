In the last lab of this week, you'll be provided with an **RDS database** that you need to connect to **from an EC2 environment**, and then **create a table inside the database and populate the table with data**. 

- Connecting to a database and moving data into it and reading data from it are all super common tasks you'll encounter as a data engineer. 
- And like I said before, having to troubleshoot connection and permission issues in these basic tasks is something you'll run into all the time. 

So in this lab, when you first attempt to connect to the database, **you'll encounter several issues that you'll need to fix.** ![[Screenshot 2025-12-03 at 19.52.47.png]]


After that, you'll need to download a CSV file from an S3 bucket and then copy the data from the CSV file to the database. To read the file from the S3 bucket, you'll encounter permission issues that you'll need to fix. 
![[Screenshot 2025-12-03 at 19.53.05.png]]


RDS Endpoint: 
de-c2w1a1-rds.cvsqi0uyqtwv.us-east-1.rds.amazonaws.com
Port: 5432