# **Comprehensive Notes and Tutorial on AWS Storage Services (S3, EBS, EFS, CloudFront) for the AWS Solutions Architect Exam (with TypeScript Examples)**

---

## **Table of Contents**

1. **Introduction**
   - Overview of Storage Services
   - Importance in AWS Solutions Architect Exam

2. **Amazon S3 (Simple Storage Service)**
   - Concepts and Terminology
   - Buckets and Objects
   - Storage Classes
   - Versioning and Lifecycle Policies
   - Security and Access Control
   - Encryption
   - Data Consistency Model
   - Cross-Region Replication
   - Hands-On with TypeScript

3. **Amazon EBS (Elastic Block Store)**
   - Concepts and Types
   - Volume Types
   - Snapshots and AMIs
   - Encryption
   - Performance Considerations
   - Hands-On with TypeScript

4. **Amazon EFS (Elastic File System)**
   - Concepts and Use Cases
   - Performance Modes
   - Throughput Modes
   - Security and Access Control
   - Mounting EFS on EC2 Instances
   - Hands-On with TypeScript

5. **Amazon CloudFront**
   - Content Delivery Network Overview
   - Distributions, Origins, and Behaviors
   - Caching and TTL
   - Signed URLs and Signed Cookies
   - Integration with Other AWS Services
   - Hands-On with TypeScript

6. **Comparison and Use Cases**
   - When to Use S3, EBS, EFS, or CloudFront
   - Architectural Considerations

7. **Sample Exam Questions**

8. **Conclusion**

9. **Additional Resources**

---

## **1. Introduction**

### **Overview of Storage Services**

AWS offers a variety of storage services designed to meet different storage requirements:

- **Amazon S3**: Object storage built to store and retrieve any amount of data from anywhere.
- **Amazon EBS**: Provides block-level storage volumes for use with EC2 instances.
- **Amazon EFS**: Provides a fully managed, scalable, elastic, shared file system for use with AWS Cloud services and on-premises resources.
- **Amazon CloudFront**: A fast content delivery network (CDN) service that securely delivers data, videos, applications, and APIs to customers globally.

### **Importance in AWS Solutions Architect Exam**

Understanding AWS storage services is crucial for the AWS Solutions Architect exam:

- **Designing Storage Solutions**: Selecting appropriate storage solutions based on use cases.
- **Cost Optimization**: Balancing performance, durability, and cost.
- **Security and Compliance**: Implementing secure storage architectures.
- **Integration**: Knowing how storage services integrate with other AWS services.

---

## **2. Amazon S3 (Simple Storage Service)**

### **Concepts and Terminology**

- **Buckets**: Containers for objects stored in S3; globally unique names.
- **Objects**: Files stored in buckets, identified by a key (unique within a bucket).
- **Keys**: The object name, including the path if using a folder structure.
- **Regions**: S3 buckets are created in a specific AWS region.

### **Buckets and Objects**

- **Creating Buckets**: Buckets store objects and cannot be nested.
- **Uploading Objects**: Upload any type of file; maximum size of a single object is 5TB.
- **Data Consistency Model**: Read-after-write consistency for PUTs of new objects; eventual consistency for overwrite PUTs and DELETEs.

### **Storage Classes**

- **Standard**: High durability, availability, and performance.
- **Standard-IA (Infrequent Access)**: Lower cost for data accessed less frequently.
- **One Zone-IA**: Like Standard-IA but stored in a single AZ.
- **Glacier Instant Retrieval**: For archived data requiring immediate access.
- **Glacier Flexible Retrieval**: Low-cost storage for archived data with retrieval times ranging from minutes to hours.
- **Glacier Deep Archive**: Lowest cost for archival with retrieval times within 12 hours.

### **Versioning and Lifecycle Policies**

- **Versioning**: Keeps multiple versions of an object to protect against accidental overwrites or deletions.
- **Lifecycle Policies**: Automate moving objects between storage classes or expiring objects after a set period.

### **Security and Access Control**

- **Bucket Policies**: JSON-based policies attached to buckets to manage permissions.
- **Access Control Lists (ACLs)**: Legacy method; grants basic read/write permissions.
- **IAM Policies**: Attach to users, groups, or roles to control S3 access.
- **S3 Access Points**: Simplify managing data access at scale.

### **Encryption**

- **Server-Side Encryption (SSE)**:
  - **SSE-S3**: S3-managed keys.
  - **SSE-KMS**: AWS Key Management Service keys.
  - **SSE-C**: Customer-provided keys.
