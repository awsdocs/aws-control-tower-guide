# Provision accounts with AWS Control Tower Account Factory for Terraform<a name="taf-account-provisioning"></a>

AWS Control Tower Account Factory for Terraform \(AFT\) follows a GitOps model to automate the processes of account provisioning and account updating in AWS Control Tower\. You'll create an *account request* Terraform file, which provides the necessary input that triggers the AFT workflow for account provisioning\.

When account provisioning or updating is complete, the AFT workflow continues\. It runs the AFT account provisioning framework and account customizations steps\.

**Note**  
AFT does not affect workflow performance in AWS Control Tower\. For example, if you provision an account through Account Factory, or through AFT, the same back\-end workflow occurs, without any timing differences\. AFT enables automation, therefore the time required for provisioning may be less noticeable\.

## Prerequisites<a name="aft-prerequisites"></a>

Before AFT can begin to provision accounts, some prerequisite steps are required\. You must create:
+ **A fully deployed AFT environment\.** For more information about AFT deployment, see one or more of these sections:
  + [Deploy AWS Control Tower Account Factory for Terraform \(AFT\)](aft-getting-started.md)
  + [Overview of AWS Control Tower Account Factory for Terraform](aft-overview.md)
  + [Configure and launch your AWS Control Tower Account Factory for Terraform](aft-getting-started.md#aft-configure-and-launch)
+ **One or more AFT `git` repositories in your environment\.** For more information, see [Post\-deployment steps](aft-post-deployment.md) in [Deploy AWS Control Tower Account Factory for Terraform \(AFT\)](aft-getting-started.md)\.
+ **An account template folder in your chosen `git` repository for account customizations\.** This folder is optional\.