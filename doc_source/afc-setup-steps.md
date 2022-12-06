# Set up for customization<a name="afc-setup-steps"></a>

The next sections give steps to set up account factory for the customization process\. We recommend that you set up [delegated admin](https://docs.aws.amazon.com/accounts/latest/reference/using-orgs-delegated-admin.html) for the hub account, before you begin these steps\.

**Summary**
+ **Step 1\. Create the required role\.** Create an IAM role that grants permission for AWS Control Tower to have access to the \(hub\) account, where the AWS Service Catalog products, also called blueprints, are stored\.
+ **Step 2\. Create the AWS Service Catalog product\.** Create the AWS Service Catalog product \(also called a “blueprint product”\) that you'll need for baselining the custom account\.
+ **Step 3\. Review your custom blueprint\.** Inspect the AWS Service Catalog product \(blueprint\) that you created\.
+ **Step 4\. Call your blueprint to create a customized account\.** Enter the blueprint product information and the role information into the proper fields in Account Factory, in the AWS Control Tower console, while creating the account\.

## Step 1\. Create the required role<a name="step-1-create-blueprint-access-role"></a>

Navigate to the IAM console to set up the required role\.

Before you begin to customize accounts, you must set up a role that contains a trust relationship between AWS Control Tower and your hub account\. When assumed, the role grants AWS Control Tower access to administer the hub account\. It must be named **AWSControlTowerBlueprintAccess**\. 

AWS Control Tower assumes this role to create a Portfolio resource on your behalf in AWS Service Catalog, then to add your blueprint as a Service Catalog Product to this Portfolio, and then to share this Portfolio, and your blueprint, with your member account during account provisioning\.

 The **AWSControlTowerBlueprintAccess** role must be set up to grant trust to two principals:
+ The principal \(IAM user\) that runs AWS Control Tower in the AWS Control Tower management account\. 
+ The role named **AWSControlTowerAdmin** in the AWS Control Tower management account\.

Here's an example trust policy, similar to one you will need to include for your role\. This policy demonstrates the best practice of granting least\-privilege access\. When you make your own policy, replace the term `YourManagementAccountId` with the actual acccount ID of your AWS Control Tower management account, and replace the term `YourControlTowerUserRole` with the identifier of the IAM role for your management account\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                "arn:aws:iam::YourManagementAccountId:role/service-role/AWSControlTowerAdmin", 
                "arn:aws:iam::YourManagementAccountId:role/YourControlTowerUserRole"
                ]
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

**Required permissions policy**

AWS Control Tower requires that the managed policy named `AWSServiceCatalogAdminFullAccess` must be attached to the **AWSControlTowerBlueprintAccess** role\. This policy provides permissions that AWS Service Catalog looks for when it allows AWS Control Tower to administer your Portfolio and AWS Service Catalog Product resources\. You can attach this policy when you're creating the role in the IAM console\.

AWS Control Tower also requires the `AmazonS3ReadOnlyAccess` permission policy for the role\.

## Step 2\. Create the AWS Service Catalog product<a name="step-2-create-blueprint-product"></a>

To create an AWS Service Catalog product, follow the steps at [Creating products](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/productmgmt-cloudresource.html) in the AWS Service Catalog Administrator Guide\. You'll add your account blueprint as a template when you create the AWS Service Catalog product\.

**Summary of steps to create a blueprint**
+ Create or download an AWS CloudFormation template that will become your account blueprint\. Some template examples are given later in this section\.
+ Sign in to the AWS account where you store your Account Factory blueprints \(sometimes called the hub account\)\.
+ Navigate to the AWS Service Catalog console\. Choose **Product list**, and then choose **Upload new product**\.
+ In the **Product details** pane, enter details for your blueprint product, such as a name and description\.
+ Select **Use a template file** and then select **Choose file**\. Select or paste the AWS CloudFormation template you've developed or downloaded for use as your blueprint\.
+ Choose **Create product** at the bottom of the console page\.

