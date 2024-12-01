# **Comprehensive Notes and Tutorial on AWS Compute Services (EC2, Lambda, Elastic Beanstalk) for the AWS Solutions Architect Exam (with TypeScript Examples)**

---

## **Table of Contents**

1. **Introduction**
   - Overview of Compute Services
   - Importance in AWS Solutions Architect Exam

2. **Amazon EC2 (Elastic Compute Cloud)**
   - Concepts and Terminology
   - Instance Types and Families
   - Security Groups and Key Pairs
   - Elastic IP Addresses
   - Auto Scaling and Load Balancing
   - Placement Groups
   - Pricing Models
   - Hands-On with TypeScript

3. **AWS Lambda**
   - Serverless Computing Overview
   - Lambda Functions and Event-Driven Architecture
   - Function Configuration and Execution
   - Permissions and IAM Roles
   - Integrations with Other AWS Services
   - Versioning and Aliases
   - Lambda Layers and Environment Variables
   - Hands-On with TypeScript

4. **AWS Elastic Beanstalk**
   - PaaS Overview
   - Application Deployment and Environments
   - Supported Platforms
   - Environment Configurations
   - Scaling and Load Balancing
   - Deployment Strategies
   - Hands-On with TypeScript

5. **Comparison and Use Cases**
   - When to Use EC2, Lambda, or Elastic Beanstalk
   - Architectural Considerations

6. **Sample Exam Questions**

7. **Conclusion**

8. **Additional Resources**

---

## **1. Introduction**

### **Overview of Compute Services**

AWS Compute Services provide the processing power, memory, networking, and storage needed to deploy and run applications and services in the cloud. The three primary compute services you'll need to understand for the AWS Solutions Architect exam are:

- **Amazon EC2**: Offers virtual servers to deploy and manage applications.
- **AWS Lambda**: Enables running code without provisioning or managing servers (serverless computing).
- **AWS Elastic Beanstalk**: Provides an easy-to-use service for deploying and scaling web applications and services (Platform as a Service or PaaS).

### **Importance in AWS Solutions Architect Exam**

- **Designing Compute Solutions**: Ability to choose appropriate compute services based on requirements.
- **Scalability and Availability**: Implementing scalable architectures with high availability.
- **Cost Optimization**: Understanding pricing models and choosing cost-effective solutions.
- **Security**: Applying security best practices for compute resources.

---

## **2. Amazon EC2 (Elastic Compute Cloud)**

### **Concepts and Terminology**

- **Amazon EC2**: Web service that provides resizable compute capacity in the cloud.
- **Instances**: Virtual servers running applications.
- **AMI (Amazon Machine Image)**: Template that contains the software configuration (OS, application server, etc.).
- **EBS (Elastic Block Store)**: Provides persistent block storage volumes for use with EC2.

### **Instance Types and Families**

- **General Purpose**: Balanced compute, memory, and networking (e.g., t2, t3).
- **Compute Optimized**: Optimized for compute-intensive tasks (e.g., c5).
- **Memory Optimized**: Optimized for memory-intensive tasks (e.g., r5).
- **Storage Optimized**: High read/write access to large datasets (e.g., i3).
- **Accelerated Computing**: Use of hardware accelerators (e.g., p3 for GPU).

### **Security Groups and Key Pairs**

- **Security Groups**: Virtual firewalls controlling inbound and outbound traffic.
- **Key Pairs**: Securely connect to EC2 instances (SSH keys for Linux, RDP for Windows).

### **Elastic IP Addresses**

- Static public IPv4 addresses associated with your AWS account.
- Can be remapped to different instances.

### **Auto Scaling and Load Balancing**

- **Auto Scaling Groups (ASG)**: Automatically adjust the number of instances based on demand.
- **Elastic Load Balancer (ELB)**: Distributes incoming traffic across multiple instances.

### **Placement Groups**

- Control how instances are placed on underlying hardware.
  - **Cluster**: Low-latency network performance within a single AZ.
  - **Spread**: Instances are placed on distinct hardware.
  - **Partition**: Instances are divided into logical partitions.

