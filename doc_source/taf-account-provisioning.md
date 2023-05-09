# Provision accounts with AWS Control Tower Account Factory for Terraform \(AFT\)<a name="taf-account-provisioning"></a>

 AWS Control Tower Account Factory for Terraform \(AFT\) adopts a GitOps model that automates the process of account provisioning and updating in AWS Control Tower\. 

**Note**  
 AFT doesn't impact workflow performance in AWS Control Tower\. If you provision an account through AFT or Account Factory, the same backend workflow occurs\.  

 With AFT, you create an account request Terraform file, which contains the input that invokes the AFT workflow\. After account provisioning and updating finishes, the AFT workflow continues by running the AFT account provisioning framework and account customizations steps\. 

## Prerequisites<a name="aft-prerequisites"></a>

 Before getting started with AFT, you must create the following: 
+  A fully deployed AFT environment\. For more information, see [Overview of AWS Control Tower Account Factory for Terraform \(AFT\)](https://docs.aws.amazon.com/controltower/latest/userguide/aft-overview.html) and [Deploy AWS Control Tower Account Factory for Terraform \(AFT\)](https://docs.aws.amazon.com/controltower/latest/userguide/aft-getting-started.html) 
+  One or more AFT `git` repositories in your fully deployed AFT environment\. For more information, see [Post\-deplyment steps for AFT](https://docs.aws.amazon.com/controltower/latest/userguide/aft-post-deployment.html)\. 

**Tip**  
 Optionally, you can create an account template folder in the **aft\-account\-customizations** repository\. 