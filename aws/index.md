A basic setup for an AWS Infrastructure using AWS CDK (`AWS Cloud Development Kit`) in TypeScript for resources and configurations mentioned. This includes:

1. **2 EC2 instances (configurable) in an Auto Scaling Group with Load Balancer**.
2. **RDS (Amazon Relational Database Service) instance with user access (R/W)**.
3. **VPC (Virtual Private Cloud) with 1 public and 1 private subnet**.
4. **S3 Bucket** with configurations and a queue (SQS) that gets triggered by S3 events (e.g., S3 object creation).

Before running this script, make sure to have AWS CDK installed and the correct AWS environment configured.

Run the following command to install CDK if not yet installed:

```bash
npm install -g aws-cdk
```

Also, make sure to install dependencies for your CDK project like `S3`, `EC2`, `RDS`, `SQS`, etc:

```bash
npm install @aws-cdk/aws-ec2 @aws-cdk/aws-rds @aws-cdk/aws-s3 @aws-cdk/aws-s3-notifications @aws-cdk/aws-sqs @aws-cdk/aws-autoscaling @aws-cdk/aws-elasticloadbalancingv2 @aws-cdk/aws-iam
```

Here's the `stack.ts` file for this setup:

### `lib/stack.ts`

```ts
import * as cdk from 'aws-cdk-lib';
import { Stack, StackProps } from 'aws-cdk-lib';
import { Construct } from 'constructs';
import * as ec2 from 'aws-cdk-lib/aws-ec2';
import * as autoscaling from 'aws-cdk-lib/aws-autoscaling';
import * as elbv2 from 'aws-cdk-lib/aws-elasticloadbalancingv2';
import * as iam from 'aws-cdk-lib/aws-iam';
import * as rds from 'aws-cdk-lib/aws-rds';
import * as s3 from 'aws-cdk-lib/aws-s3';
import * as sqs from 'aws-cdk-lib/aws-sqs';
import * as s3n from 'aws-cdk-lib/aws-s3-notifications';
 
export class MyStack extends Stack {
  constructor(scope: Construct, id: string, props?: StackProps) {
    super(scope, id, props);
 
    // Create a new VPC with 1 public and 1 private subnet
    const vpc = new ec2.Vpc(this, 'MyVPC', {
      maxAzs: 2, // Specify the number of availability zones
      subnetConfiguration: [
        {
          cidrMask: 24,
          name: 'public-subnet',
          subnetType: ec2.SubnetType.PUBLIC,
        },
        {
          cidrMask: 24,
          name: 'private-subnet',
          subnetType: ec2.SubnetType.PRIVATE_WITH_EGRESS,
        },
      ],
    });
 
    // SECURITY GROUP SETTINGS
    const securityGroup = new ec2.SecurityGroup(this, 'MySG', {
      vpc,
      allowAllOutbound: true,
    });
 
    securityGroup.addIngressRule(ec2.Peer.anyIpv4(), ec2.Port.tcp(22), 'Allow SSH Access');
    securityGroup.addIngressRule(ec2.Peer.anyIpv4(), ec2.Port.tcp(5432), 'Allow PostgreSQL Access');
 
    // Create an auto-scaling group with 2 EC2 instances
    const asg = new autoscaling.AutoScalingGroup(this, 'MyAutoScalingGroup', {
      vpc,
      vpcSubnets: { subnetType: ec2.SubnetType.PUBLIC }, // Auto-scaling instances in the PUBLIC subnet
      instanceType: new ec2.InstanceType('t2.micro'), // Choose your EC2 instance type
      machineImage: new ec2.AmazonLinuxImage(),
      minCapacity: 2,
      maxCapacity: 4,
      keyName: 'my-key-pair', // Update with your EC2 KeyPair
    });
 
    // Create a load balancer
    const lb = new elbv2.ApplicationLoadBalancer(this, 'MyLB', {
      vpc,
      internetFacing: true,
    });
 
    const listener = lb.addListener('Listener', {
      port: 80,
      open: true,
    });
 
    // Attach the auto-scaling group to the load balancer
    listener.addTargets('Target', {
      port: 80,
      targets: [asg],
    });
 
    // RDS PostgreSQL instance
    const dbInstance = new rds.DatabaseInstance(this, 'MyPostgresInstance', {
      engine: rds.DatabaseInstanceEngine.postgres({
        version: rds.PostgresEngineVersion.VER_13_3,
      }),
      vpc,
      vpcSubnets: {
        subnetType: ec2.SubnetType.PRIVATE_WITH_EGRESS,
      },
      credentials: rds.Credentials.fromGeneratedSecret('dbadmin'),
      allocatedStorage: 20, // Specify storage size
      maxAllocatedStorage: 100,
      securityGroups: [securityGroup], // Apply security group for RDS instance
    });
 
    // S3 Bucket
    const bucket = new s3.Bucket(this, 'MyBucket', {
      bucketName: 'my-application-bucket',
      versioned: true,
      removalPolicy: cdk.RemovalPolicy.DESTROY, // Clean up bucket on stack removal
    });
 
    // SQS Queue that will run on S3 events
    const queue = new sqs.Queue(this, 'S3EventQueue', {
      queueName: 's3-event-queue',
    });
 
    // Add an event notification to trigger SQS on S3 "Object Created" events
    bucket.addEventNotification(s3.EventType.OBJECT_CREATED, new s3n.SqsDestination(queue));
 
    // OUTPUT section
    new cdk.CfnOutput(this, 'LoadBalancerDNS', {
      value: lb.loadBalancerDnsName,
    });
 
    new cdk.CfnOutput(this, 'DatabaseEndpoint', {
      value: dbInstance.dbInstanceEndpointAddress,
    });
 
    new cdk.CfnOutput(this, 'BucketName', {
      value: bucket.bucketName,
    });
  }
}
```

