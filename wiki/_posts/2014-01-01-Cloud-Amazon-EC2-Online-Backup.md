---
title: Cloud Amazon-EC2 Online-Backup

layout: page
category: wiki
---

This page describes how I set the [Amazon Elastic Compute Cloud (EC2)](http://en.wikipedia.org/wiki/Amazon_Elastic_Compute_Cloud) Service to create an automated backup of my [GitHub](http://en.wikipedia.org/wiki/Github) [repositories](https://github.com/frostrubin?tab=repositories) with an [Amazon Linux](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonLinuxAMIBasics.html) instance that is started and terminated on a weekly basis via [EC2 Auto Scaling](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-and-autoscaling.html).

## Run your first EC2 Spot instance

The first step was getting an [EC2 Spot Instance](http://aws.amazon.com/ec2/spot-instances/) up and running to get a feeling for the whole process, make a first login and try first drafts of the backup scripts. The process is pretty straightforward and Amazon even has some very good [tutorial videos](http://aws.amazon.com/ec2/spot-tutorials/)

### Create a SSH Key to use for future Logins

1. In the [AWS Management Console](http://aws.amazon.com/console/) for EC2 under `Network & Security > Key Pairs` choose `Create Key Pair`. I chose the name "MyFirstKeyPair".
2. Keep the `.pem` file that will be downloaded save. You will need it for future use.
3. In your local Terminal, do a `chmod 400` on the file.

### Request the spot instance

1. In the [AWS EC2 Management Console](https://console.aws.amazon.com/ec2) under `Instances > Spot Requests` choose the `Request Spot Instances` button. A wizard should pop up to help you with the setup.
2. Choose an [AMI](http://en.wikipedia.org/wiki/Amazon_Machine_Image). I chose the [Amazon Linux AMI](http://aws.amazon.com/amazon-linux-ami/) in the 64bit version.
3. Choose an [instance type](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html). To keep the costs down, I chose the smallest instance type available: [t1.micro](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts_micro_instances.html)
4. Configure your instance:
4.1 Number of instances to launch: 1
4.2 Maximum price: 0.02$
4.3 Persistent request: No
4.4 All the other details can (normally) left as they are
5. Storage
5.1 Revision the storage amount. By default the instance gets 8 GB which should suffice
5.2 Make sure that `Delete on Termination` is set for the storage
6. Launch the instance
7. You should now see a `pending` spot request in the list of your spot request
8. Under `Instances > Instances` your new instance should pop up shortly.
9. Click on the instance and choose the `Connect` button
10. SSH into your instance via `ssh -i MyFirstKeyPair.pem ec2-user@{public-ec2-ip}`
11. Install software

    sudo yum update
    sudo yum install git

12. If you are having problems with your language settings (I had some with ssh from the OS X Terminal), run the `locale` command to see a list of all locales and overwrite their values as needed. Eg: 

    locale
    export LC_CTYPE=en_US.UTF-8

13. To access other AWS resources or issue [AWS-CLI](http://aws.amazon.com/cli/) related commands, run

    aws configure
    AWS Access Key ID [None]: {your-aws-access-key-id}
    AWS Secret Access Key [None]: {your-aws-access-key}
    Default region name [None]: eu-west-1
    Default output format [None]: json

14. Once you are finished with the instance, either do a `sudo shutdown -h now` or choose `Terminate` via Right-Click on the instance in the AWS Management Console

## Prepare everything for the real instance setup

### Create a new S3 Bucket

1. Create a new [S3 Bucket](http://en.wikipedia.org/wiki/Amazon_S3). I named mine `onlinebackupbucket`.
2. Create a folder hierarchy:

    {your-bucket-name}          
     |        
     |--admin       
     |  |        
     |  |--init_scripts      
     |--github_backups      

### Set up SNS for Email notifications

In this step we will set up a AWS based notification service that will later send us a log of the backup scripts vias email. Amazons own documentation regarding such a setup [can be found here](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/US_SetupSNS.html).

1. In the [AWS SNS Management Console](https://console.aws.amazon.com/sns), hit the button `Create New Topic` and give your topic a name. I chose `OnlineBackup`
2. You will be given a unique resource identifier for the topic, eg: `arn:aws:sns:eu-west-1:896282838585:OnlineBackup`. Note this down, you will need it later.
3. Create a Subscription for your topic
3.1 Type `Email`
3.2 Endpoint `{your@email.com`
3.3 You will get a confirmation mail delivered to your inbox. 
4. In the console, choose `Publish` to test your subscription.

### Create a Role for the instances

Right now you have to manually configure the tools on an instance as you go along. This is not suitable for the intended use of instances that start and stop themselves automatically. In order for each instance to have proper AWS authorizations right from the start, an [AWS IAM](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/UsingIAM.html)[role](http://docs.aws.amazon.com/IAM/latest/UserGuide/WorkingWithRoles.html) has to be defined.

1. In the [AWS IAM Management Console](https://console.aws.amazon.com/iam) choose `Roles > Create New Role`
2. Specify a role name - I named mine `OnlineBackup`
3. Role Type: `Amazon EC2 - Allows EC2 instances to call AWS services on your behalf.`
4. Custom Policy:

    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": [
            "s3:ListBucket",
            "s3:GetBucketLocation"
          ],
          "Resource": "arn:aws:s3:::onlinebackupbucket"
        },
        {
          "Effect": "Allow",
          "Action": [
            "s3:GetObject"
          ],
          "Resource": "arn:aws:s3:::onlinebackupbucket/admin/init_scripts/github_backup.sh"
        },
        {
          "Effect": "Allow",
          "Action": [
            "s3:PutObject",
            "s3:GetObject",
            "s3:DeleteObject"
          ],
          "Resource": "arn:aws:s3:::onlinebackupbucket/github_backups/*"
        },
        {
          "Effect": "Allow",
          "Action": [
            "sns:Publish"
          ],
          "Resource": "arn:aws:sns:eu-west-1:896282838585:OnlineBackup"
        }
      ]
    }

This allows the EC2 instances to see the bucket, copy a script from the bucket, manipulate data in the `github_backups` folder and publish to the created SNS topic.
5. When you create the role from the AWS Console, an [Instance Profile](http://docs.aws.amazon.com/IAM/latest/UserGuide/instance-profiles.html) with the same name will automatically be created as well.
6. If you now launch an instance, a custom Access Key ID and Access Key will be created for the instance and scripts can retrieve these from inside the instance. Eg:

GET http://169.254.169.254/latest/meta-data/iam/security-credentials/OnlineBackup

    {
      "Code" : "Success",
      "LastUpdated" : "2013-12-03T21:45:17Z",
      "Type" : "AWS-HMAC",
      "AccessKeyId" : "AKIAIOLOFFNN8EXAMPLE",
      "SecretAccessKey" : "wKalrXUtnFEMIOK7MDENGLLzxRfiCYEXAMPLEKEY",
      "Token" : "token",
      "Expiration" : "2013-12-03T22:15:17Z"
    }

For more information see [IAM Roles for Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html) and [Instance Metadata and User Data](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AESDG-chapter-instancedata.html).

### Create a Security Group for the instances

Right now, the instances that were created from the instance wizard had a reasonable default security group created: Full internet access and Port 22 (SSH) connections enabled.
We will set up a similar custom security group for the instances to have finer control in the future.

1. In the [AWS EC2 Management Console](https://console.aws.amazon.com/ec2) under `Network & Security > Security Groups` choose `Create Security Group`
2. Give your Group a name, a description and a default VPC (I left mine as it was). Eg:

    OnlineBackup
    Online Backup Security Group
    vpc-99alm305

3. Configure it in the `Inbound` tab to allow port 22 access from 0.0.0.0/0 (the whole world)
4. In the `Outbound` tab, choose Ports: All, destination 0.0.0.0/0 to allow full access to the internet
5. Note down the ID of this group, you will need it later. Eg: `sg-7a657318`

### Test your setup

1. Request a new spot instance, but give it the Security Group `OnlineBackup` and the IAM Role `OnlineBackup`
2. In the `Configure your instance` step, choose `Advanced` and point the wizard to a download of the `custom_init.sh` file (see further down)
2. Launch the instance and give it a couple of minutes to perform the script actions.
3. If nothing happens or the script output sent to you via SNS email indicates an error, connect to the instance and debug, rinse, repeat.

## Create the final product

### Set up the automated instance launching

If your script works, it is time to get the whole instance creation and script execution automated.
This is based on [this blog post](http://alestic.com/2011/11/ec2-schedule-instance) from [Eric Hammond](https://plus.google.com/+EricHammond/) and the documentation [provided by Amazon](http://docs.aws.amazon.com/AutoScaling/latest/DeveloperGuide/schedule_time.html).

#### Create a Launch Configuration

1. Copy the scripts given at the end of this wiki entry into two separate files on your hard drive.
2. In the [EC2 AWS Console](https://console.aws.amazon.com/ec2/) under `Auto Scaling > Launch Configurations` choose `Create launch configuration` and follow the creation wizard:
3. Select your preferred AMI
4. Select instance type `t1.micro`
5. Give your launch configuration the name "OnlineBackup", select a spot price, give it the "OnlineBackup" IAM Role, and under `Configure Details > Advanced Details` choose `User Data > As file` and upload the custom_init.sh
6. Configure the launch configuration to use the security group "OnlineBackup"

#### Create an Auto Scaling Group

1. In the [EC2 AWS Console](https://console.aws.amazon.com/ec2/) under `Auto Scaling > Auto Scaling Groups` choose `Create Auto Scaling group` and follow the creation wizard:
2. Choose to create the auto scaling group based on the existing launch configuration "OnlineBackup"
3. Give the group the name `OnlineBackup`, select a desired capacity of 1 and choose a subnet
4. Do not configure any scaling policies
5. Add notifications for "launch" and "termination" to the OnlineBackup SNS Topic

#### Create a schedule

1. Launch a regular instance to get access to the AWS command line tools. If you also have them installed on your local computer, your can skip this step.
2. Create a credential file and set the permissions to use non-aws-cli command line tools for aws:

    nano credentialfile.txt
    #Content:
    AWSAccessKeyId=ABCDEFG1234
    AWSSecretKey=4321GFEDCBA
    
    chmod 600 credentialfile.txt

4. Tell the auto scaling group to not replace terminated instances

    as-suspend-processes  OnlineBackup  --processes ReplaceUnhealthy --region eu-west-1

5. Create a Start Schedule for the auto scaling group using the [Cron Format](http://en.wikipedia.org/wiki/Cron#Format) (you can easily create a cron schedule at [corntab.com](http://www.corntab.com/pages/crontab-gui).

    as-put-scheduled-update-group-action OnlineBackupStart --auto-scaling-group OnlineBackup --min-size 1 --max-size 1 --recurrence "* 17 * * 5" --region eu-west-1

6. Create a matching End Schedule to be run a couple of minutes after each instance start

    as-put-scheduled-update-group-action OnlineBackupEnd --auto-scaling-group OnlineBackup --min-size 0 --max-size 0 --recurrence "30 17 * * 5" --region eu-west-1


## Monitoring the monthly charges

To keep track of the monthly charges for this setup, a billing alarm can be set up. Amazon [has a tutorial for this](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/monitor_estimated_charges_with_cloudwatch.html).

## Troubleshooting

If you issue commands and get output like 
`Client.InvalidKeyPair.NotFound: The key pair 'MyFirstKeyPair' does not exist`
or `Client.InvalidAMIID.NotFound: The image id '[ami-c7ec0eb0]' does not exist`, your commands are sent to the wrong amazon data center. Append a `--region eu-west-1` option to your command to fix the problem.


## The Scripts

### custom_init.sh


    #!/usr/bin/env bash
    # Purpose: Get a script from a pre-defined location and run it
    
    export LC_CTYPE=en_US.UTF-8
    export AWS_DEFAULT_REGION=eu-west-1
    export AWS_DEFAULT_OUTPUT=json
    
    yum -y update # No sudo because we are running at init
    yum -y install rubygems ruby-json 

    CREDENTIALS=$(curl -s http://169.254.169.254/latest/meta-data/iam/security-credentials/OnlineBackup)
    AWS_ACCESS_KEY_ID=$(echo $CREDENTIALS | ruby -e "require 'rubygems'; require 'json'; puts JSON[STDIN.read]['AccessKeyId'];")
    AWS_SECRET_ACCESS_KEY=$(echo $CREDENTIALS | ruby -e "require 'rubygems'; require 'json'; puts JSON[STDIN.read]['SecretAccessKey'];")
    AWS_SECURITY_TOKEN=$(echo $CREDENTIALS | ruby -e "require 'rubygems'; require 'json'; puts JSON[STDIN.read]['Token'];")
    
    if [ "$AWS_ACCESS_KEY_ID" == "" ]; then
      echo "AWS Credentials could not be read"
      echo "Maybe this instance was launched without IAM data?"
      echo "To proceed, set the variables manually"
      echo "AWS_ACCESS_KEY_ID variable was initial" > /dev/console
      exit 1
    fi
    
    log_dir="/tmp/online_backup/logs"
    logfile="$log_dir"/custom_init.log   #Only for use in this init script
    
    s3_file="s3://onlinebackupbucket/admin/init_scripts/github_backup.sh"
    runfile="/tmp/online_backup/github_backup.sh"
    
    mkdir -p "$log_dir"
    if [ $? != 0 ]; then
      echo "Log dir could not be created" > /dev/console; exit 1
    fi
    
    aws s3 cp "$s3_file" "$runfile" >> "$logfile" 2>&1
    
    if [ $? != 0 ]; then
      echo "Runfile could not be downloaded from S3" >> "$logfile"  
    fi
    
    chmod 700 "${runfile}"
    if [ $? != 0 ]; then
      echo "Runfile perms could not be changed" >> "$logfile"
    else
      oldlog="$logfile"
      logfile="$log_dir"/github_backup.log # For use directly in the script
      source "$runfile"
      github_backup_status=$?
      github_backup_log=$(cat "$logfile")
      logfile="$oldlog"
    fi
    
    echo "Sending Email..." >> "$logfile"
    custom_init_log=$(cat "$logfile")
    read -d '' message <<EOF
    Hello,
    
    Online Backup was completed. Exit Status: $github_backup_status
    Log File Output follows:
    
    github backup
    -------------
    $github_backup_log
    
    custom init
    -----------
    $custom_init_log
    
    EOF
    aws sns publish --topic arn:aws:sns:eu-west-1:896282838585:OnlineBackup --subject "EC2 Cron: Backup Finished" --message "$message"
    if [ $? != 0 ]; then
      echo "Email Sending failed" > /dev/console
      echo "$message" > /dev/console
    fi
    
    shutdown -h now


### github_backup.sh

    #!/usr/bin/env bash
    
    # 0. Variables
    # Clear variables
    tmp_dir="/tmp/online_backup/github"
    oldpath=`pwd`
    
    rm -rf "$logfile"; rm -rf "$tmp_dir"
    
    # 1. Setup general AMI environment
    yum -y install git # No sudo because we are running at init
    if [ $? != 0 ]; then
      echo "Git could not be installed" >> "$logfile"; return 1
    fi
    
    # 2. Download data that will be backed up
    mkdir -p "$tmp_dir" > /dev/null 2>&1
    if [ $? != 0 ]; then
      echo "Temp dir for git clone could not be created" >> "$logfile"; return 1
    fi
    
    mkdir -p "$tmp_dir"_zips > /dev/null 2>&1
    if [ $? != 0 ]; then
      echo "Temp dir for zipping could not be created" >> "$logfile"; return 1
    fi
    
    cd "$tmp_dir"
    reps=$(curl -s https://api.github.com/users/frostrubin/repos | grep "\"svn_url\":" |sed -e 's,"svn_url":,,g;s/^[ \t]*//;s,",,g;s/,//g')
    for i in $(echo "$reps"); do
      a=`echo "$i"|sed -e 's%https://github.com/frostrubin/%%g'`
      echo Cloning "$i" >> "$logfile"
      cloneout=$(git clone $i.git 2>&1)
      if [ $? != 0 ]; then
        echo "$cloneout" >> "$logfile"
        echo "Git Clone Error" >> "$logfile"; return 1
      fi
      echo Cloning Wiki "$i" >> "$logfile"
      wikiout=$(git clone $i.wiki.git 2>&1)
      if [ $? != 0 ]; then
        echo "$wikiout"|grep "repository"|grep "not found"
        if [ $? != 0 ]; then
          echo "$wikiout"|grep "remote error"|grep "repository not exported"
          if [ $? != 0]; then
            echo "$wikiout" >> "$logfile"
            echo "Git Clone Error" >> "$logfile"; return 1
          fi
        fi
      fi
    done;
    
    echo "Zipping..." >> "$logfile"
    for i in $(ls "$tmp_dir"); do
      zipout=$(zip -r "$tmp_dir"_zips/"$i".zip "$tmp_dir"/"$i" 2>&1)
      if [ $? != 0 ]; then
        echo "$zipout" >> "$logfile"
        echo "Zip Error" "$i" >> "$logfile"; return 1
      fi
    done;
    
    date >> "$logfile"
    echo "Syncing..." >> "$logfile"
    datefile=$(date +%Y-%m-%d-%H-%M.txt)
    touch "$tmp_dir"_zips/"$datefile"
    aws s3 sync "$tmp_dir"_zips s3://onlinebackupbucket/github_backups --delete --exclude "*" --include "*.zip" --include "*.txt" >> "$logfile"
    if [ $? != 0 ]; then
      echo "S3 Sync Error" >> "$logfile"; return 1
    fi
    date >> "$logfile"
    echo "Finished Sync" >> "$logfile";
    
    return 0
