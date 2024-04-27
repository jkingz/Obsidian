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

#### Add the CloudWatch Role to the instance
	1. Head over to ec2 and select the instance in which you want to configure the custom logs.
	2. Right-click for options and select Security and then choose Modify IAM ROLE option.
	3. Select the custom cloud watch IAM role from the dropdown and save it.

#### Install CloudWatch Logs Agent
	SSH into the ec2 instance and follow the steps given below:
	 wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
	sudo dpkg -i amazon-cloudwatch-agent.deb

##### If you are just starting with cloud watch agent, it is better to run the cloud watch agent wizard that helps you create the log agent configuration.

	 sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard

The final config files get stored in the following location

	 /opt/aws/amazon-cloudwatch-agent/bin/config.json

If you want to collect the system metrics, install collected on your server.

	 sudo apt-get update -y 
	 sudo apt-get install collectd

Now let start the CloudWatch agent using the following command:

	 sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json -s

You can check the agent status using the following command.

	 sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a status

![IAM](https://devopscube.com/wp-content/uploads/2021/08/image-20.png)

### Validating Custom Logs in Cloudwatch Dashboard
Once the setup is done, you can view all the configured logs under the cloudwatch dashboard (under the logs option)

	1. Go to Logs –> Log Groups and you will see the log group you mentioned in the agent configuration.
	2. Select the log group and you should see the instance identified you mentioned in the config.
	3. If you click the instance identifier, it shows all the logs. You can use the cloud watch filter option to filter and query required logs.

![IAM](https://devopscube.com/wp-content/uploads/2020/04/image-9.png)