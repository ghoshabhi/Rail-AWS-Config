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

# B) SSH into your instance

Open a terminal of your choice and type the following command. Replace the `xx.xx.xx.xx` with your Public IP from the dasboard. The default usernames provided by EC2 are `admin`, `ubuntu` and `ec2-user`: 
```sh
$~ >> ssh -i ~/path/to/your-pem-file.pem ubuntu@xx.xx.xx.xx 
```
If you get an error saying "permission too open", then change the permissions on the file by typing:
```sh
$~ >> chmod 400 your-pem-file.pem
```
If everything went well you should be able to `ssh` int your server successfully.
![ssh_1](https://github.com/ghoshabhi/cdn/blob/master/SH_1.png?raw=true "SSH")

# C) Prepare the server:

## Install essential packages:

1. Update and Upgrade the package manager `apt`:
```
$~ >> sudo apt update && sudo apt upgrade
```
2. Install build `curl`, `gpg` and `build-essentials` packages. **Note:** Since Ubuntu 16.04 LTS's realease, you can now use `apt` instead of `apt-get`! You are free to choose either:
```
$~ >> sudo apt install -y curl gnupg build-essential
```

## Install Ruby and RVM:

1. Install RVM (Ruby Version Manager):
```
$~ >> sudo gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
$~ >> curl -sSL https://get.rvm.io | sudo bash -s stable
$~ >> sudo usermod -a -G rvm `whoami`
```
  - Confirm `rvm` was installed by typing `rvm --version`. If you don't see the following output, logout of your instance and login again and then try the command:
  ![shell_rvm_1](https://github.com/ghoshabhi/cdn/blob/master/SH_2.png?raw=true "RVM Version")

2. Run the following commands, to make sure the environment variable `rvmsudo_secure_path` is set to `1`. On macOS it is found that, the following command is needed to set the correct environment variable to avtivate `rvm`: 
```
$~ >> if sudo grep -q secure_path /etc/sudoers; then sudo sh -c "echo export rvmsudo_secure_path=1 >> /etc/profile.d/rvm_secure_path.sh" && echo Environment variable installed; fi
```
![shell_rvm_2](https://github.com/ghoshabhi/cdn/blob/master/SH_3.png?raw=true "RVM ENV VAR")

3. Install Ruby:
```
$~ >> rvm install ruby
$~ >> rvm --default use ruby
```
The above command installs the latest version of the ruby. To install a specific version you can do:
```
$~ >> rvm install ruby-2.3.0
$~ >> rvm --default use ruby-2.3.0
```

If Ruby was installed correctly, you can check its version by running the following command: `ruby -v` and playing around with the Interactive Ruby Console (irb):

![shell_irb](https://github.com/ghoshabhi/cdn/blob/master/SH_4.png?raw=true "IRB RUBY -V")