### **Pricing Models**

- **On-Demand Instances**: Pay per hour or per second with no long-term commitment.
- **Reserved Instances**: Commit to a term (1 or 3 years) for a significant discount.
- **Spot Instances**: Bid for unused capacity at a discounted rate.
- **Dedicated Hosts**: Physical servers dedicated to a single customer.

### **Hands-On with TypeScript**

#### **Setting Up AWS SDK for JavaScript (v3) with TypeScript**

```bash
npm install @aws-sdk/client-ec2 @types/node
```

#### **Launching an EC2 Instance**

```typescript
import { EC2Client, RunInstancesCommand } from "@aws-sdk/client-ec2";

const ec2Client = new EC2Client({ region: "us-east-1" });

const runInstance = async () => {
  try {
    const params = {
      ImageId: "ami-0abcdef1234567890", // Replace with a valid AMI ID in your region
      InstanceType: "t2.micro",
      MinCount: 1,
      MaxCount: 1,
      KeyName: "my-key-pair", // Replace with your key pair name
      SecurityGroupIds: ["sg-0123456789abcdef0"], // Replace with your security group ID
    };

    const command = new RunInstancesCommand(params);
    const response = await ec2Client.send(command);
    console.log("EC2 Instance Launched:", response.Instances?.[0].InstanceId);
  } catch (error) {
    console.error("Error launching EC2 instance:", error);
  }
};

runInstance();
```

> **Note**: Ensure that you have a valid AMI ID, key pair, and security group ID in your region.

#### **Stopping and Terminating EC2 Instances**

```typescript
import { StopInstancesCommand, TerminateInstancesCommand } from "@aws-sdk/client-ec2";

const stopInstance = async (instanceId: string) => {
  try {
    const command = new StopInstancesCommand({ InstanceIds: [instanceId] });
    const response = await ec2Client.send(command);
    console.log("EC2 Instance Stopped:", response.StoppingInstances);
  } catch (error) {
    console.error("Error stopping EC2 instance:", error);
  }
};

const terminateInstance = async (instanceId: string) => {
  try {
    const command = new TerminateInstancesCommand({ InstanceIds: [instanceId] });
    const response = await ec2Client.send(command);
    console.log("EC2 Instance Terminated:", response.TerminatingInstances);
  } catch (error) {
    console.error("Error terminating EC2 instance:", error);
  }
};

// Usage
stopInstance("i-0123456789abcdef0");
terminateInstance("i-0123456789abcdef0");
```

---

## **3. AWS Lambda**

### **Serverless Computing Overview**

- **Serverless Architecture**: Run code without provisioning or managing servers.
- **Pay per Use**: Billed based on execution time and resource consumption.
- **Event-Driven**: Functions are triggered by events.

### **Lambda Functions and Event-Driven Architecture**

- **Triggers**: Events that cause a Lambda function to execute (e.g., S3 events, API Gateway).
- **Handlers**: The function code that processes the event.

### **Function Configuration and Execution**

- **Runtime Environment**: Node.js, Python, Java, Go, etc.
- **Memory Allocation**: From 128 MB to 10,240 MB.
- **Timeout**: Maximum execution duration (up to 15 minutes).

### **Permissions and IAM Roles**

- **Execution Role**: Lambda functions assume an IAM role granting permissions to AWS services.

### **Integrations with Other AWS Services**

- **API Gateway**: Expose Lambda functions via RESTful APIs.
- **SNS/SQS**: Process messages.
- **EventBridge**: Complex event patterns.

### **Versioning and Aliases**

- **Versioning**: Create snapshots of function code and configuration.
- **Aliases**: Pointers to specific versions; useful for deployment strategies.

### **Lambda Layers and Environment Variables**

- **Layers**: Packages of code and data shared across functions.
- **Environment Variables**: Configuration settings accessible in function code.

### **Hands-On with TypeScript**

#### **Setting Up AWS Lambda with TypeScript**

Create a Lambda function using TypeScript and the AWS SDK.

1. **Initialize a Node.js Project**

