# Walkthroughs<a name="walkthroughs"></a>

This chapter contains walkthrough procedures that can help you in your use of AWS Control Tower\.

**Topics**
+ [Walkthrough: Move from ALZ to AWS Control Tower](alz-to-control-tower.md)
+ [Walkthrough: Automate Account Provisioning in AWS Control Tower by Service Catalog APIs](automated-provisioning-walkthrough.md)
+ [Walkthrough: Configure AWS Control Tower Without a VPC](configure-without-vpc.md)
+ [Manage AWS Control Tower Resources](walkthrough-delete.md)
+ [Walkthrough: Set Up Security Groups in AWS Control Tower With AWS Firewall Manager](firewall-setup-walkthrough.md)
+ [Walkthrough: Decommission an AWS Control Tower Landing Zone](decommission-landing-zone.md)

## \[ControlTower\.ELB\.2\] Classic Load Balancers with HTTPS/SSL listeners should use a certificate provided by AWS Certificate Manager<a name="ct-elb-2"></a>
+ **Objectives:** Enforce Data Encryption in Transit
+ **Severity:** Medium
+ **Control Type:** CloudFormation Guard Rule
+ **Implementation:** AWS CloudFormation Hook
+ **Control Behavior:** Preventive
+ ** Resource type:** `AWS::ElasticLoadBalancing::LoadBalancer`
+ **AWS Config rule:** [https://docs.aws.amazon.com/https://github.com/aws-controltower/mesarii/controls/elb/rules/elb_acm_certificate_required_checks/elb_acm_certificate_required_checks.guard](https://docs.aws.amazon.com/https://github.com/aws-controltower/mesarii/controls/elb/rules/elb_acm_certificate_required_checks/elb_acm_certificate_required_checks.guard)

This control checks whether the Classic Load Balancer uses HTTPS/SSL certificates provided by AWS Certificate Manager \(ACM\)\. The control fails if the Classic Load Balancer configured with HTTPS/SSL listener does not use a certificate provided by ACM\.

To create a certificate, you can use either ACM or a tool that supports the SSL and TLS protocols, such as OpenSSL\. Security Hub recommends that you use ACM to create or import certificates for your load balancer\.

ACM integrates with Classic Load Balancers so that you can deploy the certificate on your load balancer\. You also should automatically renew these certificates\.

### Control Behaviors<a name="elb-2-behaviors"></a>

This control implements the Failure and Pass behaviors that follow\.

**Control Failure Behaviors**
+ `SSLCertificateId` on LoadBalancer `HTTPS` or `SSL` `Listeners` is missing or is not a valid ACM certificate ARN\.

**Control Pass Behaviors**
+ `SSLCertificateId` on LoadBalancer `HTTPS` or `SSL` `Listeners` matches an ACM certificate ARN\.

### Remediation<a name="elb-2-remediation"></a>

Provide an ACM Certificate ARN for the `SSLCertificateId` property on all `HTTPS` and `SSL` `Listeners`

```
Elb:
    Type: AWS::ElasticLoadBalancing::LoadBalancer
    Properties:
        Scheme: internal
        Subnets:
        - Ref: Subnet
        Listeners:
        - Protocol: HTTPS
        SSLCertificateId: arn:aws:acm:us-west-2:123456789012:certificate/12345678-12ab-34cd-56ef-12345678
        InstancePort: 80
        LoadBalancerPort: 443
```