### Breakdown

1. **VPC**:
   - Defines a **VPC** with 2 subnets: 1 public and 1 private (for RDS).
   - The public subnet will allow auto-scaling EC2 instances with internet access, while the RDS instance will be in the private subnet for more secure database access.

2. **Auto Scaling Group with Load Balancer**:
   - The **Auto Scaling Group (ASG)** ensures that there are always 2 instances running, and it can scale up to 4 based on traffic.
   - The **Application Load Balancer (ALB)** exposes the instances to the internet on port 80.

3. **Security Group**:
   - A **Security Group** allows SSH access to EC2 instances and also PostgreSQL access on the RDS instance’s port from anywhere (not recommended for production environments where the IP range should be restricted).

4. **RDS**:
   - Creates an **RDS Postgres instance** inside the private subnet with defined engine version, allocated storage, and credentials. The credentials are auto-generated with the `rds.Credentials.fromGeneratedSecret()` method.

5. **S3 Bucket**:
   - Creates a named **S3 bucket** with versioning enabled and a removal policy to automatically delete the bucket when the stack is destroyed.

6. **SQS Queue**:
   - Creates an **SQS queue**, which is configured to receive notifications whenever an object is created in the S3 bucket, using the `addEventNotification()` method.

7. **CloudFormation Output**:
   - The `Outputs` section provides the DNS name of the load balancer, database endpoint, and bucket name once the stack is deployed, making it easy to retrieve important configuration details.

---

### How to Deploy

1. Make sure your AWS credentials are set up (`aws configure`).
2. Initialize a CDK project if it's not yet initialized:

   ```bash
   mkdir cdk-app
   cd cdk-app
   cdk init app --language typescript
   ```

3. Place this TypeScript file inside the `lib` folder (e.g., `lib/stack.ts`).

4. Run the following to bootstrap and deploy the stack:

   ```bash
   cdk bootstrap
   cdk deploy
   ```

### Final Remarks

- This setup is flexible, meaning that you can adjust instance types, scaling policies, RDS engine versions, and bucket configurations based on your project's needs.
- Adjust security group rules and key names according to your environment.

Let me know if you have any questions!
