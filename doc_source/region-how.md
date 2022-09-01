# How AWS Regions Work With AWS Control Tower<a name="region-how"></a>

Currently, AWS Control Tower is supported in the following AWS Regions:
+ US East \(N\. Virginia\)
+ US East \(Ohio\)
+ US West \(Oregon\)
+ Canada \(Central\) Region
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Singapore\) Region
+ Europe \(Frankfurt\) Region
+ Europe \(Ireland\)
+ Europe \(London\) Region
+ Europe \(Stockholm\) Region
+ Asia Pacific \(Mumbai\) Region 
+ Asia Pacific \(Seoul\) Region 
+ Asia Pacific \(Tokyo\) Region 
+ Europe \(Paris\) Region 
+ South America \(São Paulo\) Region 

**About your home Region**

When you create a landing zone, the Region that you're using for access to the AWS Management console becomes your home AWS Region for AWS Control Tower\. During the creation process, some resources are provisioned in the home Region\. Other resources, such as OUs and AWS accounts, are global\.

 After you've selected a home Region, you cannot change it\.

** Guardrails and Regions**

Currently, all preventive guardrails work globally\. Detective guardrails, however, only work in Regions where AWS Control Tower is supported\. For more information about the behavior of guardrails when you activate AWS Control Tower in a new Region, see [Configure your AWS Control Tower Regions](#deploying-to-new-region)\.

## Configure your AWS Control Tower Regions<a name="deploying-to-new-region"></a>

This section describes the behavior you can expect when you extend your AWS Control Tower landing zone into a new AWS Region\. or remove a Region from your landing zone configuration\. Generally, this action is performed through the **Update** function of the AWS Control Tower console\.

**Note**  
We recommend that you avoid expanding your AWS Control Tower landing zone into AWS Regions in which you do not require your workloads to run\. Opting out of a Region does not prevent you from deploying resources in that Region, but those resources will remain outside of AWS Control Tower governance\.

During configuration of a new Region, AWS Control Tower updates the landing zone, which means that it *baselines* your landing zone  —
+ to operate actively in all newly\-selected Regions, and
+ to cease governing resources in deselected Regions\.

Individual accounts within your organizational units \(OUs\) that are managed by AWS Control Tower are not updated as part of this landing zone update process\. Therefore, you must update your accounts by re\-registering your OUs\. 

When configuring your AWS Control Tower Regions, be aware of the following recommendations and limitations:
+ Select Regions in which you plan to host AWS resources or workloads\.
+ Opting out of a Region does not prevent you from deploying resources in that Region, but those resources will remain outside of AWS Control Tower governance\.

When you configure your landing zone for new Regions, AWS Control Tower detective guardrails adhere to the following rules:
+ *What exists stays the same\.* Guardrail behavior, detective as well as preventive, is unchanged for existing accounts, in existing OUs, in existing Regions\.
+ *You can’t apply new detective guardrails to existing OUs containing accounts that are not updated\.* When you’ve configured your AWS Control Tower landing zone into a new Region \(by updating your landing zone\), you must update existing accounts in your existing OUs before you can enable new detective guardrails on those OUs and accounts\.
+ *Your existing detective guardrails begin working in the newly configured Regions as soon as you update the accounts\.* When you update your AWS Control Tower landing zone to configure new Regions and then update an account, the detective guardrails that already are enabled on the OU will begin working on that account in the newly configured Regions\. 

**Configure AWS Control Tower Regions**

1. Sign in to the AWS Control Tower console at [https://console.aws.amazon.com/controltower](https://console.aws.amazon.com/controltower)

1. In the left\-pane navigation menu, choose **Landing zone settings**\.

1. On the **Landing zone settings** page,in the **Details** section, choose the **Modify settings** button in the upper right\. You are directed to the update landing zone workflow, because governing new Regions, or removing Regions from governance, requires you to update to the latest landing zone version\. 

1. Under **Additional AWS Regions for governance**, search for the Regions you want to govern \(or stop governing\)\. The **State** column indicates which Regions you currently govern, and which ones you don't\.

1. Select the checkbox for each additional Region to govern\. Deselect the checkbox for each Region from which you are removing governance\. 
**Note**  
If you opt not to govern a Region, you can still deploy resources in that Region, but those resources will remain outside of AWS Control Tower governance\.

1. Complete the rest of the workflow, then choose **Update landing zone**\.

1. When the landing zone setup completes, **Re\-register** the OUs to update the accounts in your new Regions\. For more information, see [Update existing OUs and accounts](update-existing-accounts.md)\.

An alternative method of provisioning or updating individual accounts after configuring new Regions is by using [the API framework of AWS Service Catalog](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_Reference.html) and [the AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/servicecatalog/index.html) to update the accounts in a batch process\. For more information, see [Provision and update accounts using automation](update-accounts-by-script.md)\.