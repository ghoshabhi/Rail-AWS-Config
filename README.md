# Step By Step Guide to Deploy a Rails App to AWS EC2

This documentation takes you through the process of configuration required to host a Rails application on a EC2 instance. The tutorial uses [Nginx](https://www.nginx.com/resources/wiki/) + [Passenger](https://www.phusionpassenger.com/library/) on the server. The sample application used in this guide can be found [here](https://github.com/ghoshabhi/Sample-Photo-Webapp)

# A) Creating a new AWS EC2 instance

1. Open [AWS Console](https://aws.amazon.com/console/)
2. After you signin into your developer account, you should see a list of AWS services. From the the services find **Compute** and select **EC2** from there. 
![aws_step_1](https://raw.githubusercontent.com/ghoshabhi/cdn/master/AWS_1.png "AWS Dashboard")

3. Select **Launch Instance** from the EC2 dashboard: 
![aws_step_2](https://github.com/ghoshabhi/cdn/blob/master/AWS_2.png?raw=true "Launch Instance")

4. Choose `Ubuntu Server 16.04 LTS` from the list of AMI (Amazon Machine Image):
![aws_step_3](https://github.com/ghoshabhi/cdn/blob/master/AWS_3.png?raw=true)

5. Choose an _instance type_. For purposes of this demo - choose `t2.micro`: 
![aws_step_4](https://github.com/ghoshabhi/cdn/blob/master/AWS_4.png?raw=true)

6. Keep on clicking _Next: ...._ from Step 3 to Step 5 in the dashboard. On _"Step 6: Configure A Security Group"_, there:
   - Click on `Add Rule` button and add a new rule for `HTTP` by selecting `HTTP` from the dropdown. 
   - In the **Source** column click on the dropdown and choose **Anywhere** so that you can ssh into your instance from any local machine. 
  At the end of this step, your console should look something like this:
  ![aws_step_5](https://github.com/ghoshabhi/cdn/blob/master/AWS_4.png?raw=true)
