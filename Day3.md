# Challenge

## 1A. Oracle to Postgres dms task challenge
1. Make sure we have taken care of prerequisites for the RDS oracle to use as CDC source with logminer https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.Oracle.html
>hint: Source is RDS oracle and not an EC2 or On-prem Oracle instance, thus prerequisites will vary.
2. Open **AWS DMS Console** by clicking on link https://console.aws.amazon.com/dms/v2/home?region=us-east-1
3. Open **Database migration tasks**.
4. Click on **Create task** from right top corner.
5. **Task Identifier**: *ORA2AUPGcdc*
6. **Replication instance** Select the replication instance.
7. **SourceDatabaseEndpoint** Select SQLserver endpoint *oraclesource*.
8. **TargetDatabaseEndpoint** Select Aurora Postgres endpoint *aurorapgtarget*.
9. Set the Migration type to *Migrate existing data and replicate ongoing changes*
10. Under **Task settings** check the logging by selecting **Enable CloudWatch logs**.
11. Under **Table mappings** select table **NEWTABLE** from **MYUSER** schema.
12. Add a **transformation rule**
    1. to rename schema from MYUSER to public.
    2. convert the table NEWTABLE to lowercase.
    3. convert all column names (%) of table NEWTABLE to lowercase
13. Lastly **Create task**
14. Once the task is running in state **ongoing replication**, verify the Aurora Postgres Table newtable has required full load data:
```SQL
select * from newtable;
```
15. Run sample inserts/update statements to the source table
```SQL
INSERT INTO newtable (id,fname,lname,region) VALUES (3,'Arthur','Coleman','NORTH');
INSERT INTO newtable (id, fname,lname,region) VALUES (4, 'Nigel','Newman','SOUTH');
UPDATE newtable set region='SOUTH' where id=2;
```
16. Verify the data is getting migrating correctly.
17. If not, troubleshoot and fix the same.
18. Finally, do upload the task logs/source and target table (newtable) select outputs.
19. Zip above files and name it as per your full name and day3
Lastly upload it to
https://tinyurl.com/yyjggswp  


## 1B. SQLServer to Postgres dms task challenge
1. Make sure we have taken care of prerequisites for the EC2 SQLServer to use as CDC source with replication https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.SQLServer.html
2. For source EC2 SQL Server rdp (remote desktop) using login **\administrator** and password **pAsswOrd12**
3. Run PSQL script on target aurora postgres cluster as mentioned in next section:
2. Open **AWS DMS Console** by clicking on link https://console.aws.amazon.com/dms/v2/home?region=us-east-1
3. Open **Database migration tasks**.
4. Click on **Create task** from right top corner.
5. **Task Identifier**: *SQL2AUPGcdc*
6. **Replication instance** Select the replication instance.
7. **SourceDatabaseEndpoint** Select SQLserver endpoint *mssqlsource*.
8. **TargetDatabaseEndpoint** Select Aurora Postgres endpoint *aurorapgtarget*.
9. Set the Migration type to *Migrate existing data and replicate ongoing changes*
10. Under **Task settings** make sure that
    1. The **Target table preparation mode** is set to **Truncate**.
    2. Check the logging by selecting **Enable CloudWatch logs**.
11. Under **Table mappings** migrate table **finaltb** from **dbo** schema.
12. Add a **transformation rule**
    1. to rename schema from dbo to public.
13. Lastly **Create task**
14. Once the task is running in state **ongoing replication**, verify the Aurora Postgres Table newtable has required full load data:
```SQL
select * from finaltb;
```
16. Verify the data is getting migrating correctly.
17. If not, troubleshoot and fix the same.
18. Finally, do upload the task logs/source and target table (finaltb) select outputs.
19. Zip above files and name it as per your full name and day3
Lastly upload it to
https://tinyurl.com/yyjggswp  

# SQLServer to Postgres dms task script to run on target Aurora instance
Script to run on target Aurora Writer PSQL connection:

```SQL
CREATE TABLE finaltb(
microid BIGINT NOT NULL GENERATED ALWAYS AS IDENTITY( START WITH 1 INCREMENT BY 1),
description VARCHAR(255),
orderno BIGINT NOT NULL
)
        WITH (
        OIDS=FALSE
        );

CREATE TABLE ordertb(
orderid BIGINT NOT NULL GENERATED ALWAYS AS IDENTITY( START WITH 1 INCREMENT BY 1),
fname VARCHAR(255),
lname VARCHAR(255)
)
        WITH (
        OIDS=FALSE
        );

ALTER TABLE finaltb
ADD CONSTRAINT pk_finaltb_933578364 PRIMARY KEY (microid);

ALTER TABLE ordertb
ADD CONSTRAINT pk__ordertb__0809337dd08f972e PRIMARY KEY (orderid);

ALTER TABLE finaltb
ADD CONSTRAINT fk_finaltb_finaltb_965578478 FOREIGN KEY (orderno)
REFERENCES ordertb (orderid)
ON UPDATE NO ACTION
ON DELETE NO ACTION;