- **Client-Side Encryption**: Encrypt data before uploading to S3.

### **Data Consistency Model**

- **Read-after-write consistency** for new objects.
- **Eventual consistency** for overwrites and deletes.

### **Cross-Region Replication**

- Replicates objects asynchronously across AWS regions.
- Requires versioning to be enabled on source and destination buckets.

### **Hands-On with TypeScript**

#### **Setting Up AWS SDK for JavaScript (v3) with TypeScript**

```bash
npm install @aws-sdk/client-s3 @types/node
```

#### **Creating a Bucket**

```typescript
import { S3Client, CreateBucketCommand } from "@aws-sdk/client-s3";

const s3Client = new S3Client({ region: "us-east-1" });

const createBucket = async (bucketName: string) => {
  try {
    const data = await s3Client.send(new CreateBucketCommand({ Bucket: bucketName }));
    console.log(`Bucket ${bucketName} created successfully`, data.Location);
  } catch (err) {
    console.error("Error creating bucket:", err);
  }
};

createBucket("my-unique-bucket-name-123456");
```

#### **Uploading an Object**

```typescript
import { PutObjectCommand } from "@aws-sdk/client-s3";
import { readFileSync } from "fs";

const uploadObject = async (bucketName: string, key: string, filePath: string) => {
  const fileContent = readFileSync(filePath);
  try {
    await s3Client.send(
      new PutObjectCommand({
        Bucket: bucketName,
        Key: key,
        Body: fileContent,
      })
    );
    console.log(`File uploaded successfully to ${bucketName}/${key}`);
  } catch (err) {
    console.error("Error uploading file:", err);
  }
};

uploadObject("my-unique-bucket-name-123456", "folder/hello.txt", "./hello.txt");
```

#### **Enabling Bucket Versioning**

```typescript
import { S3Client, PutBucketVersioningCommand } from "@aws-sdk/client-s3";

const enableVersioning = async (bucketName: string) => {
  try {
    await s3Client.send(
      new PutBucketVersioningCommand({
        Bucket: bucketName,
        VersioningConfiguration: {
          Status: "Enabled",
        },
      })
    );
    console.log(`Versioning enabled on bucket ${bucketName}`);
  } catch (err) {
    console.error("Error enabling versioning:", err);
  }
};

enableVersioning("my-unique-bucket-name-123456");
```

#### **Setting Bucket Policies**

