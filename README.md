# Using Auto Scaling In AWS Linux

Using simple commands with the AWS CLI, you first create a virtual server (EC2 instance) to run a web server. Then, you make a reusable copy of that server, called an AMI. This copy is used to automatically launch more servers when needed, using a feature called Auto Scaling, which adds or removes servers based on traffic. To keep everything running smoothly, a Load Balancer is set up to share the traffic evenly across all servers, even if they’re in different locations.



Starting architecture:







<img width="956" height="565" alt="image" src="https://github.com/user-attachments/assets/920e362c-2d6e-4ae0-bccc-9f339aab3c03" />








Final architecture:









<img width="967" height="598" alt="image" src="https://github.com/user-attachments/assets/81d7e0fb-756a-4537-97d6-8dd9974efa55" />









# STEP 1: CREATING A NEW AMI FOR AMAZON EC2 AUTO SCALING

   **Connecting to the Command Host Instance**


You use EC2 Instance Connect to connect to the Command Host EC2 instance that was created when the lab was provisioned. You use this instance to run AWS CLI commands.

1.1 On the AWS Management Console, in the Search bar, enter and choose EC2 to open the EC2 Management Console > In the navigation pane, choose Instances.

1.2 From the list of instances, select the  Command Host instance > Choose Connect > On the EC2 Instance Connect tab, choose Connect.







  <img width="1567" height="634" alt="image" src="https://github.com/user-attachments/assets/5b1a2f16-a883-4380-a98b-59c0547866f0" />







   **Configuring the AWS CLI**







<img width="1590" height="234" alt="image" src="https://github.com/user-attachments/assets/695946ce-7507-421b-a197-9d2dcad457f1" />











<img width="1396" height="135" alt="image" src="https://github.com/user-attachments/assets/77d5ae60-e931-4553-ac8a-27d45587f0b3" />








   

1.3 To confirm that the Region in which the Command Host instance is running is the same as the lab (the us-west-2 Region), run the following command:






<img width="475" height="57" alt="image" src="https://github.com/user-attachments/assets/f3697a2c-de13-4d2d-84e5-1d0e3a5e1a98" />


1.4 To update the AWS CLI software with the correct credentials, run the following command:





<img width="503" height="28" alt="image" src="https://github.com/user-attachments/assets/0f8b6b53-2737-4732-aba8-dde6c6f177ee" />








<img width="497" height="213" alt="image" src="https://github.com/user-attachments/assets/860fe083-3f6e-485b-9556-fe8083570881" />










<img width="677" height="100" alt="image" src="https://github.com/user-attachments/assets/18f0f39f-76db-4744-940c-99717f8147ed" />









1.5 To access these scripts, enter the following command to navigate to their directory:





<img width="487" height="31" alt="image" src="https://github.com/user-attachments/assets/12712f86-bb19-491b-959b-370ea535f792" />











<img width="678" height="46" alt="image" src="https://github.com/user-attachments/assets/bf8cdfc5-796c-4a1d-8c71-82ac3cd69847" />










  **Creating A New EC2 Instance**

Use the AWS CLI to create a new instance that hosts a web server. 


1.6 To inspect the UserData.txt script that was installed for you as part of the Command Host creation, run the following command:







  
<img width="491" height="33" alt="image" src="https://github.com/user-attachments/assets/501500f1-d7c6-4642-8fb0-283c2ebdda77" />













<img width="1240" height="274" alt="image" src="https://github.com/user-attachments/assets/ba630059-b701-4ec3-8f6e-7f5dd4e2583a" />















This script performs a number of initialization tasks, including updating all installed software on the box and installing a small PHP web application that you can use to simulate a high CPU load on the instance. The following lines appear near the end of the script:








<img width="503" height="98" alt="image" src="https://github.com/user-attachments/assets/f2a9c00f-930b-49d0-a3ea-526adbfcd91c" />












<img width="1032" height="526" alt="image" src="https://github.com/user-attachments/assets/645f6ec6-479c-4215-91b2-4fcd27c1ca21" />









_These lines erase any history or security information that might have accidentally been left on the instance when the image was taken._

At the bottom of this page, On Lab Details, copy the following:

Copy the **KEYNAME**, **AMIID**, **HTTPACCESS**, and **SUBNETID** values into a text editor document.



1.7 In the following script, replace the corresponding text with the values from the previous step.







<img width="504" height="174" alt="image" src="https://github.com/user-attachments/assets/c21102c9-b197-4356-9bdf-a5041a374323" />








<img width="1573" height="85" alt="image" src="https://github.com/user-attachments/assets/08feb154-0cfd-4218-9f0a-111de703daf4" />







Enter your modified script into the terminal window, and run the script.

