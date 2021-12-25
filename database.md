# Database Refresher

https://learn.cantrill.io/courses/1101194/lectures/27894829

Row Store (MySQL) can be used to get list of rows.

These rows can then be moved to Column Store (Redshift) for processing.

Wihtout Graph database a similar data fetch from SQL database would require lots of complex query processing for each request.

## Databases on EC2

When to use:

- When you want particular version of DB
- When you want certain combination of OS and DB

# Relational Database Service (RDS)

Database-as-a-service (DBaaS) - not entirely true more of DatabaseServer-as-a-service. Managed Database Instance for one or more databases

Multiple engines are MySQL, MariaDB, PostgreSQL, Oracle, Microsoft SQL

Amazon Aurora. This is so different from normal RDS, it is a seperate product.

## RDS Database Instance

Runs one of a few types of database engines and can contain multiple user created databases. Create one when you provision the instance, but multiple can be created after.

Database connects with a CNAME. RDS uses standard database engines.

The database can be optimized for:

- db.m5 general
- db.r5 memory
- db.t3 burst

There is an associated size and AZ selected.

When you provision an instance, you provision storage that is dedicated to that instance. This is EBS storage located in the same AZ. RDS is vulnerable to failures in that AZ.

The storage can be allocated with SSD or magnetic similar to EC2 EBS storage options.

io1 - high IO  
gp2 - same burst pool  
magnetic - compatibility

Billing is per instance and hourly rate for that compute. You are billed for storage allocated.

## RDS Multi AZ (High-Availability)

RDS Access ONLY via database CNAME. You cannot access the `standby replica` for any reason via RDS.

- Its a standby replica, not a read replica

  The CNAME will point at the primary instance.

If any error occurs with the primary database, AWS detects this and will failover within 60 to 120 seconds to change to the new database.

    During failover CNAME will be updated to point to standby replica

## Syncronous Replication

**Syncronous Replication** is a keyword:

1. Database writes happen
2. Primary database instance commits changes
3. Same time as the write is happening, standby replication is happening
4. Standby replica commits writes.

This does not provide fault tolerance - there will be some impact during change

## Considerations

- Multi-AZ feature is not free tier, extra infrastructure for standby. Generally two times the price.
- The standby replica cannot be accesed directly unless a fail occurs.
- Failover is highly available, not fault tolerant.
- Standby replica is in the same region only (other AZ in the VPC).
- Backups taken from standby (removes performance impacts).
- Standby replica will be used during AZ outage, primary failure, manual failover, instance type change, and software patching

## RDS Backup and Restores

RPO - Recovery Point Objective

- Time between the last backup and when the failure occured
- Amount of maximum data loss
- Influences technical solution and cost
- Lowering RPO would cost more

RTO - Recovery Time Objective

- Time between the Disaster Recovery (DR event) and full recovery
- Influenced by process, staff, tech and documentation

![img](./imgs/databases/RPOvsRTO.webp)

### RDS Backups

Types of Backups

- Automatic Backups
- Manual Snapshots

`Both type of backup use AWS managed S3 Buckets`.

- The buckets will not be visible in the AWS mgmt console
- Since data in S3 is replicated across regions, `RDS data becomes region resiliant`
- For single-az, the backup will occur from single instance
- For multi-az, backup occurs from standby replica

Similar to EBS snapshots, first snapshot is FULL size of consumed data. Subsequent snapshots are incremental.

#### Manual snapshots

Manual snapshots will remain in your AWS account even after the deletion of the database.

- Manual snapshots needs to be deleted manually.

#### Automatic Snapshots

Every 5 minutes translation logs are saved to S3. Using these `database can then be restored to a 5 min snapshot in time`.

Retention period can be anywhere from 0 to 35 days. This will be applicable to both the snapshots and the translation logs.

When you delete the database, snapshots can be retained but they will expire based on their retention period.

### RDS Restores

Post restore we need to point to new RDS instance's endpoint address

    When performing a restore, RDS creates a new RDS instance with a `new endpoint address`.

When restoring a manual snapshot, you are setting it to a single point in time. This influences the RPO value.

Automated backups are different, any 5 minute point in time.

Backups are restored and `transaction logs are replayed to bring DB to desired point in time`.

Restores aren't fast, think about RTO.

---

## RDS Read-Replicas

Unlike standby replicas the read replicas are used but only for read operations

- They have their own database address
- So applications needs to be setup accordingly
- These are kept in sync using **asyncronous replication**

### Asynchronous Replication

- It is written fully to the primary instance. Once its stored on disk, it is then pushed to the replica. This means there could be a small lag.
- These can be created in the same region or a different region. - This is a **cross region replication**

![img](./imgs/databases/RDS_RR_Architecture.webp)

### Performance Improvement (read)

We can have 5 times the read replica per DB instance.

You can combine this with multi-az.

- Use multi-az to provide availability benefit and `to remove any backup impacting application performance`
- Read replicas will be used to improve read performance
- Read replicas can have their read replicas
- Each nested read replica will have more lag than the read replica from its source

### RPO and RTO

RPO's are near zero for read replicas (because of asynchronous replications)

RTO's are low because the read replica can be quickly promoted to primary instance

- Once promoted read and write are allowed on that instance
- They cannot be converted into a read replica again

If there is a data corruption due to error or malware, you need to fallback to snapshot instead of read replicas

Global availability can be increased with cross region read replicas

    Read replica only provide read scaling, not write scaling

---

## RDS Security

SSL/TLS is available for RDS in transit

- This can be mandatory on per user basis

### Basics

RDS encryption is support using KMS and EBS Volume encryption

    Once encrytion is enabled, it cannot be removed

- RDS database writes unencrypted data
- Data is encrypted by host the RDS instance is running on
- AWS or Customer Manged CMK generates Data Encryption Keys
- Data Encryption Keys are used for the actual encryption operation

> Storage, Logs, Snapshot and Replicas are encrypted using the same CMK [or DEK?]

### RDS MySQL and RDS Oracle

RDS MySQL and RDS Oracle support TDE

- TDE stands for Transparent Data Encryption
- Encryption is handled by the DB engine instead of the host
- This is less trust worthy

RDS Oracle support TDE with CloudHSM

- Allows much stronger control over the keys
- Allows to remove AWS from chain of trust

### How Encryption and TDE work

Consider few DB instances backed by their host and these instances use EBS for their underlying storage.

With Oracle using TDE with CloudHSM.

    Data is encrypted before leaving the instance

- This setup peforms encryption using DB engine
- Apart from RDS instance, the key is not known to AWS

With KMS based encryption

    Database engine has no encryption awareness

- DEKs are loaded in to the hosts as required
- Database engine perfroms a regular write operation, unaware of any encryption
- Data is encrypted by the host before sending it to EBS volume
- Data is decrypted by the host after reading ti from EBS volume

![img](./imgs/databases/RDSEncryption.webp)

> Red lines represent encryption and decryption operations

### RDS IAM Authentication

    IAM Authentication is only used for authentication, authorisation is controlled within RDS (based on permissions on the local DB user).

RDS IAM Authentication allows to access RDS instance without password

    For this to work, you need to configure RDS instance to allows IAM user authentication

- `generate-db-auth-token` is used to create the token thats valid for `15 minutes`
- Policy attached to Users or Roles `maps that IAM identity onto the local RDS user`

![img](./imgs/databases/RDSIAMAuthentication.webp)