```bash
mkdir lambda-typescript-example
cd lambda-typescript-example
npm init -y
npm install typescript @types/node
npx tsc --init
```

2. **Install Dependencies**

```bash
npm install aws-sdk
```

3. **Write the Lambda Function**

Create a file `index.ts`:

```typescript
import { S3 } from "aws-sdk";

export const handler = async (event: any): Promise<any> => {
  const s3 = new S3();
  const buckets = await s3.listBuckets().promise();

  return {
    statusCode: 200,
    body: JSON.stringify({ buckets: buckets.Buckets }),
  };
};
```

4. **Compile to JavaScript**

```bash
npx tsc
```

5. **Deploying the Lambda Function**

Use the AWS Console or AWS CLI to create the Lambda function:

```bash
aws lambda create-function \
  --function-name MyTypeScriptFunction \
  --runtime nodejs14.x \
  --handler index.handler \
  --role arn:aws:iam::123456789012:role/MyLambdaRole \
  --zip-file fileb://function.zip
```

> **Note**: Package the compiled JavaScript files into `function.zip`.

#### **Connecting Lambda to API Gateway**

```typescript
// Update the Lambda function to handle API Gateway events
export const handler = async (event: any): Promise<any> => {
  const name = event.queryStringParameters?.name || "World";
  return {
    statusCode: 200,
    body: JSON.stringify({ message: `Hello, ${name}!` }),
  };
};
```

---

## **4. AWS Elastic Beanstalk**

### **PaaS Overview**

- **Managed Service**: Simplifies application deployment and management.
- **Supports Multiple Platforms**: Node.js, Python, Java, .NET, PHP, etc.
- **Underlying Infrastructure**: AWS handles resources like EC2, ELB, Auto Scaling.

### **Application Deployment and Environments**

- **Application**: Top-level container for environments.
- **Environment**: Version of the application deployed on AWS resources.
  - **Web Server Environment**: Handles HTTP requests.
  - **Worker Environment**: Processes background tasks.

### **Supported Platforms**

- **Preconfigured Platforms**: Include language runtime and web servers.
- **Custom Platforms**: Use if preconfigured platforms don't meet requirements.

### **Environment Configurations**

- **Configuration Files**: YAML or JSON files (`.ebextensions` directory).
- **Environment Variables**: Set configuration settings accessible by the application.
- **Resource Configuration**: Modify the underlying AWS resources.

### **Scaling and Load Balancing**

- **Auto Scaling**: Automatically scale instances based on demand.
- **Load Balancers**: ELB distributes traffic.

### **Deployment Strategies**

- **All at Once**: Deploys changes to all instances simultaneously.
- **Rolling**: Deploys changes in batches.
- **Rolling with Additional Batch**: Ensures capacity during deployment.
- **Immutable**: Deploys to new instances, swaps them in.

### **Hands-On with TypeScript**

Deploy a Node.js application written in TypeScript to Elastic Beanstalk.

#### **Create a Node.js Application with TypeScript**

1. **Initialize Project**

```bash
mkdir beanstalk-typescript-app
cd beanstalk-typescript-app
npm init -y
npm install express typescript ts-node @types/express
npx tsc --init
```

2. **Write an Express Server in TypeScript**

Create `src/index.ts`:

```typescript
import express, { Request, Response } from "express";

const app = express();
const port = process.env.PORT || 3000;

app.get("/", (req: Request, res: Response) => {
  res.send("Hello from Elastic Beanstalk with TypeScript!");
});

app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});
```

3. **Add Build Script in `package.json`**

```json
"scripts": {
  "start": "node dist/index.js",
  "build": "tsc"
}
```

4. **Compile TypeScript**

```bash
npm run build
```

#### **Deploying to Elastic Beanstalk**

1. **Install Elastic Beanstalk CLI**

```bash
pip install awsebcli --upgrade --user
```

2. **Initialize Elastic Beanstalk Application**

```bash
eb init -p node.js my-typescript-app
```

3. **Create an Environment**

```bash
eb create my-typescript-env
```

4. **Deploy the Application**

```bash
eb deploy
```

#### **Configuring Elastic Beanstalk**

