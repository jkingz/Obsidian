# TABLE OF CONTENTS
	1. AWS Cloud Watch Agent Use Cases
	2. Application Logs To Aws CloudWatch WorkFlow
	3. Create an IAM ROLE for CloudWatch Agent
	4. Add the CloudWatch Role to the instance
	5. Install CloudWatch Logs Agent
	6. Configure CloudWatch Agent
---
# AWS Cloud Watch Agent Use Cases
#### AWS CloudWatch log service has the capability to store custom logs and process metric from your application instances. Here are some example use cases for custom logs and metrics.

	1. Web Servers(Nginx, Apache, etc) access or error logs can be pushed to CloudWatch logs it acts as central log management for your applications running AWS
	2. Custom application logs (Python, Java, etc) can be pushed to cloudwatch and you can set up custom dashboard and alerts base on log patterns.
	3. Ec2 instance metrics/custom system metrics/ app can be pushed to cloudwatch
---
# Application Logs to AWS CloudWatch Workflow
#### You can send logs from any number of Ec2 source to CloudWatch. All you need to have is a CloudWatch agent running on your instance.

### Here is what you have to do
	1. Create a custom ec2 IAM ROLE with CloudWatch log write access
	2. Install CloudWatch logs ec2 agent 
	3. Configure log source in the cloudwatch agent configuration file
	4. Start the agent with the configuration file
	5. Validate logs in the cloudwatch dashboard.
![IAM](https://devopscube.com/wp-content/uploads/2020/04/app_cloudwatch_logs-min-1.png)
---
# Create an IAM role for CloudWatch Agent
#### To setup aws cutom logs, first, you need to create and add a custom ec2 IAM role to your instance. This IAM role will have policies with write access to the CloudWatch service so that all the logs from ec2 instances can be shipped to CloudWatch.

##### Steps:
	1. Head over to AWS IAM -> Policies -> Create Policy
![IAM](https://devopscube.com/wp-content/uploads/2021/09/image-7.png)
