# Continuous Integration and Deployment to AWS

This is a simple React App which is deplyoed as Docker Container in AWS Elastic Beanstack. The following sections lists the step by step process of setting up a Continuous Integration Process from local desktop/laptop. For this project TravisCI is used as a the tool for performing Continuous Integration and Deployment to AWS. A seperate Docker Container is also used to run the default tests that are created as a part of the default bootstrapped React Project.


## Prerequisites

1. React JS Library
2. Docker Desktop
3. Travis CI Account
4. GitHub
5. AWS Account

### `React Application`

1. Create a simple React App by running npx create-react-app in the command prompt and make sure you can view it by  [http://localhost:3000](http://localhost:3000) to view it in your browser. Next add Container Support by adding Docker to the project.

2. Create two Docker files one for Development (Dockerfile.dev) and another for Production version (Dockerfile) of the React Application as below.
The only difference between these two is, for Production Docker container, we use NGINX as the webserver to host the React Application and also uses the multi stage build to copy the build output to NGINX.

      a.  Dockerfile.dev

   ![image](https://user-images.githubusercontent.com/50028950/157384321-a72fb287-4ceb-4b31-9fdc-7644e2b63431.png)

      b. Dockerfile (Production)
    
   ![image](https://user-images.githubusercontent.com/50028950/157384669-d6fee228-6c78-47ea-99e5-38edef76c1ed.png)
   
      c. We can run these containers using the docker build and docker run commands using the port mapping and confirm there are no issues in the Docker configuration.

  ![image](https://user-images.githubusercontent.com/50028950/157386251-3e0469d0-5d03-400d-9b5f-f361b3fb2288.png)

      d. Run docker run -p 9000:3000 -t ktadikonda/dockerreactaws and navigate to http://localhost:9000. 
  
  ![image](https://user-images.githubusercontent.com/50028950/157387568-234ca600-7dd4-4bc0-96bd-a287dba0cd41.png)
  
      e. To create the Production container, we just run the docker build command without the -f parameter. In this case, Docker will use the default Dockefile that we created for Production in above will be used to create the container. I used the tag name "dockerreactawsprod" to differentiate it from existing development image from above step. 

  ![image](https://user-images.githubusercontent.com/50028950/157388126-feee0a74-23a4-4bc6-872d-32195d88c110.png)
  
      f. Upload this code to the GitHub account so that we can next setup the Continous Integration from TravisCI. 


### Travis CI Set up

1. Create an Account in TravisCI and select the Free Tier for the Account Plan.
2. If you login with your Github account and provide access to your repositories, you can see all your public repositories where you can setup CI.

   ![image](https://user-images.githubusercontent.com/50028950/157390360-4401831c-587a-4f42-9144-9eb72c4458aa.png)
   
3. Travis looks for a configuration file called ".travis.yml (dot at the beginning is important) on how to package the code. Since we need to package the application as a 
   docker image we need to pass the docker build commands to the TravisCI yml file as below.
   
   ![image](https://user-images.githubusercontent.com/50028950/157391498-c82b3b98-ae00-4af9-8b65-70c1fc087528.png)
   
   The script command performs the task of running the tests that are created by default in the React App using the npm run test.
   
 4. Now with this if any changes in the master branch, TravisCI will initiate the build and creates the docker containers, run the tests and dispays the results.
 
   ![image](https://user-images.githubusercontent.com/50028950/157392053-95faf537-943c-474b-b002-f573f87fea8d.png)




### AWS Setup

1. Login to your AWS Account and Search for Elastic BeanStalk (EBS) and Create Application. By default AWS will create this application in an environment.
2. While creating the Application select the Platform as Docker and Plaform branch as Docker running 64 bit Amazon Linux2 as below

 ![image](https://user-images.githubusercontent.com/50028950/157393744-327608d9-ddc7-4b41-834c-027de156f9a0.png)

3. AWS will internally create a bunch of infrastrucure like Load Balanacers/VPC/VPNs as a part of this provisioning this EBS call and finally after sometime you should see something like this.

 ![image](https://user-images.githubusercontent.com/50028950/157394148-d69b4a6f-16b1-4598-8d81-cb37f176d446.png)

4. Once you click on the URL that was created for the EBS, you will see the sample mockscreen as below. Now will upload the docker container into this so that we hit the EBS url, we can see the React App in the EBS.

 ![image](https://user-images.githubusercontent.com/50028950/157395771-d0017898-d87d-4691-864f-487ae5c61553.png)
 
 Internally EBS will also create a S3 bucket to copy the build output and this bucket name will be used for configuring the Travis CI later.

5. Next we need to create an IAM role so that TravisCI can use this role to deploy the code into EBS whenever there is a change.
6. Create a new IAM user and select AdministerAccess-AWSElasticBeanStalk as the role for this user. After the creation of this user, AWS will provide us AWS Access key and Secret Access Key. These keys needs to be updated in the TravisCI.  

 ![image](https://user-images.githubusercontent.com/50028950/157396511-f1017ec6-b61a-47e6-9a57-1724214a5810.png)

 ![image](https://user-images.githubusercontent.com/50028950/157396687-3f8ede4a-50e4-48b4-ba09-531f663aea7f.png)
 
 Note : Please copy the Secret Access Key immediately as it is only visible once. If you lost it, we need to regenerate the keys again.


### `Final Updates in Travis Configuration`

1. Lastly, we need to tell the TravisCI to deploy the docker container to the Amazon EBS that we created as below by updating its configuration.

 ![image](https://user-images.githubusercontent.com/50028950/157397305-e9baa8c4-ce88-4727-9643-b5500e973511.png)

  a. Deploy : Location where we need to deploy this application. Since we are deploying it to AWS Elastic Beanstalk we need to specify "elasticbeanstalk". </br>
  b. Region : This is the AWS region which can be found from the AWS Beanstalk URL (Generally like us-east-1, us-west-2 etc) </br>
  c. app:This is Application Name which we have used while creating the EBS </br>
  d. Env: Created by AWS automatically by appending the "env" to the app name. </br>
  e. bucket_name: Created during the process of EBS Provisioning. You can get it by navigating to the S3 section of AWS and identifying a bucket which starts with "elastic..." </br>
  f. bucket_path : Same as app name. </br>
  
 2. Next we need to tell TravisCI to use the ACCESS and SECRET ACCESS Keys while deploying into AWS. Additionally since we do not want to have these keys hardcoded in the YML files we will pass these as parameters in the above config (See last two lines)
 3. Navigate to the Project Settings in TravisCI and add the same keys.
 
 ![image](https://user-images.githubusercontent.com/50028950/157398388-627ed571-38d8-45b9-b6b2-faf8753f7aaf.png)

Thats it.. Now if we make any change to the app and push it to master branch, an automatic build will be triggered in Travis CI and the updated container is deployed in AWS as Elastic Beanstalk application.

![image](https://user-images.githubusercontent.com/50028950/157398762-0ffbbb1f-f39d-4f10-8816-59511ed293b1.png)

 

