# Setting up a CI/CD pipeline by integrating Jenkins with AWS CodeBuild and AWS CodeDeploy
Jenkins open-source automation server is used to deploy AWS CodeBuild artifacts with AWS CodeDeploy, creating a functioning CI/CD pipeline. When properly implemented, the CI/CD pipeline is triggered by code changes pushed to your GitHub repo, automatically fed into CodeBuild, then the output is deployed on CodeDeploy.
The functioning pipeline creates a fully managed build service that compiles your source code. It then produces code artifacts that can be used by CodeDeploy to deploy to your production environment automatically.
 
Walkthrough
Creating resources to build the infrastructure, including the Jenkins server, CodeBuild project, and CodeDeploy application.
Accessing and unlocking the Jenkins server.
Creating a project and configuring the CodeDeploy Jenkins plugin.
Testing the whole CI/CD pipeline.
Create the resources
how to launch an AWS CloudFormation template, a tool that creates the following resources:
Amazon S3 bucket—Stores the GitHub repository files and the CodeBuild artifact application file that CodeDeploy uses. IAM S3 bucket policy—Allows the Jenkins server access to the S3 bucket. JenkinsRole—An IAM role and instance profile for the Amazon EC2 instance for use as a Jenkins server. This role allows Jenkins on the EC2 instance to access the S3 bucket to write files and access to create CodeDeploy deployments. CodeDeploy application and CodeDeploy deployment group. CodeDeploy service role—An IAM role to enable CodeDeploy to read the tags applied to the instances or the EC2 Auto Scaling group names associated with the instances. CodeDeployRole—An IAM role and instance profile for the EC2 instances of CodeDeploy. This role has permissions to write files to the S3 bucket created by this template and to create deployments in CodeDeploy. CodeBuildRole—An IAM role to be used by CodeBuild to access the S3 bucket and create the build projects. Jenkins server—An EC2 instance running Jenkins. CodeBuild project—This is configured with the S3 bucket and S3 artifact. Auto Scaling group—Contains EC2 instances running Apache and the CodeDeploy agent fronted by an Elastic Load Balancer. Auto Scaling launch configurations—For use by the Auto Scaling group. Security groups—For the Jenkins server, the load balancer, and the CodeDeploy EC2 instance
 
 
Access and unlock your Jenkins server
Copy the JenkinsServerDNSName value from the Outputs tab of the CloudFormation stack, and paste it into your browser.
To unlock the Jenkins server, SSH to the server using the IP address and key pair, following the instructions from Unlocking Jenkins.
Use the root user to Cat the log file (/var/log/jenkins/jenkins.log) and copy the automatically generated alphanumeric password (between the two sets of asterisks). Then, use the password to unlock your Jenkins server, as shown in the following screenshots.
 
 
 
 
Create a project and configure the CodeDeploy Jenkins plugin
                                 
Testing the whole CI/CD pipeline
To test the whole solution, put an application on your GitHub repository.
The following screenshot shows an application tree containing the application source files, including text and binary files, executables, and packages:
 
In this example, the application files are the templates directory, test_app.py file, and web.py file.
The appspec.yml file is the main application specification file telling CodeDeploy how to deploy your application. Jenkins uses the AppSpec file to manage each deployment as a series of lifecycle event “hooks”, as defined in the file. For information about how to create a well-formed AppSpec file, see AWS CodeDeploy AppSpec File Reference.
The buildspec.yml file is a collection of build commands and related settings, in YAML format, that CodeBuild uses to run a build. You can include a build spec as part of the source code, or you can define a build spec when you create a build project. For more information, see How AWS CodeBuild Works.
The scripts folder contains the scripts that you would like to run during the CodeDeploy LifecycleHooks execution with respect to your application requirements. For more information, see Plan a Revision for AWS CodeDeploy.
To test this solution, perform the following steps:
Unzip the application files and send them to your GitHub repository, run the following git commands from the path where you placed your sample application:
$ git add -A
$ git commit -m 'Your first application'
$ git push On the Jenkins server dashboard, wait for two minutes until the previously set project trigger starts working. After the trigger starts working, you should see a new build taking place.
 
In the Jenkins server Console Output page, check the build events and review the steps performed by each Jenkins plugin. You can also review the CodeDeploy deployment in detail, as shown in the following screenshot:
 
On completion, Jenkins should report that you have successfully deployed a web application. You can also use your ELBDNSName value to confirm that the deployed application is running successfully.
 

