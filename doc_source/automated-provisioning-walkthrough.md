# Walkthrough: Automated Account Provisioning in AWS Control Tower<a name="automated-provisioning-walkthrough"></a>

AWS Control Tower is integrated with several other AWS services, such as AWS Service Catalog\. You can use the APIs to create and provision your member accounts in AWS Control Tower\.

The video shows you how to provision accounts in an automated, batch fashion, by calling the AWS Service Catalog APIs\. For provisioning, you'll call the [https://docs.aws.amazon.com/servicecatalog/latest/dg/API_ProvisionProduct.html](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_ProvisionProduct.html) API from the AWS command line interface \(CLI\), and you'll specify a JSON file that contains the parameters for each account you'd like to set up\. The video illustrates installing and using the [AWS Cloud9](https://docs.aws.amazon.com/cloud9/latest/user-guide/welcome.html) development environment to perform this work\.

**Note**  
You also can use this approach for automating account updates, by calling the [https://docs.aws.amazon.com/servicecatalog/latest/dg/API_UpdateProvisionedProduct.html](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_UpdateProvisionedProduct.html) API of AWS Service Catalog for each account\. You can write a script to update the accounts, one by one\.

## Video Walkthrough<a name="automated-provisioning-video"></a>

This video \(7:08\) describes how to automate account deployments in AWS Control Tower\. For better viewing, select the icon at the lower right corner of the video to enlarge it to full screen\. Captioning is available\.

LInk to video: http://youtu.be/t0gxOsByOlA