- **Environment Variables**

  Create a file `.ebextensions/options.config`:

  ```yaml
  option_settings:
    aws:elasticbeanstalk:application:environment:
      NODE_ENV: production
  ```

- **Set the Start Command**

  In `package.json`, specify the `start` script.

- **Ensure the Application Listens on the Correct Port**

  Use the `PORT` environment variable provided by Elastic Beanstalk.

---

## **5. Comparison and Use Cases**

### **When to Use EC2**

- **Legacy Applications**: Require full control over the OS.
- **Custom Environments**: Specialized software or configurations.
- **Long-Running Processes**: Applications that need persistent state or storage.

### **When to Use Lambda**

- **Event-Driven Processing**: Responding to events (e.g., file uploads, messages).
- **Microservices Architecture**: Small, independent functions.
- **Cost Optimization**: Infrequent workloads; pay-per-execution model.

### **When to Use Elastic Beanstalk**

- **Simplified Deployment**: Manage application lifecycle without deep AWS expertise.
- **Standard Web Applications**: Deploying common web app stacks.
- **Rapid Development and Testing**: Quickly spin up environments.

### **Architectural Considerations**

- **Scalability**: All services support scaling, but the mechanisms differ.
- **Flexibility vs. Management Overhead**: EC2 offers flexibility at the cost of more management.
- **Cost Model**: Consider on-demand vs. pay-per-use pricing.
- **Integration with Other Services**: Lambda integrates tightly with AWS services.

---

## **6. Sample Exam Questions**

1. **Question**: An organization needs to run a web application that requires a relational database and the ability to scale to millions of users. They want minimal management overhead. Which AWS service combination is the most appropriate?

   - A) Amazon EC2 with an RDS database
   - B) AWS Elastic Beanstalk with an RDS database
   - C) AWS Lambda with Amazon DynamoDB
   - D) Amazon EC2 with a self-managed database on the instance

   **Answer**: **B**

2. **Question**: A company wants to build a serverless application triggered by image uploads to an S3 bucket. Which AWS service should they use to process the images?

   - A) Amazon EC2
   - B) AWS Lambda
   - C) AWS Elastic Beanstalk
   - D) Amazon ECS

   **Answer**: **B**

3. **Question**: You need to run a computing workload that is stateless and highly parallelizable. Cost optimization is important, and interruptions are acceptable. Which EC2 instance pricing model is most suitable?

   - A) On-Demand Instances
   - B) Reserved Instances
   - C) Spot Instances
   - D) Dedicated Hosts

   **Answer**: **C**

---

## **7. Conclusion**

Understanding AWS Compute Services is critical for designing scalable, cost-effective, and efficient architectures on AWS. As a solutions architect, you should be able to select the appropriate compute service based on the application's requirements, operational considerations, and business objectives.

---

## **8. Additional Resources**

- **AWS Documentation**
  - EC2: [Amazon EC2 Documentation](https://docs.aws.amazon.com/ec2/index.html)
  - Lambda: [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
  - Elastic Beanstalk: [AWS Elastic Beanstalk Developer Guide](https://docs.aws.amazon.com/elastic-beanstalk/index.html)

- **AWS Whitepapers**
  - [AWS Well-Architected Framework](https://d1.awsstatic.com/whitepapers/architecture/AWS_Well-Architected_Framework.pdf)
  - [Serverless Architectures with AWS Lambda](https://d1.awsstatic.com/whitepapers/serverless-architectures-with-aws-lambda.pdf)

- **AWS Training**
  - [AWS Solutions Architect Learning Path](https://aws.amazon.com/training/learning-paths/architect/)

---

## **Tips for the Exam**

- **Understand Service Limits and Quotas**: Be aware of default limits and how to request increases.
- **Know the Integration Points**: How compute services interact with other AWS services.
- **Consider Security Implications**: IAM roles, security groups, VPC configurations.
- **Practice Hands-On Labs**: Gain practical experience to reinforce concepts.
- **Stay Updated**: AWS services evolve; ensure you're familiar with the latest features.

---

**Good luck with your AWS Solutions Architect exam preparation!**