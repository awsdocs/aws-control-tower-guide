# How to decommission a landing zone<a name="how-to-decommission"></a>

After you've contacted [AWS Support](https://aws.amazon.com/premiumsupport/) and received confirmation that they've enabled your account for decommissioning, you'll be able to see a section on your **Settings** page that allows you to follow the procedure given here\. This procedure will not work until AWS Support has enabled your account for decommissioning\.

1. Navigate to the **Settings** page in the AWS Control Tower console\.

1. Choose **Decommission your landing zone** within the **Decommission your landing zone** section\.

1.  A dialog appears, explaining the action you are about to perform, with a required confirmation process\. To confirm your intent to decommission, you must select every box and type the confirmation as requested\.
**Important**  
*The decommissioning process cannot be undone\.*

1. If you confirm your intent to decommission your landing zone, you are redirected to the AWS Control Tower home page while decommissioning is in progress\. The process may require up to two hours\.

1. When decommissioning has succeeded, you must delete remaining resources manually before setting up a new landing zone from the AWS Control Tower console\. These remaining resources include some specific S3 buckets, organizations, and CloudWatch Logs log groups\.
**Note**  
*These actions may have significant consequences for your billing and compliance activities\. For example, failure to delete these resources can result in unexpected charges\.*

    For more information about how to delete resources manually, see [Manual Cleanup of AWS Control Tower Resources](walkthrough-delete.md#manual-decommissioning)\.