1.7 The output of this command provides you with an InstanceId. Subsequent steps in this lab refer to this value as NEW-INSTANCE-ID. Replace this value as needed throughout this lab > Copy and paste the InstanceId value into a text editor to use later. 

1.8 To use the aws ec2 wait instance-running command to monitor this instance's status, replace NEW-INSTANCE-ID in the following command with the InstanceID value that you copied in the previous step. Run your modified command. 








<img width="499" height="52" alt="image" src="https://github.com/user-attachments/assets/9003f11f-94fb-4a92-bbb7-b203f061838f" />








<img width="976" height="37" alt="image" src="https://github.com/user-attachments/assets/5f850ca2-a5a7-4499-a527-6bb85e902ccb" />






_Wait for the command to return to a prompt before proceeding.

Your instance starts a new web server. To test that the web server was installed properly, you must obtain the public DNS name._


1.9  To obtain the public DNS name, in the following command, replace NEW-INSTANCE-ID with the value that you copied previously, and run your modified command:






<img width="719" height="55" alt="image" src="https://github.com/user-attachments/assets/07075809-2067-4682-bda2-af2b09acec94" />










<img width="1372" height="54" alt="image" src="https://github.com/user-attachments/assets/4708b71d-d508-47bb-b190-5401f7804f01" />






1.10 Copy the output of this command without the quotation marks. 

   The value of this output is referred to as PUBLIC-DNS-ADDRESS in the next steps.


1.11 In a new browser tab, enter the output that you copied from the previous step.

   It could take a few minutes for the web server to be installed. Wait 5 minutes before continuing to the next steps.


  Do not choose Start Stress at this stage.

1.12 In the following command, replace _PUBLIC-DNS-ADDRESS_ with the value that you copied in the previous steps, and then run your modified command.







<img width="502" height="41" alt="image" src="https://github.com/user-attachments/assets/e63b09cd-813a-469c-aef6-4e256e7db7ef" />

  










<img width="264" height="266" alt="image" src="https://github.com/user-attachments/assets/1362784d-6986-4b85-b814-1787196d43f3" />





  **Creating a Custom AMI**

  
1.13 To create a new AMI based on this instance, in the following aws ec2 create-image command, replace NEW-INSTANCE-ID with the value that you copied previously, and run your adjusted command:







<img width="504" height="53" alt="image" src="https://github.com/user-attachments/assets/9e7a88b8-4580-46c5-b4d5-0339401846e6" />








<img width="1017" height="91" alt="image" src="https://github.com/user-attachments/assets/64d9a6bd-81ca-4222-a0ab-5567a2b7a4bb" />









<img width="546" height="101" alt="image" src="https://github.com/user-attachments/assets/3242ea14-5956-4b54-91ff-bb739ba9686f" />







STEP 2: CREATING AN AUTO SCALING ENVIRONMENT

**Creating an Application Load Balancer**

2.1 On the EC2 Management Console, in the left navigation pane, locate the Load Balancing section, and choose Load Balancers > Choose Create load balancer.

2.2 In the Load balancer types section, for Application Load Balancer, choose Create.

2.3 On the Create Application Load Balancer page, in the Basic configuration section, configure the following option:

   * For Load balancer name, enter _WebServerELB_






2.4   <img width="508" height="221" alt="image" src="https://github.com/user-attachments/assets/4aebe20d-c716-4c08-bd21-6fcaf31d3263" />




2.5 In the Security groups section, choose the X for the default security group to remove it > From the Security groups dropdown list, choose HTTPAccess.

The HTTPAccess security group has already been created for you, which permits HTTP access.

2.6 In the Listeners and routing section, choose the Create target group link.

Note: This link opens a new browser tab with the Create target group configuration options.

2.7 On the Specify group details page, in the Basic configuration section, configure the following options:

  * For Choose a target type, choose Instances.

  * For Target group name, enter webserver-app

2.8 In the Health checks section, for Health check path, enter /index.php > At the bottom of the page, choose Next.

2.9 On the Register targets page, choose Create target group.

Once the target group has been created successfully, close the Target groups browser tab.

2.10 Return to the Load balancers browser tab, and locate the Listeners and routing section. For Default action, choose  Refresh to the right of the Forward to dropdown list.

2.11 From the Forward to dropdown list, choose webserver-app > From the Forward to dropdown list, choose webserver-app.

2.12 To view the WebServerELB load balancer that you created, choose View load balancer > To copy the DNS name of the load balancer, use the copy option , and paste the DNS name into a text editor. 

**Creating a launch template**

STEP 3: VERIFYING THE AUTO SCALING CONFIGURATION


STEP 4: TESTING AUTO SCALING CONFIGURATION 






# **LAB DETAILS**

<img width="556" height="201" alt="image" src="https://github.com/user-attachments/assets/c727ff0b-c935-4bc4-a240-fe77a6e58533" />





