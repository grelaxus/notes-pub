# Serverless Authentication and Authorization Quickstart Lab

This lab demonstrates how to demo the SpaceFinder reference app, and help you understand the interactions between Cognito User Pools, Cognito Federated Identities, API Gateway, Lambda, and IAM.

>**Estimated cost:** The estimated cost to run this lab for an hour is $0 for AWS Accounts eligible for the [AWS Free Tier](https://aws.amazon.com/free/), and less than $0.25 for other AWS accounts.

> Please be sure to complete the "CLEANUP" section after you're done with the lab!

---

# Here's the high-level plan...

1. **SETUP** (5 minutes): Provision an EC2 instance, and run the pre-built SpaceFinder Docker container. Configure your AWS credentials, deploy the AWS resources, and start the Ionic 2 server.
1. **INTERACT AND LEARN** (30 minutes): Interact with the hybrid mobile app, and gain insights with the behind-the-scenes info displayed in the browser's JavaScript console. Explore how the AWS resources are configured.
1. **CLEANUP** (1 minute): Stop the Ionic 2 server, and un-deploy the AWS resources.

---

# Fast links

GoTo:  
[KK](#headKK)

---

# SETUP (5 minutes)

Setup is quick and easy. You'll provision an EC2 instance, and run the pre-built SpaceFinder Docker container. Once your AWS credentials and configured and the AWS resources are deployed, you'll start the Ionic 2 server which will serve up the hybrid mobile app.

1. **Create a highly-privileged IAM user, if necessary.** The lab needs permissions to provision/de-provision Cognito User Pools, DynamoDB tables, S3 buckets, Lambda functions, API Gateway configurations, CloudFormation stacks, and IAM roles. You will need the AWS credentials (AWS Access Key and AWS Secret Access Key ID) associated with this highly-privileged user later on in step #5.
	> All resources are created in your personal AWS account. This lab is self-contained and cleans up after itself by un-deploying all generated AWS resources.
  
1. **Launch the EC2 instance** in your AWS account, using a public community AMI which contains a Docker image with a pre-configured SpaceFinder environment:

	- **Public Community AMI**: `ami-bada16ac`
	- **Instance type**: m4.large
  - **Network**: Select any VPC that has a **public subnet**. (If you haven't modified your "default VPC", that will work fine for this lab.)
  - **Subnet**: Launch in any **public subnet** (in any AZ). 
  - **Auto-assign Public IP**: Enable
  - **Security Groups:** Open up ports 22 (SSH) and 80 (HTTP) to `0.0.0.0/0`
  - **SSH keypair:** Associate it with an SSH keypair of your choice
  - **Name:** You may wish to tag your instance with a name (e.g. "AWS-Auth-Lab") to make it to easier to identify later, and to remind yourself to delete the instance once you're done with the lab.
  - For all other EC2 launch settings, you can use the defaults.
1. **SSH into the EC2 instance**. The AMI is based on Amazon Linux, and the username to SSH into the instance is `ec2-user`. ([Instructions](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html))

		ssh -i /path/to/keypair.pem ec2-user@PUBLIC_DNS_OF_YOUR_EC2_SERVER

1. **Once logged in, run the Docker container** in interactive mode. This  command will start the Docker container, and bind the container’s port 8100 to the host EC2 instance’s port 80:

		docker run --rm -it -p 80:8100 awsdevops/aws-serverless-auth-reference-app

	> Troubleshooting: If you encounter a error saying “Bind for 0.0.0.0:80 failed: port is already allocated”, try: `sudo service docker restart`
	

	


1. **Configure the AWS credentials** to be used while inside the Docker container. Use the AWS credentials (AWS Access Key and AWS Secret Access Key ID) associated with the highly-privileged IAM user that was referenced in step #1.

		aws configure

	> Just accept the defaults when it asks for region and output format. The app configuration file at `~/api/config.js` configures the resources to be provisioned in `us-east-1` by default. Also, AWS credentials are NOT persisted across Docker container runs, so if you exit and later re-run the Docker container, remember to run `aws configure` again.

1. **Deploy the AWS resources into your account**

		cd /home/aws-serverless-auth-reference-app/api
		gulp deploy
		gulp bootstrap
		
    <details><summary>gulp deploy log</summary><p>
	
	```sh
	[Docker container (aws-serverless-auth-reference-app): /home/aws-serverless-
	auth-reference-app/api]  gulp deploy
	[03:01:16] Using gulpfile /home/aws-serverless-auth-reference-app/api/gulpfile.js
	[03:01:16] Starting 'deploy'...
	[03:01:16] Starting 'create_cloudformation_stack'...
	[2017-05-07T03:01:16.837Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Creating CloudFormation stack...
	[2017-05-07T03:01:17.282Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Successfully invoked the CloudFormation stack spacefinder-api-development-stack
	[2017-05-07T03:01:17.373Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Waiting for stack mutation. This may take some time - CREATE_IN_PROGRESS
	[2017-05-07T03:01:22.441Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Waiting for stack mutation. This may take some time - CREATE_IN_PROGRESS
	[2017-05-07T03:01:27.494Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Waiting for stack mutation. This may take some time - CREATE_IN_PROGRESS
	[2017-05-07T03:01:32.562Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Waiting for stack mutation. This may take some time - CREATE_IN_PROGRESS
	[2017-05-07T03:01:37.621Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Waiting for stack mutation. This may take some time - CREATE_IN_PROGRESS
	[2017-05-07T03:01:42.708Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Waiting for stack mutation. This may take some time - CREATE_IN_PROGRESS
	[2017-05-07T03:01:47.802Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Waiting for stack mutation. This may take some time - CREATE_IN_PROGRESS
	[2017-05-07T03:01:52.859Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Waiting for stack mutation. This may take some time - CREATE_IN_PROGRESS
	[2017-05-07T03:01:57.907Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Waiting for stack mutation. This may take some time - CREATE_IN_PROGRESS
	[2017-05-07T03:02:03.005Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Stack operation completed
	[03:02:03] Finished 'create_cloudformation_stack' after 46 s
	[03:02:03] Starting 'create_dynamodb_tables'...
	[2017-05-07T03:02:03.009Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Creating DynamoDB tables...
	Table created: 'spacefinder-api-development-locations'
	Table created: 'spacefinder-api-development-profiles'
	Table created: 'spacefinder-api-development-bookings'
	Table created: 'spacefinder-api-development-resources'
	[03:02:03] Finished 'create_dynamodb_tables' after 187 ms
	[03:02:03] Starting 'create_cognito_pools'...
	[2017-05-07T03:02:03.195Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Creating Cognito Identity and User Pools...
	[2017-05-07T03:02:05.208Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created Cognito User Pool spacefinder-api-development-userPool
	[2017-05-07T03:02:05.280Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created Cognito User Pool Client spacefinder-api-development-userPool-client
	[2017-05-07T03:02:05.602Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created Cognito Identity Pool spacefinder_api_development_identityPool
	[2017-05-07T03:02:05.782Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Setup Cognito Identity Pools user roles
	[03:02:05] Finished 'create_cognito_pools' after 2.59 s
	[03:02:05] Starting 'generate_client_config'...
	[2017-05-07T03:02:05.783Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Generating application client config...
	[2017-05-07T03:02:06.042Z]  INFO: spacefinder/190 on 92b9ce9f42b8: 
	    Generated Config:
	     { REGION: 'us-east-1',
	      PROFILE_IMAGES_S3_BUCKET: 'spacefinder-api-development-stack-userdatabucket-1517eusg4r1la',
	      API_ENDPOINT: 'https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development',
	      USER_POOL_ID: 'us-east-1_DvCJ5Zbm1',
	      CLIENT_ID: '13399vooimes62v8ahlnr83gei',
	      IDENTITY_POOL_ID: 'us-east-1:f2191387-075f-4d86-a79c-cd83766677b0' }
	[2017-05-07T03:02:06.044Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Successfully generated Lambda environment config
	[2017-05-07T03:02:06.045Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Successfully generated application config
	[03:02:06] Finished 'generate_client_config' after 262 ms
	[03:02:06] Starting 'deploy_lambda'...
	[03:02:06] Starting 'create_lambda_zip'...
	[2017-05-07T03:02:06.046Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Creating Lambda zip archive...
	[2017-05-07T03:02:06.047Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created 'dist' directory for Lambda zip
	[2017-05-07T03:02:10.506Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Lambda zip archive written to lambda-1.0.0.zip as 6949793 total bytes compressed
	[03:02:10] Finished 'create_lambda_zip' after 4.46 s
	[03:02:10] Starting 'upload_lambda_zip'...
	[2017-05-07T03:02:10.511Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Uploading Lambda zip archive to S3...
	[2017-05-07T03:02:11.155Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Successfully uploaded Lambda code to lambda-1.0.0.zip
	[2017-05-07T03:02:11.156Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Uploaded Lambda zip as s3://spacefinder-api-development-stack-lambdabucket-17omho37awvzi/lambda-1.0.0.zip
	[03:02:11] Finished 'upload_lambda_zip' after 646 ms
	[03:02:11] Starting 'create_lambda_functions'...
	[2017-05-07T03:02:11.157Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Creating Lambda functions from Swagger API definition...
	[2017-05-07T03:02:11.991Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created/updated Lambda function spacefinder-api-development-bookings-ListByResourceId
	[2017-05-07T03:02:12.098Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created/updated Lambda function spacefinder-api-development-bookings-Delete
	[2017-05-07T03:02:12.156Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created/updated Lambda function spacefinder-api-development-bookings-Get
	[2017-05-07T03:02:12.224Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created/updated Lambda function spacefinder-api-development-resources-List
	[2017-05-07T03:02:12.316Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created/updated Lambda function spacefinder-api-development-bookings-Create
	[2017-05-07T03:02:12.324Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created/updated Lambda function spacefinder-api-development-resources-Delete
	[2017-05-07T03:02:12.339Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created/updated Lambda function spacefinder-api-development-resources-Get
	[2017-05-07T03:02:12.362Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created/updated Lambda function spacefinder-api-development-locations-Get
	[2017-05-07T03:02:12.388Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created/updated Lambda function spacefinder-api-development-bookings-ListByUserId
	[2017-05-07T03:02:12.497Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created/updated Lambda function spacefinder-api-development-resources-Create
	[2017-05-07T03:02:12.503Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created/updated Lambda function spacefinder-api-development-locations-Delete
	[2017-05-07T03:02:12.520Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created/updated Lambda function spacefinder-api-development-locations-Create
	[2017-05-07T03:02:12.926Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created/updated Lambda function spacefinder-api-development-locations-List
	[2017-05-07T03:02:12.990Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Permissions updated spacefinder-api-development-locations-List
	[2017-05-07T03:02:13.005Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Permissions updated spacefinder-api-development-resources-Delete
	[2017-05-07T03:02:13.006Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Permissions updated spacefinder-api-development-bookings-ListByUserId
	[2017-05-07T03:02:13.007Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Permissions updated spacefinder-api-development-resources-Get
	[2017-05-07T03:02:13.007Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Permissions updated spacefinder-api-development-bookings-ListByResourceId
	[2017-05-07T03:02:13.010Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Permissions updated spacefinder-api-development-resources-List
	[2017-05-07T03:02:13.011Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Permissions updated spacefinder-api-development-locations-Delete
	[2017-05-07T03:02:13.012Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Permissions updated spacefinder-api-development-locations-Create
	[2017-05-07T03:02:13.012Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Permissions updated spacefinder-api-development-bookings-Delete
	[2017-05-07T03:02:13.015Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Permissions updated spacefinder-api-development-bookings-Create
	[2017-05-07T03:02:13.020Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Permissions updated spacefinder-api-development-resources-Create
	[2017-05-07T03:02:13.025Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Permissions updated spacefinder-api-development-locations-Get
	[2017-05-07T03:02:13.030Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Permissions updated spacefinder-api-development-bookings-Get
	[03:02:13] Finished 'create_lambda_functions' after 1.87 s
	[03:02:13] Starting 'create_custom_authorizer'...
	[2017-05-07T03:02:13.032Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Creating Custom Authorizer Lambda function...
	[2017-05-07T03:02:13.715Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Created/updated Lambda function spacefinder-api-development-authorizer-Custom
	[2017-05-07T03:02:13.740Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Permissions updated spacefinder-api-development-authorizer-Custom
	[03:02:13] Finished 'create_custom_authorizer' after 710 ms
	[03:02:13] Finished 'deploy_lambda' after 7.7 s
	[03:02:13] Starting 'deploy_api'...
	[03:02:13] Starting 'import_api'...
	[2017-05-07T03:02:13.742Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Importing Swagger API definition into API Gateway...
	[03:02:13] Finished 'import_api' after 37 ms
	[03:02:13] Starting 'sleep'...
	[2017-05-07T03:02:13.779Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Sleeping for 5000 milliseconds
	[2017-05-07T03:02:14.861Z]  INFO: spacefinder/190 on 92b9ce9f42b8: API imported successfully
	[03:02:18] Finished 'sleep' after 5.01 s
	[03:02:18] Starting 'create_api_stage'...
	[2017-05-07T03:02:18.785Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Creating API stage deployment...
	[2017-05-07T03:02:19.922Z]  INFO: spacefinder/190 on 92b9ce9f42b8: API deployment created successfully
	[03:02:19] Finished 'create_api_stage' after 1.14 s
	[03:02:19] Finished 'deploy_api' after 6.18 s
	[03:02:19] Starting 'generate_sample_users'...
	[2017-05-07T03:02:19.924Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Generating sample users
	[03:02:19] Finished 'generate_sample_users' after 54 ms
	[03:02:19] Starting 'generate_sample_groups'...
	[2017-05-07T03:02:19.978Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Generating sample user groups..
	[2017-05-07T03:02:20.066Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Incoming request to crate group adminGroup
	[2017-05-07T03:02:20.077Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Incoming request to crate group clientGroup
	[03:02:20] Finished 'generate_sample_groups' after 101 ms
	[03:02:20] Starting 'sleep'...
	[2017-05-07T03:02:20.080Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Sleeping for 5000 milliseconds
	[03:02:25] Finished 'sleep' after 5.01 s
	[03:02:25] Starting 'assign_users_to_cognito_user_groups'...
	[2017-05-07T03:02:25.085Z]  INFO: spacefinder/190 on 92b9ce9f42b8: Assigning users to groups...
	[2017-05-07T03:02:25.090Z]  INFO: spacefinder/190 on 92b9ce9f42b8: 2
	[03:02:25] Finished 'assign_users_to_cognito_user_groups' after 69 ms
	[03:02:25] Finished 'deploy' after 1.13 min

	```
	</p></details>
    <details><summary>gulp bootstrap log</summary><p>
    
    ```sh
    [Docker container (aws-serverless-auth-reference-app): /home/aws-serverless-
    auth-reference-app/api]  gulp bootstrap
    [03:12:34] Using gulpfile /home/aws-serverless-auth-reference-app/api/gulpfile.js
    [03:12:34] Starting 'bootstrap'...
    [03:12:34] Starting 'generate_sample_data'...
    [2017-05-07T03:12:34.092Z]  INFO: spacefinder/200 on 92b9ce9f42b8: Generating sample data
    [03:12:34] Finished 'generate_sample_data' after 108 ms
    [03:12:34] Finished 'bootstrap' after 111 ms
    
    ```
    </p></details>
1. **Start the Ionic 2 server.** This starts up the Ionic 2 server listening on port 8100, which is port-mapped (via Docker) to port 80 of the host EC2 instance.

		cd /home/aws-serverless-auth-reference-app/app
		ionic serve
		
	<details><summary>ionic serve log</summary><p>
	
    ```sh
    [Docker container (aws-serverless-auth-reference-app): /home/aws-serverless-
    auth-reference-app/app]  ionic serve
    ******************************************************
     Dependency warning - for the CLI to run correctly,      
     it is highly recommended to install/upgrade the following:     
    
     Please install your Cordova CLI to version  >=4.2.0 `npm install -g cordova`
    
    ******************************************************
    
    Running 'serve:before' npm script before serve
    
    > SpaceFinder@ watch /home/aws-serverless-auth-reference-app/app
    > ionic-app-scripts watch
    
    [03:13:39]  ionic-app-scripts 1.0.0 
    [03:13:39]  watch started ... 
    [03:13:39]  build dev started ... 
    [03:13:39]  clean started ... 
    [03:13:39]  clean finished in 1 ms 
    [03:13:39]  copy started ... 
    [03:13:39]  transpile started ... 
    [03:13:44]  transpile finished in 4.82 s 
    [03:13:44]  webpack started ... 
    [03:13:48]  copy finished in 9.17 s 
    [03:13:55]  webpack finished in 11.39 s 
    [03:13:55]  sass started ... 
    [03:13:57]  sass finished in 1.70 s 
    [03:13:57]  build dev finished in 17.99 s 
    [03:13:57]  watch ready in 18.41 s 
    Running live reload server: http://172.17.0.2:35729
    Watching: www/**/*, !www/lib/**/*, !www/**/*.map
    √ Running dev server:  http://172.17.0.2:8100
    Ionic server commands, enter:
      restart or r to restart the client app from the root
      goto or g and a url to have the app navigate to the given url
      consolelogs or c to enable/disable console log output
      serverlogs or s to enable/disable server log output
      quit or q to shutdown the server and exit
	```
	</p></details>
1. **View the hybrid mobile app in your browser**, by visiting:

		http://PUBLIC_DNS_OF_YOUR_EC2_SERVER/

1. **Open the JavaScript console**. As you interact with the hybrid mobile app, useful info will appear in the JavaScript console.

	* For example, for Firefox on Mac, the JavaScript console can be toggled via the menu: `Tools > Web Developer > Web Console`. For Chrome on Mac: `View > Developer > JavaScript Console`.
	* It's helpful to have the JavaScript console docked on the right side of the browser main window. This will allow you to see the mobile app and JavaScript console output at the same time.

1. **Resize the browser main window**, to simulate the width of a mobile phone. Please be sure that you can see output displayed in the JavaScript console, as you click around the app.
	<details><summary>after gulp deployment the script added a bunch of stuff in AWS in us-east-1</summary><p>

	```sh

	Cloud Formation:
	spacefinder-api-development-stack

	S3

	Lambda:
	spacefinder-api-development-authorizer-Custom
	Custom authorizer function for API Gateway to grant admin-only permissions
	Node.js 4.3
	6.6 MB

	spacefinder-api-development-locations-List
	Returns List of 'Locations'

	spacefinder-api-development-resources-Create
	Add Resource


	spacefinder-api-development-locations-Create
	Adds a Location


	spacefinder-api-development-locations-Delete
	Deletes a Location

	spacefinder-api-development-resources-Get
	Get resource


	spacefinder-api-development-resources-Delete
	Deletes a Resource


	spacefinder-api-development-bookings-Create
	Add Booking


	spacefinder-api-development-resources-List
	Returns list of 'Resources'


	spacefinder-api-development-bookings-Get
	Get booking


	spacefinder-api-development-locations-Get
	Returns a Location

	spacefinder-api-development-bookings-ListByResourceId
	Returns List of Bookings associated with Resource


	spacefinder-api-development-bookings-ListByUserId
	Returns List of Bookings associated with a User


	spacefinder-api-development-bookings-Delete
	Delete booking

	API Gateway:
	Spacefinder-API

	User Pools:
	spacefinder-api-development-userPool

	Federated identity:
	spacefinder_api_development_identityPool
	with User Pool ID

	IAM Roles:
	spacefinder-api-developme-CognitoIdentityPoolAuthA-90VJT18QBP3L
	2017-05-06 20:01 PDT

	spacefinder-api-developme-CognitoIdentityPoolAuthS-DODIL6BLNZUT
	2017-05-06 20:01 PDT

	spacefinder-api-developme-CognitoIdentityPoolUnAut-P1N914OVULXD
	2017-05-06 20:01 PDT

	spacefinder-api-development-st-LambdaExecutionRole-EKSHQDFB9U9
	2017-05-06 20:01 PDT

	```
	</p></details>

# INTERACT AND LEARN (30 minutes)

Interact with the mobile app, and gain insights by viewing the behind-the-scenes info in the browser's JavaScript console. Explore how the AWS resources are configured.

---

### A. Register and Sign-in

1. **Register as a new user in the hybrid mobile app, using your e-mail address.**
  - *Note: Your email address is not tracked or stored or used for any purposes beyond your use in this lab, and is stored in the Cognito User Pool in your AWS account. The Cognito User Pool and other auto-generated AWS resources are un-deployed at the end of this lab.*
1. **Provide confirmation code from e-mail to validate e-mail and confirm registration**
1. **Sign-in as your new user**
	- Review the output in the browser's JavaScript console.
      - *Which JWT tokens are returned from Cognito User Pools?*
      - *Which AWS credential components are returned from Cognito Federated Identities?*
1. **Copy/paste the identity token into the JWT debugger at <http://jwt.io>, and decode it to see the base64-decoded content.**
	- *How long is the identity token valid for before it expires?*
    - *Which attributes are encoded in the token?*
    <details><summary>decoded identity token</summary><p>

	```json
	decoded identity token:
	{
	  "sub": "6527a55d-5498-428b-a997-8213981c70cd",
	  "aud": "13399vooimes62v8ahlnr83gei",
	  "email_verified": true,
	  "token_use": "id",
	  "auth_time": 1494128661,
	  "iss": "https://cognito-idp.us-east-1.amazonaws.com/us-east-1_DvCJ5Zbm1",
	  "cognito:username": "testuser",
	  "exp": 1494132261,
	  "given_name": "test",
	  "iat": 1494128661,
	  "family_name": "user",
	  "email": "qqq@qmail.com"
	}
	```
	</p></details>
1. **Copy/paste the access token into the JWT debugger at <http://jwt.io>, and decode it to see the base64-decoded content.**
	- *How long is the access token valid for before it expires?*
  - *Which attributes are encoded in the token?*
  - *Which attributes also exist in the identity token? Which ones are different?*
	<details><summary>decoded access token</summary><p>

	```json
	decoded access token:
	{
	  "sub": "6527a55d-5498-428b-a997-8213981c70cd",
	  "token_use": "access",
	  "scope": "aws.cognito.signin.user.admin",
	  "iss": "https://cognito-idp.us-east-1.amazonaws.com/us-east-1_DvCJ5Zbm1",
	  "exp": 1494132261,
	  "iat": 1494128661,
	  "jti": "1303a3ae-4396-4820-962b-1eefce7c1087",
	  "client_id": "13399vooimes62v8ahlnr83gei",
	  "username": "testuser"
	}


	```
	</p></details>
---

### B. API Gateway authorization using User Pools Authorizer

1. **Browse to "Resources" tab in the mobile app**
1. **Attempt to load locations "without Auth"**
    - *Based on the JavaScript console output, what was the URL path of the API request that was issued?*
    - *For this User Pools Authorizer request, which HTTP headers were sent?*
	- *What HTTP status code is returned?*
	- *Bonus question: Which HTTP headers are in the HTTP response? (Hint: Look at the Developer Tool's Network tab)*
	<details><summary>HTTP headers</summary><p>

	```http
	GET https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development/locations 401 ()

	XMLHttpRequest cannot load https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development/locations. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://ec2-54-172-240-121.compute-1.amazonaws.com' is therefore not allowed access. The response had HTTP status code 401.

	Request URL:https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development/locations
	Request Method:GET
	Status Code:401 
	Remote Address:54.192.139.33:443
	Response Headers
	content-length:26
	content-type:application/json
	date:Sun, 07 May 2017 03:59:58 GMT
	status:401
	via:1.1 80f1af730b8d1b6fbf7728eeaff9449b.cloudfront.net (CloudFront)
	x-amz-cf-id:rTAqY04x2z76Jge2JYL2IikuGC8aKcI7d76-lg1RcKOT9_E5mZmLbw==
	x-amzn-errortype:UnauthorizedException
	x-amzn-requestid:a2e66743-32d9-11e7-be8c-3540dc96fff3
	x-cache:Error from cloudfront

	```
	</p></details>
1. **Attempt to load locations "with Auth"**
    - *What was the URL path of the API request that was issued?*
    - *For this User Pools Authorizer request, which HTTP headers were sent as part of the HTTP request?*
	<details><summary>HTTP headers</summary><p>
	"authorization" => "eyJraWQiOiJpK29xRmhHKzJcL0RtNXBVUWNMbGE..." - this is identity token, received in when sign in in previous steps

	```json
	GET https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development/locations 
	Headers: ...
		0: {"authorization" => "eyJraWQiOiJpK29x_shorter_iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiI2NTI3YTU1ZC01NDk4LTQyOGItYTk5Ny04MjEzOTgxYzcwY2QiLCJhdWQiOiIxMzM5OXZvb2ltZXM2MnY4YWhsbnI4M2dlaSIsImVtYWlsX3ZlcmlmaWVkIjp0cnVlLCJ0b2tlbl91c2UiOiJpZCIsImF1dG_shorter_YWlsLmNvbSJ9.FZDcSmBpNSkhXL7-GlyNehYzS5_shorter_fsdJmPptSsAeP77nQTfZSqN--SbLmGqrap37rIsdfsHIRBHicbxQoKd0TdiurGDfDecDKiuYgim7UgIfUA0Dn1h8smg"}

	Request URL:https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development/locations
	Request Method:OPTIONS
	Status Code:200 
	Remote Address:54.192.139.121:443
	Response Headers
	access-control-allow-headers:Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token
	access-control-allow-methods:GET,POST
	access-control-allow-origin:*
	content-length:0
	content-type:application/json
	date:Sun, 07 May 2017 04:08:17 GMT
	status:200
	via:1.1 cac0807f4e1bdd7cf57c08992aa341a5.cloudfront.net (CloudFront)
	x-amz-cf-id:owAkRIPH2NxIOTkRZY6nnEyyuk6BMWtlxIuxU9bh-UHo75k6k6JEHA==
	x-amzn-requestid:cc89ee1d-32da-11e7-b80b-d5ee3d14c01d
	x-cache:Miss from cloudfront

	```
	</p></details>
---
### C. API Gateway authorization using IAM Authorization
1.	**Click on a location**
	- *For this IAM Authorization request, which HTTP headers were sent?*
	- *How are the HTTP headers sent for IAM Authorization requests different than those sent for User Pool Authorizer requests?*
	- *Note that for IAM Authorization requests, a signature ("SigV4 signature") is calculated and sent as HTTP request headers. Note that User Pool Authorizer requests do not make use of any signatures.*
	<details><summary>HTTP headers</summary><p>

	```http
	GET https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development/locations/03cb41b0-32d3-11e7-a1c8-7994ba5037c2/resources 
	Headers: 
		0: {"authorization" => "AWS4-HMAC-SHA256 Credential=ASIAIUDVGYA37YIW2YQQ/20170507/us-east-1/execute-api/aws4_request, SignedHeaders=accept;content-type;host;x-amz-date, Signature=8a9caed90dee588215d282c5361722d0c2279777888385ceea89b78c44f832de"}
		1: {"accept" => "application/json"}
		2: {"content-type" => "application/json"}
		3: {"x-amz-date" => "20170507T042235Z"}
		4: {"x-amz-security-token" => "AgoGb3JpZ_shortcut_jHc+de9kgsfwsNds8DXwe+6OuEJVE8wwi8K6yAU="}

	```
	</p></details>
1.	**Select a resource (desk or conference room). Book one of the available timeslots.**
	- *Which HTTP method (GET/POST/PUT/DELETE) was called?*
	- *For this IAM Authorization request, which HTTP headers were sent?*
	- *Note that with IAM Authorization signing (aka "SigV4 signing"), the request URI, request body, and HTTP method are all used to calculate the signature.*
	<details><summary>List available bookings</summary><p>

	```http
	Resource Availability  
	spacefinder-api.service.ts:62 IAM Authorization Request:
	GET https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development/location…-a1c8-7994ba5037c2/resources/03d52cc0-32d3-11e7-a1c8-7994ba5037c2/bookings 
	Headers: Map {
		"authorization" => ["AWS4-HMAC-SHA256 Credential=ASIAIRZ42PWU7HBD63QA/20170507/us-east-1/execute-api/aws4_request, SignedHeaders=accept;content-type;host;x-amz-date, Signature=e49e66088285d1f6c8260fbfd08786fd50c406f4cc72a856cb1bc3287b4774f5"], 
		"accept" => ["application/json"], 
		"content-type" => ["application/json"], 
		"x-amz-date" => ["20170507T043841Z"], 
		"x-amz-security-token" => ["AgoGb3JpZ2luEBAaCXVzLWVhc3QtMSKAApvG0ZpyeSn9srQkCs…LvozKxonYnqv8DtJS/HLS8wNeAgQZeGRehG51E8Mw0cm6yAU="]}

	```
	</p></details>

	<details><summary>Book a space</summary><p>

	```http
	IAM Authorization Request:
	POST https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development/users/us-east-1:be1369a9-8f73-4670-84bf-90e3c4310759/bookings 
	Headers: Map {
			"authorization" => ["AWS4-HMAC-SHA256 Credential=ASIAIRZ42PWU7HBD63QA/20170507/us-east-1/execute-api/aws4_request, SignedHeaders=accept;content-type;host;x-amz-date, Signature=920796271b4bafb711e6f523002671ce48317e747e7e19cb2ecc66df26ff43ba"], 
			"accept" => ["application/json"], 
			"content-type" => ["application/json"], 
			"x-amz-date" => ["20170507T044306Z"], 
			"x-amz-security-token" => ["AgoGb3JpZ2luEBAaCXVzLWVhc3QtMSKAApvG0ZpyeSn9srQkCs…LvozKxonYnqv8DtJS/HLS8wNeAgQZeGRehG51E8Mw0cm6yAU="]} 

	Body: {"locationId":"03cb41b0-32d3-11e7-a1c8-7994ba5037c2","locationName":"Encore","resourceId":"03d52cc0-32d3-11e7-a1c8-7994ba5037c2","resourceName":"Desk 1","resourceDescription":"Comfortable desk","startTimeIsoTimestamp":"2017-05-06T06:00:00.000Z","startTimeEpochTime":1494050400000,"endTimeIsoTimestamp":"2017-05-06T07:00:00.000Z","endTimeEpochTime":1494054000000,"timeslot":"6am - 7am","userId":"us-east-1:be1369a9-8f73-4670-84bf-90e3c4310759","userFirstName":"test","userLastName":"user"}

	```
	</p></details>
1. **Browse to "Bookings" tab, and cancel the booking you just made.**
	- *Which HTTP method (GET/POST/PUT/DELETE) was called?*
	<details><summary>List My Bookings</summary><p>

	```http
	My Bookings  
	spacefinder-api.service.ts:62 IAM Authorization Request:
	GET https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development/users/us-east-1:be1369a9-8f73-4670-84bf-90e3c4310759/bookings 
	Headers: 
	Map {"authorization" => ["AWS4-HMAC-SHA256 Credential=ASIAJF446JAEPTR42IQQ/20170507/us-east-1/execute-api/aws4_request, SignedHeaders=accept;content-type;host;x-amz-date, Signature=c6a4b4aa4fc9ae38459685bd0cf9dd407d66a11c43840a9c2b6d8acb39c74b8f"], "accept" => ["application/json"], "content-type" => ["application/json"], "x-amz-date" => ["20170507T044558Z"], "x-amz-security-token" => ["AgoGb3JpZ2luEBEaCXVzLWVhc3QtMS_shortcut_iLTYxAib0Hzh3YoaxNLWvEwhs26yAU="]}


	```
	</p></details>

	<details><summary>Cancel Booking</summary><p>

	```http

	IAM Authorization Request:
	DELETE https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development/users/us…-8f73-4670-84bf-90e3c4310759/bookings/aac459a0-32df-11e7-87d4-6d0e492cccb7 
	Headers: Map {"authorization" => ["AWS4-HMAC-SHA256 Credential=ASIAJF446JAEPTR42IQQ/2…38abd4e41431ce0187f9436b9425f0bb887b6c770d4d57a93"], "accept" => ["application/json"], "content-type" => ["application/json"], "x-amz-date" => ["20170507T045257Z"], "x-amz-security-token" => ["AgoGb3JpZ2luEBEaCXVzLWVhc3QtMSKAAjZS5OQzDCMqbUm5Xd…bKc7Svjy0wuXZjYxoiiLTYxAib0Hzh3YoaxNLWvEwhs26yAU="]}

	```
	</p></details>
---

### D. S3 authorization using IAM Authorization

1. **Browse to "Account" tab.**
1. **Select to upload an local image as your profile picture. Any image will do.**
    - *This image will be stored directly in the S3 bucket that was created in your AWS account as part of the setup process. The image is uploaded directly to S3 using the AWS JavaScript S3 SDK (that is, it didn't go through API Gateway). The authorization headers are the same headers as those used earlier by API Gateway when configured to use IAM Authorization. This is because both API Gateway's IAM Authorization and the AWS SDKs rely on the same standard "SigV4 signing" method.*
	- *Temporary AWS credentials were used to sign the PUT request to the S3 API. How were these temporary AWS credentials obtained?*

---

### E. Fine-grained access control using IAM Authorization

1.	**You are currently logged in without Admin privileges. Toggle on "View Admin features", available in the "Account" tab"**. Note that you are NOT signed in currently as an administrator, so even though you can enable the display of Admin-only features, note that due to API authorization settings, the Admin-only API calls will still not work.
1.	**Browse back to "Resources" tab**
1.	**Click on "Add a location"**
1.	**Fill out the form and click on the “Add location” button**
	- *As expected, your API request is rejected because you don't have permissions to perform this API backend operation (because your account is not in the administrators group).*
	- *Bonus question: The HTTP response body will explain why your API request was denied. What is the HTTP response body? (Hint: Look at the Developer Tool's Network tab)*

	<details><summary>Add location</summary><p>

	```http
	Request URL:https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development/locations
	Request Method:POST
	Status Code:403 
	Remote Address:54.192.139.78:443
	Response Headers
	content-length:293
	content-type:application/json
	date:Sun, 07 May 2017 05:07:11 GMT
	status:403
	via:1.1 5534399806e3c9343716ffe6cbeb2f01.cloudfront.net (CloudFront)
	x-amz-cf-id:ZVrrn9fZCambD-cF7JjauvG79v_IOMdHo0yueXT6p_eavugoYtTRnQ==
	x-amzn-errortype:AccessDeniedException
	x-amzn-requestid:06d61e4d-32e3-11e7-bbc2-1dc322dd78e9
	x-cache:Error from cloudfront
	Request Headers
	:authority:qyt4c7jmsd.execute-api.us-east-1.amazonaws.com
	:method:POST
	:path:/development/locations
	:scheme:https
	accept:application/json
	accept-encoding:gzip, deflate, br
	accept-language:en-US,en;q=0.8
	authorization:AWS4-HMAC-SHA256 Credential=ASIAIOUN5OIMCSW3XJ5Q/20170507/us-east-1/execute-api/aws4_request, SignedHeaders=accept;content-type;host;x-amz-date, Signature=dd464bfd1a89e755260a8ae6cbfc1139c3e7b8e185c00ffaf53ad7118c22b5d6
	content-length:119
	content-type:application/json
	origin:http://ec2-54-172-240-121.compute-1.amazonaws.com
	referer:http://ec2-54-172-240-121.compute-1.amazonaws.com/
	user-agent:Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Ubuntu Chromium/56.0.2924.76 Chrome/56.0.2924.76 Safari/537.36
	x-amz-date:20170507T050710Z
	x-amz-security-token:AgoGb3JpZ2luEBEaCXVzLWVhc3QtMSKAAgPYQuEZepjuPKs_shortcut_WCx84KwBQaBfzQuEu/CYd3xwVsMwwda6yAU=

	Request Payload
	view source
	{name: "qq", description: "qq",…}
	description:"qq"
	imageUrl:"https://s3.amazonaws.com/spacefinder-public-image-repository/building.png"
	name:"qq"

	```
	</p></details>

	<details><summary>Response</summary><p>

	```http
	Response:
	{"Message":"User: arn:aws:sts::989972760655:assumed-role/spacefinder-api-developme-CognitoIdentityPoolAuthS-DODIL6BLNZUT/CognitoIdentityCredentials is not authorized to perform: execute-api:Invoke on resource: arn:aws:execute-api:us-east-1:********0655:qyt4c7jmsd/development/POST/locations"}

	```
	</p></details>
---

### F. Fine-grained access control using Custom Authorizers

1. **Click on the arrow located in the upper left of the app (next to the `Add a Location` title), to return to the previous screen.**
	<details><summary>Locations</summary><p>

	```http
	  Locations  
	spacefinder-api.service.ts:81 User Pools Authorizer Request:
	GET https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development/locations 
	Headers: 
	{"authorization" => "eyJraWQiOiJpK29xRmhHKzJcL_shortcut_iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiI2NTI3YTU1Z_shortcut_WwiOiJ2aWN0b3JzZmJhQGdtYWlsLmNvbSJ9.FZDcSmBpNSkhXL7-Gl_shortcut_HIRBHicbxQoKd0TdiurGDfDecDKiuYgim7UgIfUA0Dn1h8smg"}

	```
	</p></details>
	<details><summary>Response</summary><p>

	```http

	Response:
	{"message":"Identity token has expired"}
	```
	</p></details>
	<details><summary>Sign-Out -> Sign-In (token expired)</summary><p>

	```http
	Sign Out  
	logger.service.ts:13   Sign-In  
	account-management.service.ts:317 Authenticating user testuser
	account-management.service.ts:331 Cognito User Pools Identity Token:  eyJraWQiOiJpK29xRmhHKzJcL_shortcut_iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiI2NTI3YTU1Z_shortcut_WwiOiJ2aWN0b3JzZmJhQGdtYWlsLmNvbSJ9.DL9bq2T6mX_shortcut_ezYQtnj4PVdAU7N629VTinH7SXTbA
	account-management.service.ts:333 Cognito User Pools Access Token:  eyJraWQiOiJ2YWNPOGNHbnpiT_shortcut_iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiI2NTI3YTU1Z_shortcut_2VpIiwidXNlcm5hbWUiOiJ0ZXN0dXNlciJ9.Tc2aL3FGgR_shortcut_cIGtiXB-67Ql3oorOY9nJpVT40Olg
	account-management.service.ts:335 Cognito User Pools Refresh Token:  
	eyJjdHkiOiJKV1QiLCJlbmMiO_shortcut_WxnIjoiUlNBLU9BRVAifQ.u8lMapLOiZ7vXE-_BaBsg_shortcut_QjMNwFe5W42wuHCUZFLYEnfTwmoXQ.mOKNTQ0SVhAXD27b.Tj3u_shortcut_...very_lengthy_AxB7-nqmQEo3SWf84RYgH2-w7ap9Tc.laATfig17jCU2WxGxqdDfw
	account-management.service.ts:355 Cognito User Pools User Groups :testuser belongs to group clientGroup
	account-management.service.ts:545 Cognito User Pools User Attributes:  Object {sub: "6527a55d-5498-428b-a997-8213981c70cd", email_verified: "true", given_name: "test", family_name: "user", email: "victorsfba@gmail.com"}
	account-management.service.ts:365 Cognito Identity ID:  us-east-1:be1369a9-8f73-4670-84bf-90e3c4310759
	account-management.service.ts:367 AWS Access Key ID:  ASIAILYWSQRMGOARZ5XQ
	account-management.service.ts:369 AWS Secret Access Key:  pwovW2nVu1Hbs5TH33ujoZABqq5zaYaj1wbBwJai
	account-management.service.ts:371 AWS Session Token:  AgoGb3JpZ2luEBEaCXVzLWVhc3QtMSKAAoH9N_shortcut_YpWD4wq9y6yAU=
	```
	</p></details>
	<details><summary>Locations</summary><p>

	```http
	Locations  
	spacefinder-api.service.ts:81 User Pools Authorizer Request:
	GET https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development/locations 
	Headers: Map {"authorization" => ["eyJraWQiOiJpK29xRmhHKzJcL0RtNXBVUWNMbGE3bW5aSWRxRG…9rheK5o25NA6Lu-NWNReezYQtnj4PVdAU7N629VTinH7SXTbA"]} //identity token

	```
	</p></details>

1. **Click on "Load locations with Auth", and click on a location of your choice.**
1. **When viewing the resources for the location, click the "Delete" button next to one of the conference rooms/desks. Click "OK" to confirm when the confirmation dialog pops up.**
    - *An API Gateway Custom Authorizer is associated with this API operation. The Custom Authorizer logic evaluates the user's identity token and corresponding Cognito User Pools group memebership. Since your user isn't in the administrators group, the Custom Authorizer returns an IAM policy that effectively denies the user from performing this API operation.*
	<details><summary>Trying to delete location</summary><p>

	```http
	Custom Authorizer Request:
	DELETE https://qyt4c7jmsd.execute-api.us-east-1.amazonaws.com/development/locations/03cb41b0-32d3-11e7-a1c8-7994ba5037c2 
	Headers: Map {"authorization" => ["eyJraWQiOiJpK29xRmhHKzJcL0RtNXBVUWNMbGE3bW5aSWRxRG_shortcut_9rheK5o25NA6Lu-NWNReezYQtnj4PVdAU7N629VTinH7SXTbA"]}

	Response:
	{"Message":"User is not authorized to access this resource"}

	```
	</p></details>

---
    
### G. Testing User Pool Authorizers using the API Gateway console

1.	**In a new browser tab/window, sign in to the AWS Management Console. Navigate to the API Gateway console.**
1.	**Click to view details for the "Spacefinder-API"**
2.	**In the lefthand menu, click "Authorizers" to view the Authorizers associated with 'Spacerfinder-API'**
3.  **View the details of the "spacefinder-userPool-authorizer"**
4.  **Copy your user's identity token from the JavaScript console in the other browser tab/window. (You may need to scroll back to the beginning of your JavaScript console to find the identity token.)**
5.  **Return back to the "spacefinder-userPool-authorizer" detail screen. In the righthand pane, scroll down to the "Test your authorizer" section. Paste the identity token into the "Identity token" textfield, and click "Test".**
    - *Assuming you pasted a valid token, you should see your decoded identity token in the "Claims" textbox. This signifies that you would pass this yes/no authorization check.*
6. **Make some minor edit to the value of the "Identity token" textfield, and click "Test" again.**
    - *You should now see an "Unauthorized request" message, which indicates that if this invalid token were to be sent to this API operation, it would result in a denied request.*
    
---

### H. Testing Custom Authorizers using the API Gateway console
    
1.  **While still on the "Authorizers" screen, click on 'spacefinder-custom-authorizer' to view details.**
1.  **In the righthand pane, scroll down to the "Test your authorizer" section. Paste the identity token into the "Identity token" textfield, and click "Test"**
    - *You should see an IAM Policy with permissions to Allow restricted access to the API operations. The IAM Policy also has explicit Deny statements to block the creation or deletion of locations/resources.*
	<details><summary>first test expired identity token</summary><p>

	```json
	Policy
	{
	  "Version": "2012-10-17",
	  "Statement": [
	    {
	      "Action": "execute-api:Invoke",
	      "Effect": "Deny",
	      "Resource": [
		"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/null/*/*"
	      ]
	    }
	  ]
	}
	```
	</p></details>

	<details><summary>Logs</summary><p>

	```http

	Execution log for request test-request
	Sun May 07 05:37:15 UTC 2017 : Starting authorizer: 2egbc3 for request: test-request
	Sun May 07 05:37:15 UTC 2017 : Incoming identity: eyJraWQiOiJpK29xRmhHKzJcL_shortcut_iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiI2NTI3YTU1Z_shortcut_WwiOiJ2aWN0b3JzZmJhQGdtYWlsLmNvbSJ9.FZDcSmBpNS_shortcut_DKiuYgim7UgIfUA0Dn1h8smg
	Sun May 07 05:37:15 UTC 2017 : Endpoint request URI: https://lambda.us-east-1.amazonaws.com/2015-03-31/functions/arn:aws:lambda:us-east-1:989972760655:function:spacefinder-api-development-authorizer-Custom/invocations
	Sun May 07 05:37:15 UTC 2017 : Endpoint request headers: {x-amzn-lambda-integration-tag=test-request, Authorization=****************************************************************************************************************************************************************************************************************************************************************************************************************************************1ed243, X-Amz-Date=20170507T053715Z, x-amzn-apigateway-api-id=qyt4c7jmsd, X-Amz-Source-Arn=arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/authorizers/2egbc3, Accept=application/json, User-Agent=AmazonAPIGateway_qyt4c7jmsd, X-Amz-Security-Token=FQoDYXdzECUaDHgYEL/vboC4Di_shortcut_6HXaqkurgjWsgwgNE6PmOqI1h9TsKBbMrzHJvppyrQArGPOj6ItWwk3YW/Sj4l2hH6 [TRUNCATED]
	Sun May 07 05:37:15 UTC 2017 : Endpoint request body after transformations: {"type":"TOKEN","authorizationToken":
	"eyJraWQiOiJpK29xRmhHKzJcL_shortcut_iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiI2NTI3YTU1Z_shortcut_WwiOiJ2aWN0b3JzZmJhQGdtYWlsLmNvbSJ9.FZDcSmBpN_shortcut_DKiuYgim7UgIfUA0Dn1h8smg","methodArn":"arn:aws:execute-ap [TRUNCATED]
	Sun May 07 05:37:15 UTC 2017 : Authorizer result body before parsing: {"principalId":"","policyDocument":{"Version":"2012-10-17","Statement":[{"Action":"execute-api:Invoke","Effect":"Deny","Resource":["arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/null/*/*"]}]}}
	Sun May 07 05:37:15 UTC 2017 : Using valid authorizer policy for principal: 
	Sun May 07 05:37:15 UTC 2017 : Successfully completed authorizer execution

	```
	</p></details>

	<details><summary>Now with valid token</summary><p>

	```json
	Principal Id: 6527a55d-5498-428b-a997-8213981c70cd
	Policy
	{
	  "Version": "2012-10-17",
	  "Statement": [
	    {
	      "Action": "execute-api:Invoke",
	      "Effect": "Allow",
	      "Resource": [
		"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/null/*/*"
	      ]
	    },
	    {
	      "Action": "execute-api:Invoke",
	      "Effect": "Deny",
	      "Resource": [
		"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/null/DELETE/locations",
		"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/null/DELETE/locations/*",
		"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/null/POST/locations",
		"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/null/POST/locations/*"
	      ]
	    }
	  ]
	}
	```
	</p></details>

	<details><summary>Logs</summary><p>

	```http

	Execution log for request test-request
	Sun May 07 05:41:54 UTC 2017 : Starting authorizer: 2egbc3 for request: test-request
	Sun May 07 05:41:54 UTC 2017 : Incoming identity: eyJraWQiOiJpK29xRmhHKzJcL0RtN_shortcut_dCRT0iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiI2N_shortcut_J2aWN0b3JzZmJhQGdtYWlsLmNvbSJ9.DL9bq2T6mXwcCGadf9GK3jKtFNbaDbvXHhwA5RwsFIXNVlBNya5V7RWHkf-Q8bjsG_shortcut_89rheK5o25NA6Lu-NWNReezYQtnj4PVdAU7N629VTinH7SXTbA
	Sun May 07 05:41:54 UTC 2017 : Endpoint request URI: https://lambda.us-east-1.amazonaws.com/2015-03-31/functions/arn:aws:lambda:us-east-1:989972760655:function:spacefinder-api-development-authorizer-Custom/invocations
	Sun May 07 05:41:54 UTC 2017 : Endpoint request headers: {x-amzn-lambda-integration-tag=test-request, Authorization=****************************************************************************************************************************************************************************************************************************************************************************************************************************************0fbbc0, X-Amz-Date=20170507T054154Z, x-amzn-apigateway-api-id=qyt4c7jmsd, X-Amz-Source-Arn=arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/authorizers/2egbc3, Accept=application/json, User-Agent=AmazonAPIGateway_qyt4c7jmsd, X-Amz-Security-Token=FQoDYXdzECYaDABjmUmzE/14JQTPViK3A_shortcut_R4oEtlMTqaHkud/hh0w2Xp+u4heOROidfrC3OjSMymv [TRUNCATED]
	Sun May 07 05:41:54 UTC 2017 : Endpoint request body after transformations: {"type":"TOKEN","authorizationToken":
	"eyJraWQiOiJpK29xRmhHKzJcL0RtN_shortcut_dCRT0iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiI2N_shortcut_J2aWN0b3JzZmJhQGdtYWlsLmNvbSJ9.DL9bq2T6mXwcCGadf9GK3jKtFNbaDbvXHhwA5RwsFIXNVlBNya5V7RWHkf-Q8bjsG_shortcut_89rheK5o25NA6Lu-NWNReezYQtnj4PVdAU7N629VTinH7SXTbA","methodArn":"arn:aws:execute-ap [TRUNCATED]
	Sun May 07 05:41:54 UTC 2017 : Authorizer result body before parsing: {"principalId":"6527a55d-5498-428b-a997-8213981c70cd","policyDocument":{"Version":"2012-10-17","Statement":[{"Action":"execute-api:Invoke","Effect":"Allow","Resource":["arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/null/*/*"]},{"Action":"execute-api:Invoke","Effect":"Deny","Resource":["arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/null/DELETE/locations","arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/null/DELETE/locations/*","arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/null/POST/locations","arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/null/POST/locations/*"]}]}}
	Sun May 07 05:41:54 UTC 2017 : Using valid authorizer policy for principal: ******************************1c70cd
	Sun May 07 05:41:54 UTC 2017 : Successfully completed authorizer execution


	```
	</p></details>
---

### I. Logging in as an Admin

1. **Sign out, and sign back in as an Admin. Administrators can create and delete locations/resources. Sign-in as username `admin1` and default password `Test123!`**
1. **Copy the identity token displayed in the JavaScript console. Be sure to get the most recently displayed identity token.**
1. **Browse to "Resources" tab.**
1. **Click on "Load locations with Auth", and click on a location of your choice.**
1. **When viewing the resources for the location, click on the "Delete" button next to a conference room/desk. Click 'OK' to confirm when the dialog pops up.**
    - *An API Gateway Custom Authorizer is associated with this API operation. The Custom Authorizer logic evaluates the user's identity token and corresponding Cognito User Pools group membership. Since your user is in the administrators group, the Custom Authorizer returns an IAM policy that effectively allows the user to perform this API operation.*
1. **Click on the arrow located in the upper left of the app (next to the `Resources` title), to return to the previous screen.**
1. **Click on "Add a location"**
1.	**Fill out the form and click on the “Add location” button. You may use the default image URL for a default location image.**
	- *This time, your API request is successful, since your account is part of the administrators group, which is associated with an IAM role that has permissions to perform this API operation.*
1. **Click on "Load locations with Auth". You new location with an image should appear in the locations list.**

---

### J. Testing Authorizers using the API Gateway console, part 2

1.	**Return to the browser tab/window showing the API Gateway console.**
1.  **Navigate to the 'spacefinder-userPool-authorizer' detail screen. In the righthand pane, scroll down to the "Test your authorizer" section. Paste in the admin user's identity token into the "Identity token" textfield, and click "Test".**
    - *Assuming you pasted a valid token, you should see your decoded identity token in the "Claims" textbox. This signifies that you would pass this yes/no authorization check.*
    <details><summary>Test result</summary><p>

	```json
	{
	  "sub": "0dd6df5d-cc37-4c07-a144-fd67d68637fc",
	  "cognito:groups": "adminGroup",
	  "cognito:preferred_role": "arn:aws:iam::989972760655:role/spacefinder-api-developme-CognitoIdentityPoolAuthA-90VJT18QBP3L",
	  "iss": "https://cognito-idp.us-east-1.amazonaws.com/us-east-1_DvCJ5Zbm1",
	  "cognito:username": "admin1",
	  "given_name": "Admin",
	  "cognito:roles": "arn:aws:iam::989972760655:role/spacefinder-api-developme-CognitoIdentityPoolAuthA-90VJT18QBP3L",
	  "aud": "13399vooimes62v8ahlnr83gei",
	  "token_use": "id",
	  "auth_time": "1494136038",
	  "exp": "Sun May 07 06:47:18 UTC 2017",
	  "iat": "Sun May 07 05:47:18 UTC 2017",
	  "family_name": "User",
	  "email": "admin@example.com"
	}
	```
	</p></details>
1.  **While still on the "Authorizers" screen, click on 'spacefinder-custom-authorizer' to view the details for that authorizer.**
1.  **In the righthand pane, scroll down to the "Test your authorizer" section. Paste in the admin user's identity token into the "Identity token" textfield, and click "Test".**
    - *You should see an IAM Policy with permissions to Allow access to the API operations. Notice that the explicity Deny statements (when we tested earlier using the identity token associated with a non-Admin) are no longer present in the IAM Policy.*
	</p></details>
	<details><summary>Test result</summary><p>

	```json
	{
	  "Version": "2012-10-17",
	  "Statement": [
	    {
	      "Action": "execute-api:Invoke",
	      "Effect": "Allow",
	      "Resource": [
		"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/null/*/*"
	      ]
	    }
	  ]
	}
	```
	</p></details>

---

### K. Exploring custom authorizer implementation details

1.	**Browse to the AWS Lambda console**
1.	**Click "Create a Lambda function"**
1.	**Enter "authorizer" in the filter search box**
1.	**Choose the custom authorizer Lambda blueprint for either Node.js or Python**
1.	**Do not add any triggers and click next**
1.	**Scroll down to the in-line text editor to see the Lambda blueprint sample code.**
1.	**Browse to <https://github.com/awslabs/aws-serverless-auth-reference-app/blob/master/api/lambda/authorizer.js>**
	- *This is the source code of the custom authorizer used for this project.*
	- *Which validations are performed against the HTTP request's authorization header passed to Lambda as part of the event object?*
	- *Since a custom authorizer should always return an effective IAM policy for a token sent to it, the project returns a “deny-all” policy for any invalid or malformed tokens.*
	
---

### <a id="headKK"></a> KK. Other things in console
	

<details><summary>CognitoIdentityPoolAuthStandardPolicy</summary><p>

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": "execute-api:Invoke",
            "Resource": [
                "arn:aws:execute-api:us-east-1:989972760655:bui2h4chd5/*/*/locations",
                "arn:aws:execute-api:us-east-1:989972760655:bui2h4chd5/*/*/locations/*",
                "arn:aws:execute-api:us-east-1:989972760655:bui2h4chd5/*/*/users/${cognito-identity.amazonaws.com:sub}",
                "arn:aws:execute-api:us-east-1:989972760655:bui2h4chd5/*/*/users/${cognito-identity.amazonaws.com:sub}/*"
            ],
            "Effect": "Allow"
        },
        {
            "Action": "execute-api:Invoke",
            "Resource": [
                "arn:aws:execute-api:us-east-1:989972760655:bui2h4chd5/*/DELETE/locations",
                "arn:aws:execute-api:us-east-1:989972760655:bui2h4chd5/*/DELETE/locations/*",
                "arn:aws:execute-api:us-east-1:989972760655:bui2h4chd5/*/POST/locations",
                "arn:aws:execute-api:us-east-1:989972760655:bui2h4chd5/*/POST/locations/*"
            ],
            "Effect": "Deny"
        },
        {
            "Action": [
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::spacefinder-api-development-stack-userdatabucket-13nd1e9ksak42/${cognito-identity.amazonaws.com:sub}/*",
            "Effect": "Allow"
        }
    ]
}
```
</p></details>

<details><summary>CognitoIdentityPoolAuthAdminPolicy</summary><p>

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": "execute-api:Invoke",
            "Resource": [
                "arn:aws:execute-api:us-east-1:989972760655:bui2h4chd5/*/*/*"
            ],
            "Effect": "Allow"
        },
        {
            "Action": [
                "s3:*"
            ],
            "Resource": "arn:aws:s3:::spacefinder-api-development-stack-userdatabucket-13nd1e9ksak42/*",
            "Effect": "Allow"
        }
    ]
}
```
</p></details>

<details><summary>LambdaExecutionPolicy</summary><p>

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "dynamodb:*",
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "*",
            "Effect": "Allow"
        }
    ]
}
```
</p></details>

spacefinder-api-development-authorizer-Custom -> Role: spacefinder-api-development-st-LambdaExecutionRole-1IO87ERIY4ZO8

<details><summary>spacefinder-api-development-authorizer-Custom -> function policy</summary><p>

```json
{
    "Version": "2012-10-17",
    "Id": "default",
    "Statement": [
        {
            "Sid": "apigateway-invoke-permissions",
            "Effect": "Allow",
            "Principal": {
                "Service": "apigateway.amazonaws.com"
            },
            "Action": "lambda:InvokeFunction",
            "Resource": "arn:aws:lambda:us-east-1:989972760655:function:spacefinder-api-development-authorizer-Custom"
        }
    ]
}

```
</p></details>

<details><summary>List of Lambdas</summary><p>

**spacefinder-api-development-authorizer-Custom**,  
Custom authorizer function for API Gateway to grant admin-only permissions,  
Node.js 4.3, 6.6 MB  
Handler: authorizer.Custom  
Existing role: spacefinder-api-development-st-LambdaExecutionRole-1N1L36TATXF2Y

**spacefinder-api-development-bookings-Create**,  
Add Booking,  
Node.js 4.3, 6.6 MB  
Handler: bookings.Create
Existing role: spacefinder-api-development-st-LambdaExecutionRole-1N1L36TATXF2Y

**spacefinder-api-development-resources-Delete**,  
Deletes a Resource,  
Node.js 4.3, 6.6 MB  
Handler: resources.Delete
Existing role: spacefinder-api-development-st-LambdaExecutionRole-1N1L36TATXF2Y

**spacefinder-api-development-resources-Create**,  
Add Resource,  
Node.js 4.3, 6.6 MB  
Handler: resources.Create  
Existing role: spacefinder-api-development-st-LambdaExecutionRole-1N1L36TATXF2Y

**spacefinder-api-development-locations-Get**,  
Returns a Location,  
Node.js 4.3, 6.6 MB  
Handler: locations.Get  
Existing role: spacefinder-api-development-st-LambdaExecutionRole-1N1L36TATXF2Y  

**spacefinder-api-development-locations-List**,  
Returns List of 'Locations',  
Node.js 4.3, 6.6 MB  
Handler: locations.List  
Existing role: spacefinder-api-development-st-LambdaExecutionRole-1N1L36TATXF2Y  

**spacefinder-api-development-bookings-ListByUserId**,  
Returns List of Bookings associated with a User,  
Node.js 4.3, 6.6 MB  
Handler: bookings.ListByUserId  
Existing role: spacefinder-api-development-st-LambdaExecutionRole-1N1L36TATXF2Y

**spacefinder-api-development-bookings-ListByResourceId**,  
Returns List of Bookings associated with Resource,  
Node.js 4.3, 6.6 MB  
Handler: bookings.ListByResourceId  
Existing role: spacefinder-api-development-st-LambdaExecutionRole-1N1L36TATXF2Y

**spacefinder-api-development-resources-List**,
Returns list of 'Resources',
Node.js 4.3, 6.6 MB

**spacefinder-api-development-bookings-Delete**,
Delete booking,
Node.js 4.3, 6.6 MB

**spacefinder-api-development-locations-Delete**,
Deletes a Location,
Node.js 4.3, 6.6 MB

**spacefinder-api-development-locations-Create**,
Adds a Location,
Node.js 4.3, 6.6 MB

**spacefinder-api-development-bookings-Get**,
Get booking,
Node.js 4.3, 6.6 MB

**spacefinder-api-development-bookings-Delete**,
Delete booking,
Node.js 4.3, 6.6 MB

</p></details>

---

### L. Exploring the Cognito User Pools console

1.	**Browse to the Cognito User Pools console**
1.	**Select the "spacefinder-api-..." user pool**
1.	**Select "Users and Groups" from the left-hand panel**
1.	**Review your users in the right-hand panel**
1.	**Click on the "Groups" tab in the right-hand panel**
    - The precedence value indicates which group’s role should be chosen for a user if a user is a member of multiple groups
1.	**Click on the "adminGroup" to review the group details for standard users**
    - *Which IAM role is assigned to this group?*
    - *Can the IAM role and precedence settings be edited?*
    <details><summary>adminGroup</summary><p>

	```sh
	Description	Cognito user group for administrators
	Role ARN	arn:aws:iam::989972760655:role/spacefinder-api-developme-CognitoIdentityPoolAuthA-90VJT18QBP3L
	Precedence	0
	Updated	May 7, 2017 3:02:20 AM
	Created	May 7, 2017 3:02:20 AM
	```
	</p></details>
1.	**Click on the "Groups" breadcrumb in the right-hand panel to go back**
1.	**Click on the "clientGroup" to review its settings**
    - *Is a different IAM role assigned to this group than the "adminGroup?"*
    <details><summary>clientGroup</summary><p>

	```sh
	Description	Cognito user group for spacefinder users
	Role ARN	arn:aws:iam::989972760655:role/spacefinder-api-developme-CognitoIdentityPoolAuthS-DODIL6BLNZUT
	Precedence	1
	Updated	May 7, 2017 3:02:20 AM
	Created	May 7, 2017 3:02:20 AM
	```
	</p></details>
1.	**Click on Attributes in the left-hand panel**
    - *Are there any required attributes for new users in this user group?*
    - *Are there any custom attributes for users in this user group?*
1.	**Explore the Verifications section**
    - *This section governs requirements for new sign-ups and ongoing user sign-ins.*
    - *Notice the requirement of e-mail verification, hence the earlier confirmation code message.*
1.	**Evaluate the "apps" section**
    - *We only have one app for this project in this case since users only interact via the Ionic 2 mobile/web app.*
    - *"No Admin SRP" is required for the backend "gulp deploy" process (which you ran during setup) to be able to programatically login as our sample users to configure them and set their permanent passwords.*
    - *For production use, SRP is a best practice and "no admin SRP" auth should only be used for system logins with the user pool such as for user import/migration or for test automation purposes. In such cases, having a separate app for each backend system with this property set is advisable so client apps are required to use SRP in all cases.*
    - *A client secret is not needed for a JavaScript application, but can optionally be required for apps in other languages.*
    - *Additionally, different apps can have access to different attributes of the user pool where the identity token returned from Cognito User Pools for that app will only include attributes the given app has "read access" to.*

---

### M. Exploring the Cognito Federated Identities console

1.	**Browse to "Cognito Federated Identities" console**
1.	**Select the "Spacefinder" identity pool**
1.	**Click "Edit Identity Pool" in the top right**
1.	**Scroll down and expand the authentication providers section**
    - *Only the particular Cognito User Pool with its respective client ID is granted access to be able to assume an identity in this Cognito Federated identity pool*
    - *Since this app implements Cognito's fine-grained access control, beyond the default unauthenticated and authenticated roles associated for the identity pool, it is set to allow selection of the effective IAM role from a "token"*
    - *The token in this case is the "Cognito:preferred_role" attribute as shown in the decoded identity token returned from the user pool after sign-in.**

---

### N. Exploring the effective IAM policies

1.	**Browse to "CloudFormation" and select "spacefinder-api-..." stack**
1.	**Under "Outputs", scan for the "CognitoIdentityPoolAuthStandardRole"**
1.	**Copy the exact IAM role name for the standard role next to the output name**
1.	**Browse to the "IAM" console**
1.	**Click "Roles"**
1.	**Paste the copied value into the filter box**
    - *If the next screen showing the role details doesn’t automatically load, highlight the desired IAM role by clicking on it.*
    
1.	**Scroll down and click “Edit Policy” for the in-line policy on the role**
1.	**You should now see the effective IAM permissions for this policy**
    - *IAM policy variables specific to Cognito are leveraged to ensure a user can only create or delete resource bookings for him or herself, and not using other user’s IDs in the URI path.*
    - *Additionally, uploading of profile pictures to S3 is allowed, but only to the user’s particular path of their unique user ID within the S3 bucket.*
    - *If you were to look up the effective in-line IAM policy for the administrators role, you would see that these restrictions do not exist for administrators.*
	<details><summary>CognitoIdentityPoolAuthStandardRole policy</summary><p>

	```json
	{
	    "Version": "2012-10-17",
	    "Statement": [
		{
		    "Action": "execute-api:Invoke",
		    "Resource": [
			"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/*/*/locations",
			"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/*/*/locations/*",
			"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/*/*/users/${cognito-identity.amazonaws.com:sub}",
			"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/*/*/users/${cognito-identity.amazonaws.com:sub}/*"
		    ],
		    "Effect": "Allow"
		},
		{
		    "Action": "execute-api:Invoke",
		    "Resource": [
			"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/*/DELETE/locations",
			"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/*/DELETE/locations/*",
			"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/*/POST/locations",
			"arn:aws:execute-api:us-east-1:989972760655:qyt4c7jmsd/*/POST/locations/*"
		    ],
		    "Effect": "Deny"
		},
		{
		    "Action": [
			"s3:PutObject"
		    ],
		    "Resource": "arn:aws:s3:::spacefinder-api-development-stack-userdatabucket-1517eusg4r1la/${cognito-identity.amazonaws.com:sub}/*",
		    "Effect": "Allow"
		}
	    ]
	}
	```
	</p></details>
	<details><summary>Outputs in Cloud Formation</summary><p>

	```sh
	Outputs in Cloud Formation:
	Key	Value	Description	Export Name
	CognitoIdentityPoolUnAuthRoleArn	arn:aws:iam::989972760655:role/spacefinder-api-developme-CognitoIdentityPoolUnAut-P1N914OVULXD	ARN of the Cognito Identity Pool unauthenticated user role	
	CognitoIdentityPoolAuthStandardRoleArn	arn:aws:iam::989972760655:role/spacefinder-api-developme-CognitoIdentityPoolAuthS-DODIL6BLNZUT	ARN of the Cognito Identity Pool authenticated user role	
	ApiGatewayRestApi	qyt4c7jmsd	Name of the ApiGatewayRestApi	
	AwsRegion	us-east-1	Region of the AWS deployment	
	LambdaExecutionRoleArn	arn:aws:iam::989972760655:role/spacefinder-api-development-st-LambdaExecutionRole-EKSHQDFB9U9	ARN of the Lambda execution role	
	CognitoIdentityPoolAuthAdminRole	spacefinder-api-developme-CognitoIdentityPoolAuthA-90VJT18QBP3L	Name of the Cognito Identity Pool authenticated user role	
	AwsAccountId	989972760655	Account ID of the AWS account	
	CognitoIdentityPoolUnAuthRole	spacefinder-api-developme-CognitoIdentityPoolUnAut-P1N914OVULXD	Name of the Cognito Identity Pool unauthenticated user role	
	CognitoIdentityPoolAuthStandardRole	spacefinder-api-developme-CognitoIdentityPoolAuthS-DODIL6BLNZUT	Name of the Cognito Identity Pool authenticated user role	
	LambdaBucket	spacefinder-api-development-stack-lambdabucket-17omho37awvzi	Name of the private S3 bucket used to store zipped Lambda function code	
	CognitoIdentityPoolAuthAdminRoleArn	arn:aws:iam::989972760655:role/spacefinder-api-developme-CognitoIdentityPoolAuthA-90VJT18QBP3L	ARN of the Cognito Identity Pool authenticated user role	
	UserDataBucket	spacefinder-api-development-stack-userdatabucket-1517eusg4r1la	Name of the S3 bucket used to store application-specific user data	
	LambdaExecutionRole	spacefinder-api-development-st-LambdaExecutionRole-EKSHQDFB9U9	Name of the Lambda execution role
	```
	</p></details>
---

### O. Exploring the client-side code to interact with Cognito

1. **Client-side interactions with Cognito make use of the Cognito JavaScript SDKs. Explore how the app interacts with Cognito, by taking a look at this class:
<https://github.com/awslabs/aws-serverless-auth-reference-app/blob/master/app/src/services/account-management.service.ts>**
    - *Which calls are using Cognito User Pools vs Cognito Federated Identities?*
    - *What types of API calls are needed to get AWS credentials?*
    - *When we sign out a user, how do we ensure that Cognito fully signs-out the user?*
	<details><summary>getAwsCredentials</summary><p>

	```js
	public static getAwsCredentials(): Promise<void> {
	    // TODO: Integrate this method as needed into the overall module
	    let logins = {};

	    let promise: Promise<void> = new Promise<void>((resolve, reject) => {
	      // Check if user session exists
	      CognitoUtil.getCognitoUser().getSession((err: Error, result: any) => {
		if (err) {
		  reject(err);
		  return;
		}


		logins['cognito-idp.' + CognitoUtil.getRegion() + '.amazonaws.com/' + CognitoUtil.getUserPoolId()] = result.getIdToken().getJwtToken();

		// Add the User's Id token to the Cognito credentials login map
		AWS.config.credentials = new AWS.CognitoIdentityCredentials({
		  IdentityPoolId: CognitoUtil.getIdentityPoolId(),
		  Logins: logins
		});

		// Call refresh method to authenticate user and get new temp AWS credentials
		if (AWS.config.credentials.needsRefresh()) {
		  AWS.config.credentials.clearCachedId();
		  AWS.config.credentials.get((err) => {
		    if (err) {
		      reject(err);
		      return;
		    }
		    resolve();
		  });
		} else {
		  AWS.config.credentials.get((err) => {
		    if (err) {
		      console.error(err);
		      reject(err);
		      return;
		    }
		    resolve();
		  });
		}
	      });
	    });
	    return promise;
	  }
	}
	```
	</p></details>
---

# CLEANUP (1 minute)

Cleanup is fast and easy. This lab is self-contained and cleans up after itself by un-deploying all auto-generated AWS resources).

1. **Exit Ionic 2 console and stop the Ionic server.**
	- `Control-C` or `q` (for quit) will exit the Ionic 2 console.
  
> Troubleshooting: If your SSH session was terminated by the time you get to this point, don't worry about this Ionic 2 server step. However, you will need to repeat steps #3 through step #5 in the SETUP instructions, before you continue with the following steps. You will need to SSH back into your EC2 instance, start up the Docker container, and configure your AWS credentials, before continuing with the rest of the CLEANUP steps.

1. **Un-deploy all AWS resources**:

		cd /home/aws-serverless-auth-reference-app/api
	 	gulp undeploy

1. **Exit the Docker container**

	- Type `exit` at the command line.

1. **Terminate the EC2 instance.**