```typescript
import { PutBucketPolicyCommand } from "@aws-sdk/client-s3";

const setBucketPolicy = async (bucketName: string) => {
  const policy = {
    Version: "2012-10-17",
    Statement: [
      {
        Sid: "PublicReadGetObject",
        Effect: "Allow",
        Principal: "*",
        Action: ["s3:GetObject"],
        Resource: [`arn:aws:s3:::${bucketName}/*`],
      },
    ],
  };

  try {
    await s3Client.send(
      new PutBucketPolicyCommand({
        Bucket: bucketName,
        Policy: JSON.stringify(policy),
      })
    );
    console.log(`Bucket policy set for ${bucketName}`);
  } catch (err) {
    console.error("Error setting bucket policy:", err);
  }
};

setBucketPolicy("my-unique-bucket-name-123456");
```

---

## **3. Amazon EBS (Elastic Block Store)**

### **Concepts and Types**

- **EBS Volumes**: Durable, block-level storage devices that you can attach to your EC2 instances.
- **Types of EBS Volumes**:
  - **SSD-backed volumes**: General Purpose SSD (gp2, gp3), Provisioned IOPS SSD (io1, io2).
  - **HDD-backed volumes**: Throughput Optimized HDD (st1), Cold HDD (sc1).

### **Volume Types**

- **General Purpose SSD (gp2, gp3)**:
  - **gp2**: Offers up to 16,000 IOPS.
  - **gp3**: Offers baseline performance with the ability to provision additional IOPS and throughput.
- **Provisioned IOPS SSD (io1, io2)**:
  - Designed for I/O-intensive workloads requiring low latency.
  - Can provision up to 64,000 IOPS per volume (io2 Block Express offers higher).
- **Throughput Optimized HDD (st1)**:
  - Low-cost HDD volume designed for frequently accessed, throughput-intensive workloads.
- **Cold HDD (sc1)**:
  - Lowest cost HDD volume designed for less frequently accessed data.

### **Snapshots and AMIs**

- **Snapshots**: Point-in-time backups of EBS volumes stored in S3.
- **AMIs (Amazon Machine Images)**: Templates for EC2 instances that include EBS snapshots.

### **Encryption**

- EBS volumes support encryption:
  - Data at rest is encrypted inside the volume.
  - Data in transit between the volume and the instance is encrypted.
  - Snapshots and volumes created from encrypted volumes are also encrypted.

### **Performance Considerations**

- **IOPS**: Input/output operations per second.
- **Throughput**: Measured in MiB/s.
- **Optimization**: Choose the appropriate volume type based on performance requirements.

### **Hands-On with TypeScript**

#### **Installing AWS SDK**

```bash
npm install @aws-sdk/client-ec2
```

#### **Creating an EBS Volume**

```typescript
import { EC2Client, CreateVolumeCommand } from "@aws-sdk/client-ec2";

const ec2Client = new EC2Client({ region: "us-east-1" });

const createVolume = async () => {
  try {
    const params = {
      AvailabilityZone: "us-east-1a",
      Size: 10, // Size in GiB
      VolumeType: "gp2",
    };

    const data = await ec2Client.send(new CreateVolumeCommand(params));
    console.log(`Volume created: ${data.VolumeId}`);
  } catch (err) {
    console.error("Error creating volume:", err);
  }
};

createVolume();
```

#### **Attaching an EBS Volume to an EC2 Instance**

```typescript
import { AttachVolumeCommand } from "@aws-sdk/client-ec2";

const attachVolume = async (volumeId: string, instanceId: string, device: string) => {
  try {
    const params = {
      VolumeId: volumeId,
      InstanceId: instanceId,
      Device: device, // e.g., '/dev/sdf'
    };

    const data = await ec2Client.send(new AttachVolumeCommand(params));
    console.log(`Volume ${volumeId} attached to instance ${instanceId}`);
  } catch (err) {
    console.error("Error attaching volume:", err);
  }
};

attachVolume("vol-0123456789abcdef0", "i-0123456789abcdef0", "/dev/sdf");
```

#### **Creating a Snapshot**

```typescript
import { CreateSnapshotCommand } from "@aws-sdk/client-ec2";

const createSnapshot = async (volumeId: string) => {
  try {
    const data = await ec2Client.send(new CreateSnapshotCommand({ VolumeId: volumeId }));
    console.log(`Snapshot created: ${data.SnapshotId}`);
  } catch (err) {
    console.error("Error creating snapshot:", err);
  }
};

createSnapshot("vol-0123456789abcdef0");
```

---

## **4. Amazon EFS (Elastic File System)**

### **Concepts and Use Cases**

- **Shared File Storage**: Provides a scalable, shared file system for use with EC2 instances.
- **POSIX-Compliant**: Compatible with standard Linux file system APIs and file system permissions.
- **Use Cases**: Big data and analytics, media processing workflows, content management.

### **Performance Modes**

- **General Purpose Mode**: Ideal for latency-sensitive use cases (default).
- **Max I/O Mode**: Optimizes for higher aggregate throughput and IOPS; slightly higher latencies.

### **Throughput Modes**

- **Bursting Throughput (default)**: Scales with file system size.
- **Provisioned Throughput**: Specify throughput independent of storage size.

### **Security and Access Control**

- **Security Groups**: Control network traffic to and from EFS.
- **IAM Policies**: Manage permissions for EFS actions.
- **Encryption**:
  - **At Rest**: Encrypt data stored in EFS.
  - **In Transit**: Encrypt data as it travels between clients and EFS.

### **Mounting EFS on EC2 Instances**

- **Mount Targets**: Set up in each VPC subnet.
- **NFSv4.1 Protocol**: Use NFS clients to connect.
- **Amazon EFS Mount Helper**: Simplifies mounting.

### **Hands-On with TypeScript**

#### **Installing AWS SDK**

```bash
npm install @aws-sdk/client-efs
```

#### **Creating an EFS File System**

```typescript
import { EFSClient, CreateFileSystemCommand } from "@aws-sdk/client-efs";

const efsClient = new EFSClient({ region: "us-east-1" });

const createEFS = async () => {
  try {
    const data = await efsClient.send(new CreateFileSystemCommand({}));
    console.log(`EFS File System created: ${data.FileSystemId}`);
  } catch (err) {
    console.error("Error creating EFS file system:", err);
  }
};

createEFS();
```

#### **Creating Mount Targets**

```typescript
import { CreateMountTargetCommand } from "@aws-sdk/client-efs";

const createMountTarget = async (
  fileSystemId: string,
  subnetId: string,
  securityGroups: string[]
) => {
  try {
    const data = await efsClient.send(
      new CreateMountTargetCommand({
        FileSystemId: fileSystemId,
        SubnetId: subnetId,
        SecurityGroups: securityGroups,
      })
    );
    console.log(`Mount target created: ${data.MountTargetId}`);
  } catch (err) {
    console.error("Error creating mount target:", err);
  }
};

createMountTarget("fs-01234567", "subnet-0123456789abcdef0", ["sg-0123456789abcdef0"]);
```

#### **Mounting EFS on an EC2 Instance**

Mounting is performed from the EC2 instance's terminal:

```bash
sudo yum install -y amazon-efs-utils
sudo mkdir /mnt/efs
sudo mount -t efs fs-01234567:/ /mnt/efs
```

---

## **5. Amazon CloudFront**

### **Content Delivery Network Overview**

- **CloudFront**: Distributes content through a network of edge locations globally.
- **Reduces Latency**: Delivers content to users with low latency.

### **Distributions, Origins, and Behaviors**

- **Distributions**: Configurations for delivering content (web or streaming).
- **Origins**: The source of the content (S3 bucket, HTTP server, etc.).
- **Behaviors**: Control how CloudFront handles requests (caching settings, allowed HTTP methods).

### **Caching and TTL**

- **Cache Control**: Use headers to manage caching.
- **Time to Live (TTL)**: Determines how long objects are cached.

### **Signed URLs and Signed Cookies**

- **Restrict Access**: Control who can access content.
- **Signed URLs**: Grant specific users access to content.
- **Signed Cookies**: Grant access to multiple restricted files.

### **Integration with Other AWS Services**

- **S3 Origin Access Identity (OAI)**: Allows CloudFront to access private S3 content.
- **Lambda@Edge**: Run code closer to users to customize content delivery.

### **Hands-On with TypeScript**

#### **Installing AWS SDK**

```bash
npm install @aws-sdk/client-cloudfront
```

#### **Creating a CloudFront Distribution for an S3 Bucket**

```typescript
import { CloudFrontClient, CreateDistributionCommand } from "@aws-sdk/client-cloudfront";

const cloudFrontClient = new CloudFrontClient({ region: "us-east-1" });

const createDistribution = async (s3BucketName: string, oaiId: string) => {
  try {
    const params = {
      DistributionConfig: {
        CallerReference: `${Date.now()}`,
        Origins: {
          Quantity: 1,
          Items: [
            {
              Id: s3BucketName,
              DomainName: `${s3BucketName}.s3.amazonaws.com`,
              S3OriginConfig: {
                OriginAccessIdentity: `origin-access-identity/cloudfront/${oaiId}`,
              },
            },
          ],
        },
        DefaultCacheBehavior: {
          TargetOriginId: s3BucketName,
          ViewerProtocolPolicy: "redirect-to-https",
          ForwardedValues: {
            QueryString: false,
            Cookies: {
              Forward: "none",
            },
          },
          TrustedSigners: {
            Enabled: false,
            Quantity: 0,
          },
          MinTTL: 0,
        },
        Enabled: true,
      },
    };

    const data = await cloudFrontClient.send(new CreateDistributionCommand(params));
    console.log(`CloudFront Distribution created: ${data.Distribution?.Id}`);
  } catch (err) {
    console.error("Error creating CloudFront distribution:", err);
  }
};

createDistribution("my-unique-bucket-name-123456", "E1ABCDEFGHIJKL");
```

> **Note**: You need to create an Origin Access Identity (OAI) and replace `oaiId` with its ID.

#### **Invalidating Cached Objects**

```typescript
import { CreateInvalidationCommand } from "@aws-sdk/client-cloudfront";

const invalidateCache = async (distributionId: string, paths: string[]) => {
  try {
    const params = {
      DistributionId: distributionId,
      InvalidationBatch: {
        CallerReference: `${Date.now()}`,
        Paths: {
          Quantity: paths.length,
          Items: paths,
        },
      },
    };
    const data = await cloudFrontClient.send(new CreateInvalidationCommand(params));
    console.log(`Invalidation created: ${data.Invalidation?.Id}`);
  } catch (err) {
    console.error("Error creating invalidation:", err);
  }
};

invalidateCache("E1ABCDEFGHIJKL", ["/folder/*"]);
```

---

## **6. Comparison and Use Cases**

### **When to Use S3**

- **Data Storage and Backup**: Durable and scalable storage for any type of data.
- **Static Website Hosting**: Serve static content directly from S3.
- **Big Data Analytics**: Store data for processing with services like EMR, Athena.

### **When to Use EBS**

- **Block Storage for EC2**: Persistent storage volumes for EC2 instances.
- **Databases and Applications**: Use cases requiring low-latency block storage.

### **When to Use EFS**

- **Shared File Storage**: Multiple EC2 instances need access to the same files.
- **Big Data and Analytics**: Scalable storage for data processing.

### **When to Use CloudFront**

- **Content Delivery**: Distribute content globally with low latency.
- **Security**: Protect content with SSL, signed URLs, and WAF integration.
- **Web Applications**: Improve performance by caching static content at edge locations.

### **Architectural Considerations**

- **Performance**: Choose storage based on latency and throughput requirements.
- **Durability and Availability**: S3 offers 99.999999999% (11 nines) durability.
- **Cost Optimization**: Select appropriate storage classes and services to balance cost and performance.
- **Scalability**: Services like S3 and EFS automatically scale with your data.

---

## **7. Sample Exam Questions**

1. **Question**: A company needs to store large amounts of data with infrequent access but requires rapid retrieval when needed. Which S3 storage class is most appropriate?

   - A) S3 Standard
   - B) S3 Standard-Infrequent Access (Standard-IA)
   - C) S3 One Zone-Infrequent Access
   - D) S3 Glacier Instant Retrieval

   **Answer**: **D**

2. **Question**: An application running on multiple EC2 instances requires shared access to a common file system. Which AWS storage service should you recommend?

   - A) Amazon EBS
   - B) Amazon S3
   - C) Amazon EFS
   - D) Amazon DynamoDB

   **Answer**: **C**

3. **Question**: You want to serve dynamic content with low latency to users worldwide while protecting the origin against DDoS attacks. Which service should you use?

   - A) Amazon S3 with Transfer Acceleration
   - B) Amazon CloudFront with AWS Shield
   - C) Elastic Load Balancer with Auto Scaling
   - D) Amazon EFS with CloudWatch

   **Answer**: **B**

4. **Question**: An application requires block storage for an EC2 instance running a database that demands high IOPS. Which EBS volume type should you choose?

   - A) General Purpose SSD (gp2)
   - B) Provisioned IOPS SSD (io1/io2)
   - C) Throughput Optimized HDD (st1)
   - D) Cold HDD (sc1)

   **Answer**: **B**

---

## **8. Conclusion**

AWS offers a comprehensive suite of storage services tailored to various use cases. As a Solutions Architect, understanding the features, limitations, and appropriate use cases of S3, EBS, EFS, and CloudFront is crucial for designing robust, scalable, and cost-effective architectures on AWS.

---

## **9. Additional Resources**

- **AWS Documentation**
  - S3: [Amazon S3 Documentation](https://docs.aws.amazon.com/s3/index.html)
  - EBS: [Amazon EBS Documentation](https://docs.aws.amazon.com/ebs/index.html)
  - EFS: [Amazon EFS Documentation](https://docs.aws.amazon.com/efs/index.html)
  - CloudFront: [Amazon CloudFront Documentation](https://docs.aws.amazon.com/cloudfront/index.html)

- **AWS Whitepapers**
  - [Storage Options in the Cloud](https://d1.awsstatic.com/whitepapers/aws-storage-services-whitepaper.pdf)
  - [Amazon S3 Security](https://d1.awsstatic.com/whitepapers/Amazon-S3-Security.pdf)
  - [AWS Well-Architected Framework – Storage Pillar](https://docs.aws.amazon.com/wellarchitected/latest/storage-pillars/welcome.html)

- **AWS Training**
  - [AWS Solutions Architect Learning Path](https://aws.amazon.com/training/learning-paths/architect/)

---

## **Tips for the Exam**

- **Understand the Characteristics**: Know the features, performance, and cost differences between S3, EBS, EFS, and CloudFront.
- **Security Best Practices**: Be familiar with securing data at rest and in transit, IAM policies, bucket policies, and encryption options.
- **Use Cases**: Be prepared to recommend storage solutions based on specific requirements.
- **Scaling and Performance**: Understand how each service scales and how to optimize performance.
- **Integration**: Know how storage services integrate with other AWS services like EC2, Lambda, and CloudFront.

---

**Good luck with your AWS Solutions Architect exam preparation!**