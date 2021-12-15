# Storage Basics

- `Direct` local attached storage - these are physical disks on EC2

- `Network` attached storage - volumes delivered over the network (EBS)

- `Ephemeral` storage - temporary storage. Instant store attached to EC2 host

- `Persistent` storage - permanent storage that lives on past the lifetime of the instance. EBS is persistent storage.

## Three types of storage

- `Block Storage` - volume presented to the OS as a collection of blocks.
  - These are mountable and bootable.
  - You can mount an EBS volume
  - You can boot off an EBS volume.
- `File Storage` - Presented as a file share with a structure.
  - You access the files by traversing the storage.
  - You cannot boot from storage.
  - You can mount it.
- `Object Storage` - It is a flat collection of objects.
  - An object can be anything with or without attached metadata.
  - To retrieve the object, you need to provide the key and then the value will be returned.
  - This is not mountable or bootable.
  - It scales very well and can have simultanious access.

### Storage Performance

- `IO Block Size` - size of the wheels. This determines how to split up the data.
- `IOPS` - speed of an engine rev (RPM). How many reads or writes a storage system can accomidate in a second.
- `Throughput` - end speed of the race car. This can be influenced by the network speed for network storage. Expressed in MB/s (megabyte per second).

```
Block Size \* IOPS = Throughput
```

---

## Elastic Block Store (EBS)

The OS will create a file system on top of this, NTFS or EXT3 and then it mounts it as a drive or a root volume on Linux.

- It cannot be connected to two EC2 instances at once
- It can be dettached and reattached from one to another
- It `can only be attached to EC2 instance in the same AZ`
- One EC2 instance can be connected to multiple EBS volumes at once

### Availability

- Volumes are isolated to one AZ.
- EC2 instance in one AZ cant access EBS volume of another AZ.
- The data is highly available and resilient for that AZ.
- All of the data is replicated within that AZ. The entire AZ must have a major fault to go down.

### Snapshots

- Snapshots are copied to S3
  - Across regions and
  - 3 plus AZ's in a region
- You can copy the snapshot to S3 in another region
  - And create the EBS volume from the snapshot in any AZ in that region
- Snapshots are incrimental, the first being a full backup. And any future snapshots being incremental.
- Size of snapshot is determined by the data in the volume, not the volume size.
- Even if a single snapshot is deleted other snapshots or future snapshots will not be impacted. Each snapshot are designed to be self sufficient as in they are not impacted by what happens to other snapshots. [What if you delete the first one?]

#### Restore

- If you restore a snapshot, it does it lazily.
- If you restore a volume, it will transfer it slowly in the background.

If you attempt to read data that hasn't been restored yet, it will pull it from S3, but this will achieve lower levels of performance than reading from new EBS volume directly.

- You could also use Fast Snapshot Restore (FSR)
- This immediately restores the data to EBS from S3 snapshot
- You can create 50 FSR per region
- FSR costs extra than regular restore

#### Billing

- GB/month
- Incremental snapshots takes less space and are billed accordingly, not as per the EBS volume size

### Encryption

- Can be encrypted using KMS
- If the user is having data on an encrypted volume and is trying to share it with others, he has to copy the data from the encrypted volume to a new unencrypted volume.

### Pricing

Billed based on GB/month (and performance in some cases)

### Consideration

- With EC2, no matter how many EBS volumes you combine using RAID0, the maximum IOPS is limited to 260,000 (io1/2/BE/gp2/3)
- Instance store volumes can give more than 260,000 IOPS using correct instance type

---

## Types of EBS Volumes

- General purpose SSD (gp2)
- Provisioned IOPS SSD (io1)
- T-put optimized HDD (st1)
- Cold HDD (sc1)

Each volume has a dominant performance attribute

SSD based focus on maximum IOPS such as a database
HDD based focus on throughput for logs or media storage.

---

## EBS - General Purpose SSD (gp2)

Volumes can be from 1GB to 16TB

- Suitable for low latency workload such as boot volume, low latency apps, dev and test

### Credit Based

Say you are tranferrring a 160KB data, then you will consume 10 IOPS.

- 1 IOPS is 1 IO in 1 second
- 1 IOPS assume 16 KB

You need IO credit to do IO operations. Credits are placed under an IO bucket.

- It has a capacity of 5.4 million IOPS credits
- Fills at the `baseline performance`