You can download an AWS CloudFormation template from the AWS Service Catalog reference architecture repository\. [One example from that repository helps set up a backup plan for your resources](https://raw.githubusercontent.com/aws-samples/aws-service-catalog-reference-architectures/master/backup/backup-tagoptions.yml)\.

Here's an example template, for a fictitious company called **Best Pets**\. It helps set up a connection to their pet database\.

```
Resources:
  ConnectionStringGeneratorLambdaRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service:
                - lambda.amazonaws.com
            Action:
              - "sts:AssumeRole"
  ConnectionStringGeneratorLambda:
    Type: AWS::Lambda::Function
    Properties:
      FunctionName: !Join ['-', ['ConnectionStringGenerator', !Select [4, !Split ['-', !Select [2, !Split ['/', !Ref AWS::StackId]]]]]]
      Description: Retrieves the connection string for this account to access the Pet Database
      Role: !GetAtt ConnectionStringGeneratorLambdaRole.Arn
      Runtime: nodejs16.x
      Handler: index.handler
      Timeout: 5
      Code:
        ZipFile: >
          const response = require("cfn-response");
          exports.handler = function (event, context) {
            const awsAccountId = context.invokedFunctionArn.split(":")[4]
            const connectionString= "fake connection string that's specific to account " + awsAccountId;
            const responseData = {
              Value: connectionString,
            }
            response.send(event, context, response.SUCCESS, responseData);
            return connectionString;
          };

  ConnectionString:
    Type: Custom::ConnectionStringGenerator
    Properties:
      ServiceToken: !GetAtt ConnectionStringGeneratorLambda.Arn

  PetDatabaseConnectionString:
    DependsOn: ConnectionString
    # For example purposes we're using SSM parameter store.
    # In your template, use secure alternatives to store
    # sensitive values such as connection strings.
    Type: AWS::SSM::Parameter
    Properties: 
      Name: pet-database-connection-string
      Description: Connection information for the BestPets pet database
      Type: String
      Value: !GetAtt ConnectionString.Value
```

## Step 3\. Review your custom blueprint<a name="step-3-review-blueprint"></a>

You can view your blueprint in the AWS Service Catalog console\. For more information, see [Managing products](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/catalogs_products.html#productmgmt-menu) in the AWS Service Catalog Adminstrator Guide\.

## Step 4\. Call your blueprint to create a customized account<a name="step-4-call-the-blueprint"></a>

When you follow the **Create account** workflow in the AWS Control Tower console, you'll see an optional section where you can enter information about the blueprint you'd like to use for customizing accounts\.

**Note**  
You must set up your customization hub account and add at least one blueprint \(AWS Service Catalog product\) before you can enter that information into the AWS Control Tower console and begin to provision customized accounts\.

**Create or update a customized account in the AWS Control Tower console\.**

1. Enter the account ID for the account that contains your blueprints\.

1. From that account, select an existing AWS Service Catalog product; that is, select an existing blueprint\.

1. Select the proper version of the blueprint \(AWS Service Catalog product\), if you have more than one version\.

1. You can add or change a blueprint provisioning policy at this point in the process\. The blueprint provisioning policy, written in JSON, is attached to an IAM role to provision the resources specified in the blueprint template\. By default, the **AdministratorAccess** policy is applied here\.

1. Choose the AWS Region or Regions in which you wish to deploy accounts based on this blueprint\.

1. If your blueprint contains parameters, you can enter the values for the parameters into additional fields in the AWS Control Tower workflow\. The additional values may include: a GitHub repository name, a GitHub branch, an Amazon ECS cluster name, and a GitHub identity for the repository owner\.

1. You can customize accounts at a later time by following the **Account update** process, if your hub account or blueprints are not yet ready\.

For more details, see [Create a customized account from a blueprint](create-afc-customized-account.md)\.