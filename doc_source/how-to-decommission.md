# How to decommission a landing zone<a name="how-to-decommission"></a>

To decommission your AWS Control Tower landing zone, follow the procedure given here\.

**Note**  
We recommend that you unmanage your enrolled accounts prior to decommissioning\.

1. Navigate to the **Landing Zone Settings** page in the AWS Control Tower console\.

1. Choose **Decommission your landing zone** within the **Decommission your landing zone** section\.

1.  A dialog appears, explaining the action you are about to perform, with a required confirmation process\. To confirm your intent to decommission, you must select every box and type the confirmation as requested\.
**Important**  
*The decommissioning process cannot be undone\.*

1. If you confirm your intent to decommission your landing zone, you are redirected to the AWS Control Tower home page while decommissioning is in progress\. The process may require up to two hours\.

1. When decommissioning has succeeded, you must delete remaining resources manually before setting up a new landing zone from the AWS Control Tower console\. These remaining resources include some specific S3 buckets, organizations, and CloudWatch Logs log groups\.
**Note**  
*These actions may have significant consequences for your billing and compliance activities\. For example, failure to delete these resources can result in unexpected charges\.*

    For more information about how to delete resources manually, see [Manual Cleanup of AWS Control Tower Resources](walkthrough-delete.md#manual-decommissioning)\.

1. If you intend to set up a new landing zone in a new AWS Region, follow this additional step\. Enter the following command through the CLI: 

   ```
   aws organizations disable-aws-service-access --service-principal controltower.amazonaws.com
   ```