### Credits Refill

- Bucket fill with at least a minimum `(base)` of 100 IO credits per second (regardless of the volume size)
- On top of that, bucket fills based on volume size
- Bucket is filling up with `3 IO credit per second per GB of volume size` max `(burst)`
- i.e. 100 GB volume will get 300 IO credits per second refilling the bucket

### Credit Depletion

- You can use upto `3000 IOPS` during heavy workload
- So even if the bucket is filling up with `3 IO credit per second per GB of volume size`, you can consume more
- One reason for this is that the credit bucket is initialized with 5.4 million IO credits
- So, we can run the IO operations at `3000 IOPS` for full 30 minutes

### IOPS

- For small volume sizes (100 minimum, 3000 burst)
- For large volume sizes - greater than 5.3 TB (16000 IOPS)

### EBS - General Purpose SSD (gp3)

- 20% percent cheaper than gp2 (base price)
- 3000 IOPS and 125 MBPS (standard) per volume
- 16000 IOPS and 1000 MBPS for extra cost per volume
- 4 times throughput of 1000 MBPS vs 250MBPS of gp2

---

## EBS - Provisioned IOPS SSD (io1)

With this IOPS can be adjusted independently of size.

- Suitable for super high performance and low latency workloads

Has three class

- io1
- io2
- BlockExpress

### Volume Size

- 4GB to 16 TB (io1/io2)
- 4GB to 64 TB (BlockExpress)

### IOPS

- Upto 64,000 IOPS and 1000 MBPS per volume (io1/io2)
- Upto 256,000 IOPS and 4000 MBPS per volume (BlockExpress)

### Per Instance Performance

This is the limit of throughput between EBS volumes combined to one single EC2 instance.

- io1 (260,000 IOPS and 7500 MBPS)
  - you need about io1 4 volumes connected to an EC2 instance to reach this throughput
- io2 (160,000 IOPS and 4750 MBPS)
- BlockExpress (260,000 IOPS and 7500 MBPS)

### Size to performance limitation

- 3 IOPS/GB max (gp3)
- 50 IOPS/GB max (io1)
- 500 IOPS/GB max (io2)
- 1000 IOPS/GB max (BlockExpress)

---

## EBS - Hard Disk Based

- Suitable when you need to read large blocks of data rather than random access of data
- Big data, data warehouse and log processing

**NOTE**: Cannot be a boot volume.

### Volume Size

- 125 GB to 16 TB (st1/sc1)

### IOPS

- Maximum of 500 IOPS (1MB blocks) i.e. 500 MBPS
- Maximum of 250 IOPS (1MB blocks) i.e. 250 MBPS

### Credit Based

Similar to gp2, this uses a credit based performance model with a credit bucket. It uses MBPS instead of IOPS though.

- st1
  - Base (40MBPS/TB)
  - Burst (250MBPS/TB)
- sc1
  - Base (12MBPS/TB)
  - Burst (80MBPS/TB)

---

## EBS - EC2 Instance Store Volumes

An instance store provides temporary block-level storage for your instance.

- Instance store is ideal for temporary storage of information that changes frequently, such as buffers, caches, scratch data, and other temporary content

The volumes are `physically connected to one EC2 host`. They are isolated to that one specific host.

Highest storage performance in AWS.

They are included in instance price.

**NOTE**: They can be attached ONLY at launch. Cannot be attached later.

- An instance store consists of one or more instance store volumes exposed as block devices.
- The size of an instance store as well as the number of devices available varies by instance type.
- Any data on instance store data is lost if it gets moved, or resized.

### IOPS

- D3 type instance (4.6 GBPS)
- I3 type instance (16 GBPS)

---

# Commands

List block devices (connected to an EC2 instance)

> lsblk

Check if there are any file system on the block device

> sudo file -s /dev/xvdf

Create a file system of type `xfs` on the block device `/dev/xvdf`

> sudo mkfs -t xfs /dev/xvdf

Mount a file system to a mount point, by creating a directory and using that directory as the mount point

> sudo mkdir /ebstest

> sudo mount /dev/xvdf /ebstest

Check all the file systems (on the current instance)

> df -k

To automount after reboot using the UUID of the EBS volume to be mounted

> sudo blkid

> sudo vi /etc/fstab

Perform mount of all the files listed in fstab file

> sudo mount -a

---
