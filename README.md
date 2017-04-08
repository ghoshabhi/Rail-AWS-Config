# Step By Step Guide to Deploy a Rails App to AWS EC2

This documentation takes you through the process of configuration required to host a Rails application on a EC2 instance. The tutorial uses [Nginx](https://www.nginx.com/resources/wiki/) + [Passenger](https://www.phusionpassenger.com/library/) on the server. The sample application used in this guide can be found [here](https://github.com/ghoshabhi/Sample-Photo-Webapp)

# (I) Creating a new AWS EC2 instance

- Open [AWS Console](https://aws.amazon.com/console/)
- After you signin into your developer account, you should see a list of AWS services. From the the services find **Compute** and select **EC2** from there. 
![aws_step_1](https://raw.githubusercontent.com/ghoshabhi/cdn/master/AWS_1.png "AWS Dashboard")

- Select **Launch Instance** from the EC2 dashboard: 
![aws_step_2](https://github.com/ghoshabhi/cdn/blob/master/AWS_2.png?raw=true "Launch Instance")
