# Amazon Elastic Compute Cloud (Amazon EC2) 

In this task, you will practically create an Amazon EC2 instance,set up a simple web server,create your own AMI image from an Amazon EC2 instance, create a volume and attach the volume to your EC2 instance. Before you do this task make sure that you have  Lab 3 from the Amazon Cloud Academy and is a familiar with EC2. 

You are required to follow the following steps to complete the lab. 

## Launch the Instance under AWS Academy Learner Lab

1. If you have not yet done ***Lab 3 – Introduction to Amazon EC2*** from **AWS Academy Portal.** It is recommended to complete that lab first.
2. Launch Your Amazon EC2 Instance. (You can follow the guidelines as mentioned in the Lab document till Step 13. (Lab 3)
3. In **Step 14**, it asks you to remove the  Inbound security group rules. ```Please do not remove it.``` Let the rule exist as you will make SSH connection to this newly created instance. 
4. Continue to Step 15 as it is and then **launch the instance.** 

### Bastion Host

Under instances, you willl notice Bastion Host. "A Bastion Host in AWS is a special-purpose instance that acts as a gateway for accessing and managing other resources within a Virtual Private Cloud (VPC) that are not directly accessible from the internet. It provides a secure way to access servers (such as EC2 instances) in a private subnet."  [Read More](https://aws.amazon.com/solutions/implementations/linux-bastion/)

## Establish a Connection to Your Instance

1. Use SSH client to connect to your newly created instance. 

**You will need a key pair, if you have not stored vockey key at the time of generation, you might need to create a new key pair.**
- Public key: Amazon EC2 stores the public key on your instance.
- Private key: You securely store the private key as anyone with access to it can connect to your instances using that key pair.

When launching an instance, you need to sepcify the key pair. 

[Read More About Key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html?icmpid=docs_ec2_console#having-ec2-create-your-key-pair)

### Install & start a simple Web Server by running the following commands

```
sudo dnf install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

```

### Create a simple html page

- You can user nano to create a simple html file and name it as index.html in /var/www/html directory. In the index.html file, you can write your own Name.
- You can also use VSC to create/edit files. You need to remote explorer extensions and configure the fie permissons

```
sudo chown -R ec2-user:root /var/www/html
sudo chmod 775 /var/www/html
``` 

### Update Your Security Group 

You will be proving an access to the Web Server, therefore you are required to add inbound rule as below 

- Type: HTTP
- Source: Anywhere-IPv4

> Check by typing your ipv4 address in the browser, if you can see a simple page. 

### Create an AMI from an Amazon EC2 Instance

You can create a tailored image of a virtual server  that you've created and configured according to your specific requirements like in this task. This custom AMI can include your desired operating system, software packages, configurations, and even your application code.

Creating a custom AMI allows you to capture a snapshot of a particular configuration that you've set up, making it easier to replicate and deploy identical instances with the same setup in the future.

***Follow the guidelines at this link to create your own AMI***

[Link](https://docs.aws.amazon.com/toolkit-for-visual-studio/latest/user-guide/tkv-create-ami-from-instance.html) 

Locate the image that you have created. **Try to launch a new instance** with your own AMI and check if it includes a webserver and the files you have created in your earlier instance.

***Note: Always remember to delete the unused resoruces so that you don't burn your credits.***

## Create an Amazon EBS (Elastic Block Store) volume 

**Steps:** 
- From the AWS Management Console, get to the EC2 dashboard.
- Under the "Elastic Block Store" section, click on "Volumes" in the left sidebar.
- Click the "Create Volume" button.

- Configure the volume settings, including the volume type, size, availability zone, etc. **Allocate only 1 GB for the volume.** 

> Please note that to attach an Amazon EBS (Elastic Block Store) volume to an Amazon EC2 instance, both the volume and the instance must be in the same availability zone within the same Amazon Web Services (AWS) region. 

- Click the "Create Volume" button.

## Attach the volume to an EC2 instance

Once the volume is created, you need to attach the volume to an EC2 instance. 
**Steps:**
- In the the "Volumes" dashboard, Select the volume you just created.
- Click the "Actions" button and choose "Attach Volume."
- In the "Attach Volume" dialog box, select the EC2 instance you want to attach the volume to. 
- Choose the device name (e.g., /dev/sdf) for the volume on the instance.
- Click the "Attach" button.


## Format and Mount the New Volume

In order to use the volume, you need to format and mount it.
**Steps:**

- Check your newly attached volume by running the following command 

```
lsblk
```
- Run the following command to format the volume with the ext4 file system. Replace /dev/volumename with the actual device name of your volume. 

```
sudo mkfs -t ext4 /dev/volumename
```

- Create a directory where you want to mount the volume, for example: 

```
sudo mkdir /mnt/webserver
```

- Mount the volume to the directory
```
sudo mount /dev/volumename /mnt/webserver
```
- Check if the volume is mounted properly 
```
df -h
```
> You are also required to adjust file permissions as per your need once the volume is mounted. 
```
sudo chmod -R permissions /mnt/webserver
sudo chown -R user:group /mnt/webserver
```
## Configure your web server 

Now lets assume that you want to make this new volume as a directory to store web pages. You need to configure your web server. The configuration files for the Apache HTTP Server are typically located in the ```/etc/httpd/``` directory. 

***The main configuration file is /etc/httpd/conf/httpd.conf***

- Edit the file /etc/httpd/conf/httpd.conf You need to open this file as a root user 

```
sudo nano /etc/httpd/conf/httpd.conf
```
Find the **DocumentRoot** directive in the file and replace the document web directory to the newly created volume

DocumentRoot "/mnt/webserver"

- You are also required to update the  the Directory Block. In the same file, you need to update <Directory> to match the new directory that you have set as the DocumentRoot.

- Restart your Web Server
```
sudo systemctl restart httpd
```

## Creating EBS snapshots

In this task, you will learn to create a EBS snapshot for Amazon EBS volume that you created above and name it as . Follow these steps:

1. Log in to the AWS Management Console.

2. Navigate to the EC2 dashboard.

3. Choose "Volumes" from the left sidebar to view your existing EBS volumes.

4. Select an EBS volume for which you want to create a snapshot.

5. In the "Actions" dropdown, choose "Create Snapshot."

6. Provide a meaningful description for the snapshot.

7. Review the snapshot configuration and click "Create Snapshot."

## Copying EBS Snapshots

In this task, you will learn to copy a snapshot. 

1. Choose the snapshot you created above.

2. In the "Actions" dropdown, select "Copy Snapshot."

3. Configure the snapshot copy settings, including the destination region and description.

4. Click "Copy Snapshot" to initiate the copy process.


## Delete EBS Snapshot 

In this task, you will learn to delete a snapshot. 

1. Select one of the snapshots.

2. In the "Actions" dropdown, choose "Delete Snapshot."

3. Confirm the deletion when prompted.
