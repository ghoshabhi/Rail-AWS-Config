# Step By Step Guide to Deploy a Rails App to AWS EC2

This documentation takes you through the process of configuration required to host a Rails application on a EC2 instance. The tutorial uses [Nginx](https://www.nginx.com/resources/wiki/) + [Passenger](https://www.phusionpassenger.com/library/) on the server. The sample application used in this guide can be found [here](https://github.com/ghoshabhi/Sample-Photo-Webapp)

# A) Creating a new AWS EC2 instance

1. Open [AWS Console](https://aws.amazon.com/console/)
2. After you signin into your developer account, you should see a list of AWS services. From the the services find **Compute** and select **EC2** from there. 
![aws_step_1](https://raw.githubusercontent.com/ghoshabhi/cdn/master/AWS_1.png "AWS Dashboard")

3. Select **Launch Instance** from the EC2 dashboard: 
![aws_step_2](https://github.com/ghoshabhi/cdn/blob/master/AWS_2.png?raw=true "Launch Instance")

4. Choose `Ubuntu Server 16.04 LTS` from the list of AMI (Amazon Machine Image):
![aws_step_3](https://github.com/ghoshabhi/cdn/blob/master/AWS_3.png?raw=true "Choose AMI")

5. Choose an _instance type_. For purposes of this demo - choose `t2.micro`: 
![aws_step_4](https://github.com/ghoshabhi/cdn/blob/master/AWS_4.png?raw=true "t2.micro")

6. Keep on clicking _Next: ...._ from Step 3 to Step 5 in the dashboard. On _"Step 6: Configure A Security Group"_, there:
   - Click on `Add Rule` button and add a new rule for `HTTP` by selecting `HTTP` from the dropdown. 
   - In the **Source** column click on the dropdown and choose **Anywhere** so that you can ssh into your instance from any local machine. 
  
  At the end of this step, your console should look something like this:
  ![aws_step_5](https://github.com/ghoshabhi/cdn/blob/master/AWS_5.png?raw=true "Add http rule")

7. Click on **Review and Launch** and then click on **Launch** on next screen. You will prompted to create a new publick key which will be used to SSH into your instance. After your create a new public key, hit **Download Key Pair**.
![aws_step_6](https://github.com/ghoshabhi/cdn/blob/master/AWS_6.png?raw=true "Generate Public Key")

8. At this point, you have succesfully launched an AWS EC2 instance. Click on **View Instances** to see your newly deployed instance.
![aws_step_7](https://github.com/ghoshabhi/cdn/blob/master/AWS_7.png?raw=true "View Instances")
![aws_step_8](https://github.com/ghoshabhi/cdn/blob/master/AWS_8.png?raw=true "Dashboard")
