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

## Install bundler

Bundler is a popular tool for managing application gem dependencies. To install run the following command:

```
$~ >> gem install bundler --no-rdoc --no-ri
```

## Install NodeJS

Rails uses NodeJS to precompile the asset pipeline. To install Node:
```
$~ >> sudo apt-get install -y nodejs &&
      sudo ln -sf /usr/bin/nodejs /usr/local/bin/node
```
![shell_node_install](https://github.com/ghoshabhi/cdn/blob/master/SH_5.png?raw=true "NodeJS")

# D) Install Passenger Web Server

Passenger is an open source web application server for Ruby. It handles HTTP requests, manages processes and resources, and enables administration, monitoring and problem diagnosis. Passenger is very easy to use, makes deploying in production much easier and is scalable.

To install Passenger on your EC2 instance follow the commands below:

1. Install Passenger's PGP Key and add HTTPS support for APT:
```
$~ >> sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7 &&
sudo apt-get install -y apt-transport-https ca-certificates
```

2. Add Passenger's APT repository:
```
$~ >> sudo sh -c 'echo deb https://oss-binaries.phusionpassenger.com/apt/passenger xenial main > /etc/apt/sources.list.d/passenger.list' && sudo apt-get update
```
3. Install Passenger + Nginx
```
$~ >> sudo apt-get install -y nginx-extras passenger
```
![shell_passenger_nginx](https://github.com/ghoshabhi/cdn/blob/master/SH_6.png?raw=true "Passenger+Nginx")

4. Now we will enable Passenger Nginx module:
  - `sudo nano /etc/nginx/nginx.conf`
  - Uncomment the line in the `http` block by removing the `#` symbol, that says: `# include /etc/nginx/passenger.conf;` 
  - To save the file press: `Ctrl+O` => Press the enter key => `Ctrl+X`
  - If you do not see a `http` block, add it yourself:
  ```
    http {
      include /etc/nginx/passenger.conf
    }
  ```
  - ![shell_passenger_conf](https://github.com/ghoshabhi/cdn/blob/master/SH_7.png?raw=true "Passenger Config Enable")
  -  Finally restart the Nginx service:
  ```
  $~ >> sudo service nginx restart
  ```
5. If everything went well and Nginx with Passenger was installed correctly, you should see the following page when you visit your EC2 instance's public IP:
  ![nginx](https://github.com/ghoshabhi/cdn/blob/master/SH_8.png?raw=true "Nginx")

6. Validate if Passenger was installed or not correctly:

![validate_passenger](https://github.com/ghoshabhi/cdn/blob/master/SH_9.png?raw=true "Validate Passenger")

# E) Deploy App

## Add new user

1. Add new user:
```
$~ >> sudo adduser abhi
```
![sudo_add_user](https://github.com/ghoshabhi/cdn/blob/master/SH_10.png?raw=true "Add user")

2. Give new user `sudo` permissions:
```
$~ >> sudo visudo
```

A editor will open up, find `root    ALL=(ALL:ALL) ALL` under `User privilege section` and add the following line:
```
root    ALL=(ALL:ALL) ALL
abhi    ALL=(ALL:ALL) ALL
```
![adduser](https://github.com/ghoshabhi/cdn/blob/master/SH_11.png?raw=true "Add User")

To save the file press: `Ctrl + O` => Hit enter to save the file name => `Ctrl+Y`

3. Add the new user to `sudo` group:
![sudo_group](https://github.com/ghoshabhi/cdn/blob/master/SH_12.png?raw=true "Sudo Group")

## Install the public key for new user

1. Issue the following command to get the public key. Copy the output of the command, we will use this in th next step.
```
$~ >> cat ~/.ssh/authorized_keys
```

2. Create a `.ssh` directory and make a file `authorized_keys` there and set correct permissions on them:

![ssh_dir](https://github.com/ghoshabhi/cdn/blob/master/SH_13.png?raw=true "SSH dir")

3. Edit the `~/.ssh/authorized_keys` file by:
```
$~ >> nano ~/.ssh/authorized_keys
```

4. Paste the public key you copied on step 1 here and save the file and exit.

5. Enter `exit` twice to exit out of the server.

6. If everything worked correctly, you should now be able to log in to your server by the new user you created!
![ssh_new_user](https://github.com/ghoshabhi/cdn/blob/master/SH_14.png?raw=true "SSH new user")

_**Note:** Creating a new user is very important, as the root user doesn't throw any confirmation messages when you run commands - which can be dangerous. The new user has to always use `sudo` which asks for password and saves us from any wrong command being issued._

## Install Git on server

I have already deployed my project on Github, since it is easier to transfer files from a git server than using FTP, but you may suit yourself.

_**Note:** The commands henceforth are written from the new user account and not the root user, unless otherwise mentioned_

1. Install Git:
```
$~ >> sudo apt install -y git
```

2. Make a new directory under `/var/www` and clone the project there:
```
$~ >> sudo mkdir /var/www/photosite-webapp
$~ >> sudo chown your-new-user-name: /var/www/photosite-webapp
```

3. Clone your project from github into `/var/www/photosite-webapp` with correct user permissions:
```
$~ >> sudo -u your-new-user-name -H git clone https://your-github-project-url
```
If everything worked correctly, you should now have your project's code on your server:

![git_setup](https://github.com/ghoshabhi/cdn/blob/master/P1.png?raw=true "Git Project Setup")

## Install project dependencies

