#### CLOUDFRONT WITH WAF PROTECTION 2023

#### Lab Level: 200 Intermidediate 

#### Description
According to Cloud Threat Report, Unit 42™ which is based on analysis of 210,000 cloud accounts across 1,300 different organizations in 76% of organizations MFA is not enforced for console users, and 58% of organizations don't enforce MFA for root/admin users.

This lab is based on [Ben Potter, Security Lead, Well-Architected](https://www.wellarchitectedlabs.com/security/200_labs/200_cloudfront_with_waf_protection/), which is roughly was created in 2018.

Main purpose of my project is to demonstrate what I've learned and how I had to implement changes due to AWS updates for its services.
 
Things I've done differently:

* instance creation/security groups/roles
* Apache Web Server installation for Linux 2 instance
* Cloudfront roll out
* 

I hope you will enjoy following the updated version. I had a lot of fun :).
#### Goals
Protecting network and host-level boundaries
System security configuration and maintanance
Enforcing service-level protection

#### Services & concepts used:

EC2
Amazon CloudFront
AWS Web Application Firewall (WAF)
Defence in Depth

#### Steps:

* Launch Instance EC2
* Configure AWS WAF
* Cofigure Amazon CloudFront
* Tear down this lab

#### Launch instance 

1. EC2 service -> Launch Instance (top right):
* Name: cloudfront_with_waf
* AMI: Amazon Linux 2023 AMI
* Instance Type: t2.micro
  
  ![ec2_launch](https://user-images.githubusercontent.com/78635937/271756002-2afbc686-231b-4a14-8590-edcfedb6a336.png)


* Key Pair (login): create new key_pair

  ![image](https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/d1981ea2-6fb5-4734-a89f-d1797dc33610)

* Network settings (expand)
* Create security group: cloudfront_with_waf_sec_group (can be done after I prefer to be done with it here)
* Description: sec group for cloudfront with waf
* Inbound Security Group Rules:
 * Type: SSH Source, Type: My IP
 * Type: HTTPS Source, Type: Anywhere
 * Type: HTTPS Source, Type: Anywhere

**Note that best practice is to have an Elastic Load Balancer inline or the EC2 instance not directly exposed to the internet. However, for simplicity in this lab, we are opening the access to anywhere. Other lab modules secure access with Elastic Load Balancer.**

 ![image](https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/09ef6661-d9c0-4fd2-9487-8f4a9931e480)

 ![image](https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/8d23253e-ad1b-4b6d-8093-0abaa6c3144c)

 ![image](https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/dcb2ea9c-5d08-4dcc-a835-92c212d84aed)

 ![image](https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/c07900c2-7d10-47ca-ac34-6367152dec72)


* Create New Role:

 ![image](https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/cd15bcb5-d1e3-413b-870f-696607faf8af)

 ![image](https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/a620d482-ea78-4866-b342-60ad98cdcb40)

 ![image](https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/a53ff73a-93d6-4dd0-901a-718c19d9c324)



* After you create a Role it's time to add it to our instance and click Update:

 ![image](https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/fa1bb0d0-43a6-41da-9092-c12780321771)

 ![image](https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/5a05ba4e-9534-416b-acb2-5431d789f908)


* Connect to instance via SSH use the pem key created earlier (same directory):
 
 ![image](https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/6124d9a4-bab8-42cb-9494-823e543e5612)

![image](https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/21fc4d86-4979-4a8f-8a82-b13b91ee2142)


* run the following to install Apache Web Server:

> sudo yum update -y
>
> sudo yum install -y httpd
>
> sudo systemctl start httpd
>
> sudo systemctl enable httpd
>
> sudo chmod 777 /var/www/html
>
> usermod -a -G www ec2-user
>
> sudo vi /var/www/html/index.html
>
![image](https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/fda12bc1-f58d-4eac-ab49-e568f02b8190)

* Copy Public IPv4 DNS link and paste it in your browser, you should see the following:

<img width="497" alt="image" src="https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/18219e48-c7ba-452f-9933-da0add4547f6">

#### AWS WAF Configuration with CloudFormation

* Open CloudFormation console
* Create Stack
* Use the following template AWS S3 URL: https://s3-us-west-2.amazonaws.com/aws-well-architected-labs/Security/Code/waf-global.yaml

<img width="2347" alt="image" src="https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/e48b6231-0de8-4727-98b9-4eb0243f4ed0">

* Specify stack details:
  * Stack Name: waf
  * WAFName: Lab1
  * WAFCloudWatchPrefix: Lab1
  * Rest leave as DEFAULT
  * Review and Complete

* For a few good minutes (aprox 10) the stack will be in status CREATE_IN_PROGRESS till it's done then you'll see CREATE_COMPLETE:

<img width="2349" alt="image" src="https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/baf490a5-ae3c-4d3a-aca2-66762721d8a5">

The above steps set up basic AWS WAF configuration which we gonna use next in CloudFront.

<img width="2352" alt="image" src="https://github.com/0xsisu/CLOUDFRONT-WITH-WAF-PROTECTION/assets/78635937/bdddedf7-5a89-4d0a-8725-f25f178fbdc6">


#### Cloudfront

Amazon CloudFront is a content delivery network (CDN) service. [Here](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html) you can learn more about it.















 
