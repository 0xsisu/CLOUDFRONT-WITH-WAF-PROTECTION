#### CLOUDFRONT WITH WAF PROTECTION
[Author credit: Ben Potter, Security Lead, Well-Architected](https://www.wellarchitectedlabs.com/security/200_labs/200_cloudfront_with_waf_protection/)

#### Description
According to Cloud Threat Report, Unit 42™ which is based on analysis of 210,000 cloud accounts across 1,300 different organizations in 76% of organizations MFA is not enforced for console users, and 58% of organizations don't enforce MFA for root/admin users.
In this lab I wil be demonstrating what I;ve learned from Ben Porter's 200 Level Lab tutorial

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

1. EC2 service -> Launch instance (top right)
2. Choose micro
