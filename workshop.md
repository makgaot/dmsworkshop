# Workshop Agenda

1. Create a *Replication Instance*.
2. Create Source and Target *Endpoints*.
3. Create a *Replication task* to migrate data from either a Oracle or SQLServer Source to Aurora Postgres target.


# Replication Instance
1. Open **AWS DMS Console** by clicking on link https://console.aws.amazon.com/dms/v2/home?region=us-east-1
2. Open **Replication Instances**.
3. Create a replication Instance with following Inputs:
    1. Name of instance and short description
    2. Select VPC (having name **myVPC**)
    3. Under advanced security and network configuration select “VPC Security Group” name having **EC2ClientSG**
    4. Lastly click on **Create**.

# Endpoints
Based on your interest you can work on a *Oracle to Aurora Postgres* Or a *SQL Server to Aurora Postgres* migration.

## SQL Server
1. Open **AWS DMS Console** by clicking on link https://console.aws.amazon.com/dms/v2/home?region=us-east-1
2. Open **Endpoints**.
3. Click on Create Endpoint
4. Select Endpoint type as **Source endpoint**.
5. **Endpoint identifier**: a label for source sql server say **mssqlsource**
6. **Source engine**: Select *sqlserver*.
7. **Server name**: from the cloudformation stack output use the SQL Server endpoint e.g. *ec2-54-243-25-80.compute-1.amazonaws.com*
8. **Port**: *1433*
9. **User name**: *myuser*
10. **Password**: *pAsswOrd12*
11. **Database name**: *mydata*
12. Expand **Test endpoint connection (optional)**
13. **VPC**: Select VPC (having name *myVPC*)
14. **Replication instance**: select replication instance in previous section.
15. click on **Run test**
16. If the connection test fails troubleshoot the same.
17. Lastly click on **Create endpoint**.


## Oracle
1. Open **AWS DMS Console** by clicking on link https://console.aws.amazon.com/dms/v2/home?region=us-east-1
2. Open **Endpoints**.
3. Click on Create Endpoint
4. Select Endpoint type as **Source endpoint**.
5. **Endpoint identifier**: a label for source Oracle  say **oraclesource**
6. **Source engine**: Select *oracle*.
7. **Server name**: from the cloudformation stack output use the Oracle endpoint e.g. *ro1elgnpepti0fd.cfbcererxz1y.us-east-1.rds.amazonaws.com*
8. **Port**: *1521*
9. **User name**: *myuser*
10. **Password**: *pAsswOrd12*
11. **Database name**: *mydata*
12. **SID/Service name: * ORCL
13. Expand **Test endpoint connection (optional)**
14. **VPC**: Select VPC (having name *myVPC*)
15. **Replication instance**: select replication instance in previous section.
16. click on **Run test**
17. If the connection test fails troubleshoot the same.
18. Lastly click on **Create endpoint**.


## Aurora Postgres
1. Open **AWS DMS Console** by clicking on link https://console.aws.amazon.com/dms/v2/home?region=us-east-1
2. Open **Endpoints**.
3. Click on Create Endpoint
4. Select Endpoint type as **Target endpoint**.
5. **Endpoint identifier**: a label for target aurora cluster say **AuroraPGTarget**
6. **Target engine**: Select *aurora-postgres*.
7. **Server name**: from the cloudformation stack output use the Aurora Postgres **Writer** endpoint e.g. *rds-aupg106-111h2zti22ufc.cluster-cfbcererxz1y.us-east-1.rds.amazonaws.com*
8. **Port**: *5432*
9. **User name**: *myuser*
10. **Password**: *pAsswOrd12*
11. **Database name**: *mydata*
12. Expand **Test endpoint connection (optional)**
13. **VPC**: Select VPC (having name *myVPC*)
14. **Replication instance**: select replication instance in previous section.
15. click on **Run test**
16. If the connection test fails troubleshoot the same.
17. Lastly click on **Create endpoint**.


# Task (SQLServer -> Aurora Postgres)
1. Open **AWS DMS Console** by clicking on link https://console.aws.amazon.com/dms/v2/home?region=us-east-1
2. Open **Database migration tasks**.
3. Click on **Create task** from right top corner.
4. **Task Identifier**: *SQL2AUPG*
5. **Replication instance** Select the replication instance.
6. **SourceDatabaseEndpoint** Select SQLserver endpoint *mssqlsource*.
7. **TargetDatabaseEndpoint** Select Aurora Postgres endpoint *aurorapgtarget*.
8. Set the Migration type to *Migrate existing data*
9. Under **Task settings** check the logging by selecting **Enable CloudWatch logs**.
10. Under **Table mappings** select table **mytable** from **dbo** schema.
11. Add a **transformation rule** to rename schema from dbo to public.
12. Lastly **Create task**

Monitor the task for successful completion. If not troubleshoot the same using cloudwatch logs.

Verify the data is migrated to target (aurora postgres)
```
select * from mytable;
```
# Task (Oracle -> Aurora Postgres)
1. Open **AWS DMS Console** by clicking on link https://console.aws.amazon.com/dms/v2/home?region=us-east-1
2. Open **Database migration tasks**.
3. Click on **Create task** from right top corner.
4. **Task Identifier**: *ORA2AUPG*
5. **Replication instance** Select the replication instance.
6. **SourceDatabaseEndpoint** Select SQLserver endpoint *oraclesource*.
7. **TargetDatabaseEndpoint** Select Aurora Postgres endpoint *aurorapgtarget*.
8. Set the Migration type to *Migrate existing data*
9. Under **Task settings** check the logging by selecting **Enable CloudWatch logs**.
10. Under **Table mappings** select table **MYTABLE** from **MYUSER** schema.
11. Add a **transformation rule**
    1. to rename schema from MYUSER to public.
    2. convert the table MYTABLE to lowercase.
    3. convert all column names (%) of table MYTABLE to lowercase
12. Lastly **Create task**

Monitor the task for successful completion. If not troubleshoot the same using cloudwatch logs.

Verify the data is migrated to target (aurora postgres)
```
select * from mytable;
```
