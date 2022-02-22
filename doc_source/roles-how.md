# How AWS Control Tower works with roles to create and manage accounts<a name="roles-how"></a>

In general, roles are a part of identity and access management \(IAM\) in AWS\. Refer to [Permissions Required to Use the AWS Control Tower Console](access-control-managing-permissions.md#additional-console-required-permissions) for information about the roles required by AWS Control Tower\. For general information about IAM and roles in AWS, see [ the IAM roles topic in the *AWS IAM User Guide*](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)\.

## Roles and account creation<a name="roles-and-accont-creation"></a>

AWS Control Tower creates a customer's account by calling the `CreateAccount` API of AWS Organizations\. When AWS Organizations creates this account, it creates a role within that account, which AWS Control Tower names by passing in a parameter to the API\. The name of the role is `AWSControlTowerExecution`\.

AWS Control Tower takes over the `AWSControlTowerExecution` role for all accounts created by Account Factory\. Using this role, AWS Control Tower *baselines* the account and applies mandatory \(and any other enabled\) guardrails, which results in creation of other roles\. These roles in turn are used by other services, such as AWS Config\.

**Note**  
To *baseline* an account is to set up its resources, which include [Account Factory templates](https://docs.aws.amazon.com/controltower/latest/userguide/account-factory-considerations.html), sometimes referred to as *blueprints*, and guardrails\. The baselining process also sets up the centralized logging and security audit roles on the account, as part of deploying the templates\. AWS Control Tower baselines are contained in the roles that you apply to every enrolled account\.

For more information about accounts and resources, see [About AWS accounts in AWS Control Tower](accounts.md)\.

### <a name="awscontroltowerexecution"></a>

**The AWSControlTowerExecution role, explained**

The `AWSControlTowerExecution` role must be present in all enrolled accounts\. It allows AWS Control Tower to manage your individual accounts and report information about them to your Audit and Log Archive accounts\.

The `AWSControlTowerExecution` role can be added into an account in several ways, as follows:
+ For accounts in the Security OU \(sometimes called *core accounts*\), AWS Control Tower creates the role at the time of initial AWS Control Tower setup\.
+ For an Account Factory account created through the AWS Control Tower console, AWS Control Tower creates this role at the time of account creation\.
+ For a single account enrollment, we ask customers to manually create the role and then enroll the account in AWS Control Tower\.
+ When extending governance to an OU, AWS Control Tower uses the **StackSet\-AWSControlTowerExecutionRole** to create the role in all accounts in that OU\.

Purpose of the `AWSControlTowerExecution` role:
+ `AWSControlTowerExecution` allows you to create and enroll accounts, automatically, with scripts and Lambda functions\.
+ `AWSControlTowerExecution` helps you configure your organizations's logging, so that all the logs for every account are sent to the logging account\.
+ `AWSControlTowerExecution` allows you to enroll an individual account in AWS Control Tower\. First, you must add the `AWSControlTowerExecution` role to that account\. For steps on how to add the role, see [Manually add the required IAM role to an existing AWS account and enroll it](enroll-manually.md)\.

How the `AWSControlTowerExecution` role works with OUs:

The `AWSControlTowerExecution` role ensures that your selected AWS Control Tower guardrails apply automatically to every individual account, in each OU, in your organization, as well as to every new account you create in AWS Control Tower\. As a result:
+ You can provide compliance and security reports more easily, based on the auditing and logging features embodied by AWS Control Tower [guardrails](https://docs.aws.amazon.com/controltower/latest/userguide/guardrails.html)\.
+ Your security and compliance teams can verify that all requirements are met, and that no organizational drift has occurred\.

For more information about drift, see [Detect and resolve drift in AWS Control Tower](https://docs.aws.amazon.com/controltower/latest/userguide/drift.html)\.

To summarize, the `AWSControlTowerExecution` role and its associated policy gives you flexible control of security and compliance across your entire organization\. Therefore, breaches of security or protocol are less likely to occur\.

## Optional conditions for your role trust relationships<a name="conditions-for-role-trust"></a>

You can impose conditions in your role trust policies, to restrict the accounts and resources that interact with certain roles in AWS Control Tower\. We strongly recommend that you restrict access to the `AWSControlTowerAdmin` role, because it allows wide access permissions\.

To help prevent an attacker from gaining access to your resources, manually edit your AWS Control Tower trust policy to add at least one `aws:SourceArn` or `aws:SourceAccount` conditional to the policy statement\. As a security best practice, we strongly recommend adding the `aws:SourceArn` condition, because it is more specific than `aws:SourceAccount`, limiting access to a specific account and a specific resource\.

If you don't know the full ARN of the resource, or if you are specifying multiple resources, you can use the `aws:SourceArn` condition with wildcards \(\*\) for the unknown portions of the ARN\. For example, `arn:aws:controltower:*:123456789012:*` works if you don't wish to specify a Region\.

The following example demonstrates the use of the `aws:SourceArn` IAM condition with your IAM role trust polices\. Add the condition in your trust relationship for the **AWSControlTowerAdmin** role, because the AWS Control Tower service principal interacts with it\. 

As shown in the example, the source ARN is of the format: `arn:aws:controltower:${HOME_REGION}:${CUSTOMER_AWSACCOUNT_id}:*`

Replace the strings `${HOME_REGION}` and `${CUSTOMER_AWSACCOUNT_id}` with your own home Region and account ID of the calling account\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": [
          "controltower.amazonaws.com"
        ]
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "ArnEquals": {
          "aws:SourceArn": "arn:aws:controltower:us-west-2:012345678901:*"
        }
      }
    }
  ]
}
```

In the example, the Source ARN designated as `arn:aws:controltower:us-west-2:012345678901:*` is the only ARN allowed to perform the `sts:AssumeRole` action\. In other words, only users who can sign in to the account ID `012345678901`, in the `us-west-2` Region, are allowed to perform actions that require this specific role and trust relationship for the AWS Control Tower service, designated as `controltower.amazonaws.com`\.

The next example shows the `aws:SourceAccount` and `aws:SourceArn` conditions applied to the role trust policy\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": [
          "controltower.amazonaws.com"
        ]
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "aws:SourceAccount": "012345678901"
        },
        "StringLike": {
          "aws:SourceArn": "arn:aws:controltower:us-west-2:012345678901:*"
        }
      }
    }
  ]
}
```

The example illustrates the `aws:SourceArn` condition statement, with an added `aws:SourceAccount` condition statement\. For more information, see [Prevent cross\-service impersonation](prevent-confused-deputy.md)\.

 For general information about permission policies in AWS Control Tower see [Managing Access to Resources](access-control-overview.md#access-control-manage-access-intro)\.

**Recommendations:**

We recommend that you add conditions to the roles that AWS Control Tower creates, because those roles are directly assumed by other AWS services\. For more information, see the example for **AWSControlTowerAdmin**, shown previously in this section\. For the AWS Config recorder role, we recommend adding the `aws:SourceArn` condition, specifying the Config recorder ARN as the permitted source ARN\.

For roles such as **AWSControlTowerExecution** or the [ roles that can be assumed by the AWS Control Tower Audit account in all managed accounts](#stacksets-and-roles), we recommend that you add the `aws:SourceArn` condition to these roles with `aws:PrincipalOrgID`, which validates that the principal accessing the resource belongs to an account in the correct AWS organization\.

**Note**  
In case of drift, it is possible that an AWS Control Tower role may be reset under certain circumstances\. It is recommended that you re\-check the roles periodically, if you have customized them\.

## AWS Control Tower ConfigRecorderRole<a name="aws-controltower-configrecorderrole"></a>

AWS Control Tower deploys this role as a resource in the log archive account, the audit account, and in each account created by Account Factory\. The role can be assumed by AWS Config, as shown in the role trust relationship artifact, given later in this section\. This role is over 1000 lines long, because it allows multiple actions by many AWS services\. The role grants permission to AWS Config to record configurations and deliver them to the delivery channels\.

**Note**  
When you create this IAM role, you give AWS Control Tower permission to manage the AWS Config resources as defined in the permissions policy for this role\. The first time that AWS Control Tower uses this role, AWS Config might create a new [service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in your account\. That role grants AWS Config access to other AWS resources that are required to complete your original AWS Control Tower request\.  
To learn more about how AWS Config or other services create and use service\-linked roles, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html)\. Look for the services that have **Yes **in the **Service\-Linked Role** column to indicate that they support using service\-linked roles\. Choose a **Yes** with a link to view the service\-linked role documentation for that service\.  
For a definition of AWS service\-linked role, see [AWS service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/iam-term-service-linked-role)\.

Role name: `aws-controltower-ConfigRecorderRole`

Deployed in these accounts: Log archive, Audit, Account factory accounts

Assumed by: AWS Config

AWS managed policies:
+ [ ReadOnlyAccess](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html#awsmp_readonlyaccess)
+ [AWS\_ConfigRole](https://docs.aws.amazon.com/config/latest/developerguide/security-iam-awsmanpol.html#security-iam-awsmanpol-AWS_ConfigRole)

The following JSON artifact shows the role\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "a4b:Get*",
                "a4b:List*",
                "a4b:Search*",
                "access-analyzer:GetAccessPreview",
                "access-analyzer:GetAnalyzedResource",
                "access-analyzer:GetAnalyzer",
                "access-analyzer:GetArchiveRule",
                "access-analyzer:GetFinding",
                "access-analyzer:GetGeneratedPolicy",
                "access-analyzer:ListAccessPreviewFindings",
                "access-analyzer:ListAccessPreviews",
                "access-analyzer:ListAnalyzedResources",
                "access-analyzer:ListAnalyzers",
                "access-analyzer:ListArchiveRules",
                "access-analyzer:ListFindings",
                "access-analyzer:ListPolicyGenerations",
                "access-analyzer:ListTagsForResource",
                "access-analyzer:ValidatePolicy",
                "acm-pca:Describe*",
                "acm-pca:Get*",
                "acm-pca:List*",
                "acm:Describe*",
                "acm:Get*",
                "acm:List*",
                "airflow:ListEnvironments",
                "airflow:ListTagsForResource",
                "amplify:GetApp",
                "amplify:GetBranch",
                "amplify:GetDomainAssociation",
                "amplify:GetJob",
                "amplify:ListApps",
                "amplify:ListBranches",
                "amplify:ListDomainAssociations",
                "amplify:ListJobs",
                "apigateway:GET",
                "appconfig:GetApplication",
                "appconfig:GetConfiguration",
                "appconfig:GetConfigurationProfile",
                "appconfig:GetDeployment",
                "appconfig:GetDeploymentStrategy",
                "appconfig:GetEnvironment",
                "appconfig:GetHostedConfigurationVersion",
                "appconfig:ListApplications",
                "appconfig:ListConfigurationProfiles",
                "appconfig:ListDeployments",
                "appconfig:ListDeploymentStrategies",
                "appconfig:ListEnvironments",
                "appconfig:ListHostedConfigurationVersions",
                "appconfig:ListTagsForResource",
                "application-autoscaling:Describe*",
                "applicationinsights:Describe*",
                "applicationinsights:List*",
                "appmesh:Describe*",
                "appmesh:List*",
                "appstream:Describe*",
                "appstream:List*",
                "appsync:Get*",
                "appsync:List*",
                "aps:DescribeAlertManagerDefinition",
                "aps:DescribeRuleGroupsNamespace",
                "aps:DescribeWorkspace",
                "aps:GetAlertManagerSilence",
                "aps:GetAlertManagerStatus",
                "aps:GetLabels",
                "aps:GetMetricMetadata",
                "aps:GetSeries",
                "aps:ListAlerts",
                "aps:ListAlertManagerAlerts",
                "aps:ListAlertManagerAlertGroups",
                "aps:ListAlertManagerReceivers",
                "aps:ListAlertManagerSilences",
                "aps:ListRules",
                "aps:ListRuleGroupsNamespaces",
                "aps:ListTagsForResource",
                "aps:ListWorkspaces",
                "aps:QueryMetrics",
                "athena:Batch*",
                "athena:Get*",
                "athena:List*",
                "auditmanager:GetAccountStatus",
                "auditmanager:GetAssessment",
                "auditmanager:GetAssessmentFramework",
                "auditmanager:GetAssessmentReportUrl",
                "auditmanager:GetChangeLogs",
                "auditmanager:GetControl",
                "auditmanager:GetDelegations",
                "auditmanager:GetEvidence",
                "auditmanager:GetEvidenceByEvidenceFolder",
                "auditmanager:GetEvidenceFolder",
                "auditmanager:GetEvidenceFoldersByAssessment",
                "auditmanager:GetEvidenceFoldersByAssessmentControl",
                "auditmanager:GetOrganizationAdminAccount",
                "auditmanager:GetServicesInScope",
                "auditmanager:GetSettings",
                "auditmanager:ListAssessmentFrameworks",
                "auditmanager:ListAssessmentReports",
                "auditmanager:ListAssessments",
                "auditmanager:ListControls",
                "auditmanager:ListKeywordsForDataSource",
                "auditmanager:ListNotifications",
                "auditmanager:ListTagsForResource",
                "auditmanager:ValidateAssessmentReportIntegrity",
                "autoscaling-plans:Describe*",
                "autoscaling-plans:GetScalingPlanResourceForecastData",
                "autoscaling:Describe*",
                "aws-portal:View*",
                "backup:Describe*",
                "backup:Get*",
                "backup:List*",
                "batch:Describe*",
                "batch:List*",
                "braket:GetDevice",
                "braket:GetQuantumTask",
                "braket:SearchDevices",
                "braket:SearchQuantumTasks",
                "budgets:Describe*",
                "budgets:View*",
                "cassandra:Select",
                "ce:DescribeCostCategoryDefinition",
                "ce:DescribeNotificationSubscription",
                "ce:DescribeReport",
                "ce:GetAnomalies",
                "ce:GetAnomalyMonitors",
                "ce:GetAnomalySubscriptions",
                "ce:GetCostAndUsage",
                "ce:GetCostAndUsageWithResources",
                "ce:GetCostCategories",
                "ce:GetCostForecast",
                "ce:GetDimensionValues",
                "ce:GetPreferences",
                "ce:GetReservationCoverage",
                "ce:GetReservationPurchaseRecommendation",
                "ce:GetReservationUtilization",
                "ce:GetRightsizingRecommendation",
                "ce:GetSavingsPlansCoverage",
                "ce:GetSavingsPlansPurchaseRecommendation",
                "ce:GetSavingsPlansUtilization",
                "ce:GetSavingsPlansUtilizationDetails",
                "ce:GetTags",
                "ce:GetUsageForecast",
                "ce:ListCostCategoryDefinitions",
                "chatbot:Describe*",
                "chatbot:Get*",
                "chime:Get*",
                "chime:List*",
                "chime:Retrieve*",
                "chime:Search*",
                "chime:Validate*",
                "cloud9:Describe*",
                "cloud9:List*",
                "clouddirectory:BatchRead",
                "clouddirectory:Get*",
                "clouddirectory:List*",
                "clouddirectory:LookupPolicy",
                "cloudformation:Describe*",
                "cloudformation:Detect*",
                "cloudformation:Estimate*",
                "cloudformation:Get*",
                "cloudformation:List*",
                "cloudfront:DescribeFunction",
                "cloudfront:Get*",
                "cloudfront:List*",
                "cloudhsm:Describe*",
                "cloudhsm:Get*",
                "cloudhsm:List*",
                "cloudsearch:Describe*",
                "cloudsearch:List*",
                "cloudtrail:Describe*",
                "cloudtrail:Get*",
                "cloudtrail:List*",
                "cloudtrail:LookupEvents",
                "cloudwatch:Describe*",
                "cloudwatch:Get*",
                "cloudwatch:List*",
                "codeartifact:DescribeDomain",
                "codeartifact:DescribePackageVersion",
                "codeartifact:DescribeRepository",
                "codeartifact:GetAuthorizationToken",
                "codeartifact:GetDomainPermissionsPolicy",
                "codeartifact:GetPackageVersionAsset",
                "codeartifact:GetPackageVersionReadme",
                "codeartifact:GetRepositoryEndpoint",
                "codeartifact:GetRepositoryPermissionsPolicy",
                "codeartifact:ListDomains",
                "codeartifact:ListPackages",
                "codeartifact:ListPackageVersionAssets",
                "codeartifact:ListPackageVersionDependencies",
                "codeartifact:ListPackageVersions",
                "codeartifact:ListRepositories",
                "codeartifact:ListRepositoriesInDomain",
                "codeartifact:ListTagsForResource",
                "codeartifact:ReadFromRepository",
                "codebuild:BatchGet*",
                "codebuild:DescribeCodeCoverages",
                "codebuild:DescribeTestCases",
                "codebuild:List*",
                "codecommit:BatchGet*",
                "codecommit:Describe*",
                "codecommit:Get*",
                "codecommit:GitPull",
                "codecommit:List*",
                "codedeploy:BatchGet*",
                "codedeploy:Get*",
                "codedeploy:List*",
                "codeguru-profiler:Describe*",
                "codeguru-profiler:Get*",
                "codeguru-profiler:List*",
                "codeguru-reviewer:Describe*",
                "codeguru-reviewer:Get*",
                "codeguru-reviewer:List*",
                "codepipeline:Get*",
                "codepipeline:List*",
                "codestar-connections:GetConnection",
                "codestar-connections:GetHost",
                "codestar-connections:ListConnections",
                "codestar-connections:ListHosts",
                "codestar-connections:ListTagsForResource",
                "codestar-notifications:describeNotificationRule",
                "codestar-notifications:listEventTypes",
                "codestar-notifications:listNotificationRules",
                "codestar-notifications:listTagsForResource",
                "codestar-notifications:ListTargets",
                "codestar:Describe*",
                "codestar:Get*",
                "codestar:List*",
                "codestar:Verify*",
                "cognito-identity:Describe*",
                "cognito-identity:GetCredentialsForIdentity",
                "cognito-identity:GetIdentityPoolRoles",
                "cognito-identity:GetOpenIdToken",
                "cognito-identity:GetOpenIdTokenForDeveloperIdentity",
                "cognito-identity:List*",
                "cognito-identity:Lookup*",
                "cognito-idp:AdminGet*",
                "cognito-idp:AdminList*",
                "cognito-idp:Describe*",
                "cognito-idp:Get*",
                "cognito-idp:List*",
                "cognito-sync:Describe*",
                "cognito-sync:Get*",
                "cognito-sync:List*",
                "cognito-sync:QueryRecords",
                "compute-optimizer:DescribeRecommendationExportJobs",
                "compute-optimizer:GetAutoScalingGroupRecommendations",
                "compute-optimizer:GetEBSVolumeRecommendations",
                "compute-optimizer:GetEC2InstanceRecommendations",
                "compute-optimizer:GetEC2RecommendationProjectedMetrics",
                "compute-optimizer:GetEnrollmentStatus",
                "compute-optimizer:GetEnrollmentStatusesForOrganization",
                "compute-optimizer:GetLambdaFunctionRecommendations",
                "compute-optimizer:GetRecommendationSummaries",
                "config:BatchGetAggregateResourceConfig",
                "config:BatchGetResourceConfig",
                "config:Deliver*",
                "config:Describe*",
                "config:Get*",
                "config:List*",
                "config:SelectAggregateResourceConfig",
                "config:SelectResourceConfig",
                "connect:Describe*",
                "connect:GetFederationToken",
                "connect:List*",
                "dataexchange:Get*",
                "dataexchange:List*",
                "datapipeline:Describe*",
                "datapipeline:EvaluateExpression",
                "datapipeline:Get*",
                "datapipeline:List*",
                "datapipeline:QueryObjects",
                "datapipeline:Validate*",
                "datasync:Describe*",
                "datasync:List*",
                "dax:BatchGetItem",
                "dax:Describe*",
                "dax:GetItem",
                "dax:ListTags",
                "dax:Query",
                "dax:Scan",
                "deepcomposer:GetComposition",
                "deepcomposer:GetModel",
                "deepcomposer:GetSampleModel",
                "deepcomposer:ListCompositions",
                "deepcomposer:ListModels",
                "deepcomposer:ListSampleModels",
                "deepcomposer:ListTrainingTopics",
                "detective:Get*",
                "detective:List*",
                "detective:SearchGraph",
                "devicefarm:Get*",
                "devicefarm:List*",
                "devops-guru:DescribeAccountHealth",
                "devops-guru:DescribeAccountOverview",
                "devops-guru:DescribeAnomaly",
                "devops-guru:DescribeFeedback",
                "devops-guru:DescribeInsight",
                "devops-guru:DescribeResourceCollectionHealth",
                "devops-guru:DescribeServiceIntegration",
                "devops-guru:GetCostEstimation",
                "devops-guru:GetResourceCollection",
                "devops-guru:ListAnomaliesForInsight",
                "devops-guru:ListEvents",
                "devops-guru:ListInsights",
                "devops-guru:ListNotificationChannels",
                "devops-guru:ListRecommendations",
                "devops-guru:SearchInsights",
                "devops-guru:StartCostEstimation",
                "directconnect:Describe*",
                "discovery:Describe*",
                "discovery:Get*",
                "discovery:List*",
                "dlm:Get*",
                "dms:Describe*",
                "dms:List*",
                "dms:Test*",
                "ds:Check*",
                "ds:Describe*",
                "ds:Get*",
                "ds:List*",
                "ds:Verify*",
                "dynamodb:BatchGet*",
                "dynamodb:Describe*",
                "dynamodb:Get*",
                "dynamodb:List*",
                "dynamodb:Query",
                "dynamodb:Scan",
                "ec2:Describe*",
                "ec2:Get*",
                "ec2:SearchTransitGatewayRoutes",
                "ec2messages:Get*",
                "ecr-public:BatchCheckLayerAvailability",
                "ecr-public:DescribeImages",
                "ecr-public:DescribeImageTags",
                "ecr-public:DescribeRegistries",
                "ecr-public:DescribeRepositories",
                "ecr-public:GetAuthorizationToken",
                "ecr-public:GetRegistryCatalogData",
                "ecr-public:GetRepositoryCatalogData",
                "ecr-public:GetRepositoryPolicy",
                "ecr-public:ListTagsForResource",
                "ecr:BatchCheck*",
                "ecr:BatchGet*",
                "ecr:Describe*",
                "ecr:Get*",
                "ecr:List*",
                "ecs:Describe*",
                "ecs:List*",
                "eks:Describe*",
                "eks:List*",
                "elasticache:Describe*",
                "elasticache:List*",
                "elasticbeanstalk:Check*",
                "elasticbeanstalk:Describe*",
                "elasticbeanstalk:List*",
                "elasticbeanstalk:Request*",
                "elasticbeanstalk:Retrieve*",
                "elasticbeanstalk:Validate*",
                "elasticfilesystem:Describe*",
                "elasticloadbalancing:Describe*",
                "elasticmapreduce:Describe*",
                "elasticmapreduce:GetBlockPublicAccessConfiguration",
                "elasticmapreduce:List*",
                "elasticmapreduce:View*",
                "elastictranscoder:List*",
                "elastictranscoder:Read*",
                "elemental-appliances-software:Get*",
                "elemental-appliances-software:List*",
                "es:Describe*",
                "es:ESHttpGet",
                "es:ESHttpHead",
                "es:Get*",
                "es:List*",
                "events:Describe*",
                "events:List*",
                "events:Test*",
                "firehose:Describe*",
                "firehose:List*",
                "fis:GetAction",
                "fis:GetExperiment",
                "fis:GetExperimentTemplate",
                "fis:ListActions",
                "fis:ListExperiments",
                "fis:ListExperimentTemplates",
                "fis:ListTagsForResource",
                "fms:GetAdminAccount",
                "fms:GetAppsList",
                "fms:GetComplianceDetail",
                "fms:GetNotificationChannel",
                "fms:GetPolicy",
                "fms:GetProtectionStatus",
                "fms:GetProtocolsList",
                "fms:GetViolationDetails",
                "fms:ListAppsLists",
                "fms:ListComplianceStatus",
                "fms:ListMemberAccounts",
                "fms:ListPolicies",
                "fms:ListProtocolsLists",
                "fms:ListTagsForResource",
                "forecast:DescribeDataset",
                "forecast:DescribeDatasetGroup",
                "forecast:DescribeDatasetImportJob",
                "forecast:DescribeForecast",
                "forecast:DescribeForecastExportJob",
                "forecast:DescribePredictor",
                "forecast:DescribePredictorBacktestExportJob",
                "forecast:GetAccuracyMetrics",
                "forecast:ListDatasetGroups",
                "forecast:ListDatasetImportJobs",
                "forecast:ListDatasets",
                "forecast:ListForecastExportJobs",
                "forecast:ListForecasts",
                "forecast:ListPredictorBacktestExportJobs",
                "forecast:ListPredictors",
                "forecast:QueryForecast",
                "freertos:Describe*",
                "freertos:List*",
                "fsx:Describe*",
                "fsx:List*",
                "gamelift:Describe*",
                "gamelift:Get*",
                "gamelift:List*",
                "gamelift:ResolveAlias",
                "gamelift:Search*",
                "glacier:Describe*",
                "glacier:Get*",
                "glacier:List*",
                "globalaccelerator:Describe*",
                "globalaccelerator:List*",
                "glue:BatchGetDevEndpoints",
                "glue:BatchGetJobs",
                "glue:BatchGetPartition",
                "glue:BatchGetTriggers",
                "glue:BatchGetWorkflows",
                "glue:CheckSchemaVersionValidity",
                "glue:GetCatalogImportStatus",
                "glue:GetClassifier",
                "glue:GetClassifiers",
                "glue:GetCrawler",
                "glue:GetCrawlerMetrics",
                "glue:GetCrawlers",
                "glue:GetDatabase",
                "glue:GetDatabases",
                "glue:GetDataCatalogEncryptionSettings",
                "glue:GetDataflowGraph",
                "glue:GetDevEndpoint",
                "glue:GetDevEndpoints",
                "glue:GetJob",
                "glue:GetJobBookmark",
                "glue:GetJobRun",
                "glue:GetJobRuns",
                "glue:GetJobs",
                "glue:GetMapping",
                "glue:GetMLTaskRun",
                "glue:GetMLTaskRuns",
                "glue:GetMLTransform",
                "glue:GetMLTransforms",
                "glue:GetPartition",
                "glue:GetPartitions",
                "glue:GetPlan",
                "glue:GetRegistry",
                "glue:GetResourcePolicy",
                "glue:GetSchema",
                "glue:GetSchemaByDefinition",
                "glue:GetSchemaVersion",
                "glue:GetSchemaVersionsDiff",
                "glue:GetSecurityConfiguration",
                "glue:GetSecurityConfigurations",
                "glue:GetTable",
                "glue:GetTables",
                "glue:GetTableVersion",
                "glue:GetTableVersions",
                "glue:GetTags",
                "glue:GetTrigger",
                "glue:GetTriggers",
                "glue:GetUserDefinedFunction",
                "glue:GetUserDefinedFunctions",
                "glue:GetWorkflow",
                "glue:GetWorkflowRun",
                "glue:GetWorkflowRunProperties",
                "glue:GetWorkflowRuns",
                "glue:ListCrawlers",
                "glue:ListDevEndpoints",
                "glue:ListJobs",
                "glue:ListMLTransforms",
                "glue:ListRegistries",
                "glue:ListSchemas",
                "glue:ListSchemaVersions",
                "glue:ListTriggers",
                "glue:ListWorkflows",
                "glue:QuerySchemaVersionMetadata",
                "greengrass:DescribeComponent",
                "greengrass:Get*",
                "greengrass:List*",
                "groundstation:DescribeContact",
                "groundstation:GetConfig",
                "groundstation:GetDataflowEndpointGroup",
                "groundstation:GetMinuteUsage",
                "groundstation:GetMissionProfile",
                "groundstation:GetSatellite",
                "groundstation:ListConfigs",
                "groundstation:ListContacts",
                "groundstation:ListDataflowEndpointGroups",
                "groundstation:ListGroundStations",
                "groundstation:ListMissionProfiles",
                "groundstation:ListSatellites",
                "groundstation:ListTagsForResource",
                "guardduty:DescribeOrganizationConfiguration",
                "guardduty:DescribePublishingDestination",
                "guardduty:Get*",
                "guardduty:List*",
                "health:Describe*",
                "iam:Generate*",
                "iam:Get*",
                "iam:List*",
                "iam:Simulate*",
                "imagebuilder:Get*",
                "imagebuilder:List*",
                "importexport:Get*",
                "importexport:List*",
                "inspector:Describe*",
                "inspector:Get*",
                "inspector:List*",
                "inspector:Preview*",
                "iot:Describe*",
                "iot:Get*",
                "iot:List*",
                "iot1click:DescribeDevice",
                "iot1click:DescribePlacement",
                "iot1click:DescribeProject",
                "iot1click:GetDeviceMethods",
                "iot1click:GetDevicesInPlacement",
                "iot1click:ListDeviceEvents",
                "iot1click:ListDevices",
                "iot1click:ListPlacements",
                "iot1click:ListProjects",
                "iot1click:ListTagsForResource",
                "iotanalytics:Describe*",
                "iotanalytics:Get*",
                "iotanalytics:List*",
                "iotanalytics:SampleChannelData",
                "iotevents:DescribeAlarm",
                "iotevents:DescribeAlarmModel",
                "iotevents:DescribeDetector",
                "iotevents:DescribeDetectorModel",
                "iotevents:DescribeInput",
                "iotevents:DescribeLoggingOptions",
                "iotevents:ListAlarmModels",
                "iotevents:ListAlarmModelVersions",
                "iotevents:ListAlarms",
                "iotevents:ListDetectorModels",
                "iotevents:ListDetectorModelVersions",
                "iotevents:ListDetectors",
                "iotevents:ListInputs",
                "iotevents:ListTagsForResource",
                "iotfleethub:DescribeApplication",
                "iotfleethub:ListApplications",
                "iotsitewise:Describe*",
                "iotsitewise:Get*",
                "iotsitewise:List*",
                "iotwireless:GetDestination",
                "iotwireless:GetDeviceProfile",
                "iotwireless:GetPartnerAccount",
                "iotwireless:GetServiceEndpoint",
                "iotwireless:GetServiceProfile",
                "iotwireless:GetWirelessDevice",
                "iotwireless:GetWirelessDeviceStatistics",
                "iotwireless:GetWirelessGateway",
                "iotwireless:GetWirelessGatewayCertificate",
                "iotwireless:GetWirelessGatewayFirmwareInformation",
                "iotwireless:GetWirelessGatewayStatistics",
                "iotwireless:GetWirelessGatewayTask",
                "iotwireless:GetWirelessGatewayTaskDefinition",
                "iotwireless:ListDestinations",
                "iotwireless:ListDeviceProfiles",
                "iotwireless:ListPartnerAccounts",
                "iotwireless:ListServiceProfiles",
                "iotwireless:ListTagsForResource",
                "iotwireless:ListWirelessDevices",
                "iotwireless:ListWirelessGateways",
                "iotwireless:ListWirelessGatewayTaskDefinitions",
                "ivs:BatchGetChannel",
                "ivs:GetChannel",
                "ivs:GetPlaybackKeyPair",
                "ivs:GetRecordingConfiguration",
                "ivs:ListChannels",
                "ivs:ListPlaybackKeyPairs",
                "ivs:ListRecordingConfigurations",
                "ivs:ListStreams",
                "ivs:ListTagsForResource",
                "kafka:Describe*",
                "kafka:Get*",
                "kafka:List*",
                "kendra:DescribeDataSource",
                "kendra:DescribeFaq",
                "kendra:DescribeIndex",
                "kendra:DescribeQuerySuggestionsBlockList",
                "kendra:DescribeQuerySuggestionsConfig",
                "kendra:DescribeThesaurus",
                "kendra:GetQuerySuggestions",
                "kendra:ListDataSources",
                "kendra:ListDataSourceSyncJobs",
                "kendra:ListFaqs",
                "kendra:ListIndices",
                "kendra:ListQuerySuggestionsBlockLists",
                "kendra:ListTagsForResource",
                "kendra:ListThesauri",
                "kendra:Query",
                "kinesis:Describe*",
                "kinesis:Get*",
                "kinesis:List*",
                "kinesisanalytics:Describe*",
                "kinesisanalytics:Discover*",
                "kinesisanalytics:Get*",
                "kinesisanalytics:List*",
                "kinesisvideo:Describe*",
                "kinesisvideo:Get*",
                "kinesisvideo:List*",
                "kms:Describe*",
                "kms:Get*",
                "kms:List*",
                "lambda:Get*",
                "lambda:List*",
                "lex:DescribeBot",
                "lex:DescribeBotAlias",
                "lex:DescribeBotChannel",
                "lex:DescribeBotLocale",
                "lex:DescribeBotVersion",
                "lex:DescribeExport",
                "lex:DescribeImport",
                "lex:DescribeIntent",
                "lex:DescribeResourcePolicy",
                "lex:DescribeSlot",
                "lex:DescribeSlotType",
                "lex:Get*",
                "lex:ListBotAliases",
                "lex:ListBotChannels",
                "lex:ListBotLocales",
                "lex:ListBots",
                "lex:ListBotVersions",
                "lex:ListBuiltInIntents",
                "lex:ListBuiltInSlotTypes",
                "lex:ListExports",
                "lex:ListImports",
                "lex:ListIntents",
                "lex:ListSlots",
                "lex:ListSlotTypes",
                "lex:ListTagsForResource",
                "license-manager:Get*",
                "license-manager:List*",
                "lightsail:GetActiveNames",
                "lightsail:GetAlarms",
                "lightsail:GetAutoSnapshots",
                "lightsail:GetBlueprints",
                "lightsail:GetBucketAccessKeys",
                "lightsail:GetBucketBundles",
                "lightsail:GetBucketMetricData",
                "lightsail:GetBuckets",
                "lightsail:GetBundles",
                "lightsail:GetCertificates",
                "lightsail:GetCloudFormationStackRecords",
                "lightsail:GetContainerAPIMetadata",
                "lightsail:GetContainerImages",
                "lightsail:GetContainerServiceDeployments",
                "lightsail:GetContainerServiceMetricData",
                "lightsail:GetContainerServicePowers",
                "lightsail:GetContainerServices",
                "lightsail:GetDisk",
                "lightsail:GetDisks",
                "lightsail:GetDiskSnapshot",
                "lightsail:GetDiskSnapshots",
                "lightsail:GetDistributionBundles",
                "lightsail:GetDistributionLatestCacheReset",
                "lightsail:GetDistributionMetricData",
                "lightsail:GetDistributions",
                "lightsail:GetDomain",
                "lightsail:GetDomains",
                "lightsail:GetExportSnapshotRecords",
                "lightsail:GetInstance",
                "lightsail:GetInstanceMetricData",
                "lightsail:GetInstancePortStates",
                "lightsail:GetInstances",
                "lightsail:GetInstanceSnapshot",
                "lightsail:GetInstanceSnapshots",
                "lightsail:GetInstanceState",
                "lightsail:GetKeyPair",
                "lightsail:GetKeyPairs",
                "lightsail:GetLoadBalancer",
                "lightsail:GetLoadBalancerMetricData",
                "lightsail:GetLoadBalancers",
                "lightsail:GetLoadBalancerTlsCertificates",
                "lightsail:GetOperation",
                "lightsail:GetOperations",
                "lightsail:GetOperationsForResource",
                "lightsail:GetRegions",
                "lightsail:GetRelationalDatabase",
                "lightsail:GetRelationalDatabaseBlueprints",
                "lightsail:GetRelationalDatabaseBundles",
                "lightsail:GetRelationalDatabaseEvents",
                "lightsail:GetRelationalDatabaseLogEvents",
                "lightsail:GetRelationalDatabaseLogStreams",
                "lightsail:GetRelationalDatabaseMetricData",
                "lightsail:GetRelationalDatabaseParameters",
                "lightsail:GetRelationalDatabases",
                "lightsail:GetRelationalDatabaseSnapshot",
                "lightsail:GetRelationalDatabaseSnapshots",
                "lightsail:GetStaticIp",
                "lightsail:GetStaticIps",
                "lightsail:Is*",
                "logs:Describe*",
                "logs:FilterLogEvents",
                "logs:Get*",
                "logs:ListTagsLogGroup",
                "logs:StartQuery",
                "logs:StopQuery",
                "logs:TestMetricFilter",
                "lookoutvision:DescribeDataset",
                "lookoutvision:DescribeModel",
                "lookoutvision:DescribeProject",
                "lookoutvision:ListDatasetEntries",
                "lookoutvision:ListModels",
                "lookoutvision:ListProjects",
                "lookoutvision:ListTagsForResource",
                "machinelearning:Describe*",
                "machinelearning:Get*",
                "macie:ListMemberAccounts",
                "macie:ListS3Resources",
                "macie2:BatchGetCustomDataIdentifiers",
                "macie2:DescribeBuckets",
                "macie2:DescribeClassificationJob",
                "macie2:DescribeOrganizationConfiguration",
                "macie2:GetAdministratorAccount",
                "macie2:GetBucketStatistics",
                "macie2:GetClassificationExportConfiguration",
                "macie2:GetCustomDataIdentifier",
                "macie2:GetFindings",
                "macie2:GetFindingsFilter",
                "macie2:GetFindingsPublicationConfiguration",
                "macie2:GetFindingStatistics",
                "macie2:GetInvitationsCount",
                "macie2:GetMacieSession",
                "macie2:GetMember",
                "macie2:GetUsageStatistics",
                "macie2:GetUsageTotals",
                "macie2:ListClassificationJobs",
                "macie2:ListCustomDataIdentifiers",
                "macie2:ListFindings",
                "macie2:ListFindingsFilters",
                "macie2:ListInvitations",
                "macie2:ListMembers",
                "macie2:ListOrganizationAdminAccounts",
                "macie2:ListTagsForResource",
                "macie2:SearchResources",
                "managedblockchain:GetNetwork",
                "managedblockchain:GetProposal",
                "managedblockchain:GetMember",
                "managedblockchain:GetNode",
                "managedblockchain:ListNetworks",
                "managedblockchain:ListProposals",
                "managedblockchain:ListProposalVotes",
                "managedblockchain:ListInvitations",
                "managedblockchain:ListMembers",
                "managedblockchain:ListNodes",
                "managedblockchain:ListTagsForResource",
                "mediaconnect:DescribeFlow",
                "mediaconnect:DescribeOffering",
                "mediaconnect:DescribeReservation",
                "mediaconnect:ListFlows",
                "mediaconvert:DescribeEndpoints",
                "mediaconvert:Get*",
                "mediaconvert:List*",
                "mediapackage:Describe*",
                "mediapackage:List*",
                "mediastore:DescribeContainer",
                "mediastore:DescribeObject",
                "mediastore:GetContainerPolicy",
                "mediastore:GetCorsPolicy",
                "mediastore:GetLifecyclePolicy",
                "mediastore:GetMetricPolicy",
                "mediastore:GetObject",
                "mediastore:ListContainers",
                "mediastore:ListItems",
                "mediastore:ListTagsForResource",
                "mgh:Describe*",
                "mgh:GetHomeRegion",
                "mgh:List*",
                "mgn:DescribeJobLogItems",
                "mgn:DescribeJobs",
                "mgn:DescribeReplicationConfigurationTemplates",
                "mgn:DescribeSourceServers",
                "mgn:GetLaunchConfiguration",
                "mgn:GetReplicationConfiguration",
                "mobileanalytics:Get*",
                "mobilehub:Describe*",
                "mobilehub:Export*",
                "mobilehub:Generate*",
                "mobilehub:Get*",
                "mobilehub:List*",
                "mobilehub:Validate*",
                "mobilehub:Verify*",
                "mobiletargeting:Get*",
                "mobiletargeting:List*",
                "monitron:GetProject",
                "monitron:GetProjectAdminUser",
                "monitron:ListProjects",
                "monitron:ListTagsForResource",
                "mq:Describe*",
                "mq:List*",
                "network-firewall:DescribeFirewall",
                "network-firewall:DescribeFirewallPolicy",
                "network-firewall:DescribeLoggingConfiguration",
                "network-firewall:DescribeResourcePolicy",
                "network-firewall:DescribeRuleGroup",
                "network-firewall:ListFirewallPolicies",
                "network-firewall:ListFirewalls",
                "network-firewall:ListRuleGroups",
                "network-firewall:ListTagsForResource",
                "networkmanager:DescribeGlobalNetworks",
                "networkmanager:GetConnections",
                "networkmanager:GetCustomerGatewayAssociations",
                "networkmanager:GetDevices",
                "networkmanager:GetLinkAssociations",
                "networkmanager:GetLinks",
                "networkmanager:GetSites",
                "networkmanager:GetTransitGatewayConnectPeerassociations",
                "networkmanager:GetTransitGatewayRegistrations",
                "opsworks-cm:Describe*",
                "opsworks-cm:List*",
                "opsworks:Describe*",
                "opsworks:Get*",
                "organizations:Describe*",
                "organizations:List*",
                "outposts:Get*",
                "outposts:List*",
                "personalize:Describe*",
                "personalize:Get*",
                "personalize:List*",
                "pi:DescribeDimensionKeys",
                "pi:GetDimensionKeyDetails",
                "pi:GetResourceMetrics",
                "polly:Describe*",
                "polly:Get*",
                "polly:List*",
                "polly:SynthesizeSpeech",
                "proton:GetEnvironment",
                "proton:GetEnvironmentTemplate",
                "proton:GetEnvironmentTemplateVersion",
                "proton:GetService",
                "proton:GetServiceInstance",
                "proton:GetServiceTemplate",
                "proton:GetServiceTemplateVersion",
                "proton:ListEnvironmentAccountConnections",
                "proton:ListEnvironments",
                "proton:ListEnvironmentTemplates",
                "proton:ListServiceInstances",
                "proton:ListServices",
                "proton:ListServiceTemplates",
                "proton:ListTagsForResource",
                "qldb:DescribeJournalS3Export",
                "qldb:DescribeLedger",
                "qldb:GetBlock",
                "qldb:GetDigest",
                "qldb:GetRevision",
                "qldb:ListJournalS3Exports",
                "qldb:ListJournalS3ExportsForLedger",
                "qldb:ListLedgers",
                "qldb:ListTagsForResource",
                "ram:Get*",
                "ram:List*",
                "rds:Describe*",
                "rds:Download*",
                "rds:List*",
                "redshift:Describe*",
                "redshift:GetReservedNodeExchangeOfferings",
                "redshift:View*",
                "rekognition:CompareFaces",
                "rekognition:Detect*",
                "rekognition:List*",
                "rekognition:Search*",
                "resource-groups:Get*",
                "resource-groups:List*",
                "resource-groups:Search*",
                "robomaker:BatchDescribe*",
                "robomaker:Describe*",
                "robomaker:Get*",
                "robomaker:List*",
                "route53-recovery-cluster:Get*",
                "route53-recovery-control-config:Describe*",
                "route53-recovery-control-config:List*",
                "route53-recovery-readiness:Get*",
                "route53-recovery-readiness:List*",
                "route53:Get*",
                "route53:List*",
                "route53:Test*",
                "route53domains:Check*",
                "route53domains:Get*",
                "route53domains:List*",
                "route53domains:View*",
                "route53resolver:Get*",
                "route53resolver:List*",
                "s3-object-lambda:GetObject",
                "s3-object-lambda:GetObjectAcl",
                "s3-object-lambda:GetObjectLegalHold",
                "s3-object-lambda:GetObjectRetention",
                "s3-object-lambda:GetObjectTagging",
                "s3-object-lambda:GetObjectVersion",
                "s3-object-lambda:GetObjectVersionAcl",
                "s3-object-lambda:GetObjectVersionTagging",
                "s3-object-lambda:ListBucket",
                "s3-object-lambda:ListBucketMultipartUploads",
                "s3-object-lambda:ListBucketVersions",
                "s3-object-lambda:ListMultipartUploadParts",
                "s3:DescribeJob",
                "s3:Get*",
                "s3:List*",
                "sagemaker:Describe*",
                "sagemaker:GetSearchSuggestions",
                "sagemaker:List*",
                "sagemaker:Search",
                "savingsplans:DescribeSavingsPlanRates",
                "savingsplans:DescribeSavingsPlans",
                "savingsplans:DescribeSavingsPlansOfferingRates",
                "savingsplans:DescribeSavingsPlansOfferings",
                "savingsplans:ListTagsForResource",
                "schemas:Describe*",
                "schemas:Get*",
                "schemas:List*",
                "schemas:Search*",
                "sdb:Get*",
                "sdb:List*",
                "sdb:Select*",
                "secretsmanager:Describe*",
                "secretsmanager:GetResourcePolicy",
                "secretsmanager:List*",
                "securityhub:Describe*",
                "securityhub:Get*",
                "securityhub:List*",
                "serverlessrepo:Get*",
                "serverlessrepo:List*",
                "serverlessrepo:SearchApplications",
                "servicecatalog:Describe*",
                "servicecatalog:GetApplication",
                "servicecatalog:GetAttributeGroup",
                "servicecatalog:List*",
                "servicecatalog:Scan*",
                "servicecatalog:Search*",
                "servicediscovery:Get*",
                "servicediscovery:List*",
                "servicequotas:GetAssociationForServiceQuotaTemplate",
                "servicequotas:GetAWSDefaultServiceQuota",
                "servicequotas:GetRequestedServiceQuotaChange",
                "servicequotas:GetServiceQuota",
                "servicequotas:GetServiceQuotaIncreaseRequestFromTemplate",
                "servicequotas:ListAWSDefaultServiceQuotas",
                "servicequotas:ListRequestedServiceQuotaChangeHistory",
                "servicequotas:ListRequestedServiceQuotaChangeHistoryByQuota",
                "servicequotas:ListServiceQuotaIncreaseRequestsInTemplate",
                "servicequotas:ListServiceQuotas",
                "servicequotas:ListServices",
                "ses:Describe*",
                "ses:Get*",
                "ses:List*",
                "shield:Describe*",
                "shield:Get*",
                "shield:List*",
                "signer:DescribeSigningJob",
                "signer:GetSigningPlatform",
                "signer:GetSigningProfile",
                "signer:ListProfilePermissions",
                "signer:ListSigningJobs",
                "signer:ListSigningPlatforms",
                "signer:ListSigningProfiles",
                "signer:ListTagsForResource",
                "snowball:Describe*",
                "snowball:Get*",
                "snowball:List*",
                "sns:Check*",
                "sns:Get*",
                "sns:List*",
                "sqs:Get*",
                "sqs:List*",
                "sqs:Receive*",
                "ssm-contacts:DescribeEngagement",
                "ssm-contacts:DescribePage",
                "ssm-contacts:GetContact",
                "ssm-contacts:GetContactChannel",
                "ssm-contacts:ListContactChannels",
                "ssm-contacts:ListContacts",
                "ssm-contacts:ListEngagements",
                "ssm-contacts:ListPageReceipts",
                "ssm-contacts:ListPagesByContact",
                "ssm-contacts:ListPagesByEngagement",
                "ssm-incidents:GetIncidentRecord",
                "ssm-incidents:GetReplicationSet",
                "ssm-incidents:GetResourcePolicies",
                "ssm-incidents:GetResponsePlan",
                "ssm-incidents:GetTimelineEvent",
                "ssm-incidents:ListIncidentRecords",
                "ssm-incidents:ListRelatedItems",
                "ssm-incidents:ListReplicationSets",
                "ssm-incidents:ListResponsePlans",
                "ssm-incidents:ListTagsForResource",
                "ssm-incidents:ListTimelineEvents",
                "ssm:Describe*",
                "ssm:Get*",
                "ssm:List*",
                "sso-directory:Describe*",
                "sso-directory:List*",
                "sso-directory:Search*",
                "sso:Describe*",
                "sso:Get*",
                "sso:List*",
                "sso:Search*",
                "states:Describe*",
                "states:GetExecutionHistory",
                "states:List*",
                "storagegateway:Describe*",
                "storagegateway:List*",
                "sts:GetAccessKeyInfo",
                "sts:GetCallerIdentity",
                "sts:GetSessionToken",
                "support:DescribeCases",
                "swf:Count*",
                "swf:Describe*",
                "swf:Get*",
                "swf:List*",
                "synthetics:Describe*",
                "synthetics:Get*",
                "synthetics:List*",
                "tag:Get*",
                "timestream:DescribeDatabase",
                "timestream:DescribeEndpoints",
                "timestream:DescribeTable",
                "timestream:ListDatabases",
                "timestream:ListMeasures",
                "timestream:ListTables",
                "timestream:ListTagsForResource",
                "transcribe:Get*",
                "transcribe:List*",
                "transfer:Describe*",
                "transfer:List*",
                "transfer:TestIdentityProvider",
                "trustedadvisor:Describe*",
                "waf-regional:Get*",
                "waf-regional:List*",
                "waf:Get*",
                "waf:List*",
                "wafv2:CheckCapacity",
                "wafv2:Describe*",
                "wafv2:Get*",
                "wafv2:List*",
                "workdocs:CheckAlias",
                "workdocs:Describe*",
                "workdocs:Get*",
                "worklink:Describe*",
                "worklink:List*",
                "workmail:Describe*",
                "workmail:Get*",
                "workmail:List*",
                "workmail:Search*",
                "workspaces:Describe*",
                "xray:BatchGet*",
                "xray:Get*"
            ],
            "Resource": "*"
        }
    ]
}
```

**Role trust relationship**

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "config.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

## How AWS Control Tower aggregates AWS Config rules in unmanaged OUs and accounts<a name="config-role-for-organizations"></a>

The AWS Control Tower management account creates an organization\-level aggregator, which assists in detecting external AWS Config rules, so that AWS Control Tower does not need to gain access to unmanaged accounts\. The AWS Control Tower console shows you how many externally created AWS Config rules you have for a given account, and links you to the AWS Config console, where you can view details about those external rules\. 

To create the aggregator, AWS Control Tower adds a role with the permissions required to describe an organization and list the accounts under it\. The `AWSControlTowerConfigAggregatorRoleForOrganizations` role requires the `AWSConfigRoleForOrganizations` managed policy and a trust relationship with `config.amazonaws.com`\.

Here is the IAM policy \(JSON artifact\) attached to the role:

```
{
    "Version": "2012-10-17",
      "Statement": [
       {
        "Effect": "Allow",
        "Action": [
          "organizations:ListAccounts",
          "organizations:DescribeOrganization",
          "organizations:ListAWSServiceAccessForOrganization"
         ],
       "Resource": "*"
      }
    ]
  }
```

Here is the `AWSControlTowerConfigAggregatorRoleForOrganizations` trust relationship:

```
{
    "Version": "2012-10-17",
      "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
        "Service": "config.amazonaws.com"
        },
        "Action": "sts:AssumeRole"
      }
    ]
  }
}
```

To deploy this functionality in the management account, the following permissions are added in the managed policy `AWSControlTowerServiceRolePolicy`, which is used by the `AWSControlTowerAdmin` role when it creates the AWS Config aggregator:

```
{
  "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": [
          "config:PutConfigurationAggregator",
          "config:DeleteConfigurationAggregator",
          "iam:PassRole"
          ],
        "Resource": [
          "arn:aws:iam:::role/service-role/AWSControlTowerConfigAggregatorRoleForOrganizations",
          "arn:aws:config:::config-aggregator/"
          ]
        },
      {
        "Effect": "Allow",
        "Action": "organizations:EnableAWSServiceAccess",
        "Resource": "*"
      }
    ]
}
```

New resources created: `AWSControlTowerConfigAggregatorRoleForOrganizations` and `aws-controltower-ConfigAggregatorForOrganizations`

When you are ready, you can enroll accounts individually, or enroll them as a group by registering an OU\. When you've enrolled an account, if you create a rule in AWS Config, AWS Control Tower detects the new rule\.  The aggregator shows the number of external rules and provides a link to the AWS Config console where you can view the details of each external rule for your account\. Use the information in the AWS Config console and the AWS Control Tower console to determine whether you have the appropriate guardrails enabled for the account\.

**Note**  
To link directly from the AWS Control Tower console to your aggregated list of AWS Config rules, configure your AWS Config console with the Config Recorder and Delivery Channel in the home Region of your management account\.

## Programmatic roles and trust relationships for the AWS Control Tower audit account<a name="stacksets-and-roles"></a>

You can sign into the audit account and assume a role to review other accounts programmatically\. The audit account does not allow you to log in to other accounts manually\.

The audit account gives you programmatic access to other accounts, by means of some roles that are granted to AWS Lambda functions only\. For security purposes, these roles have *trust relationships* with other roles, which means that the conditions under which the roles can be utilized are strictly defined\.

The AWS Control Tower stack set `StackSet-AWSControlTowerBP-BASELINE-ROLES` creates these programmatic\-only, cross\-account roles in the audit account:
+ **aws\-controltower\-AdministratorExecutionRole**
+ **aws\-controltower\-AuditAdministratorRole**
+ **aws\-controltower\-ReadOnlyExecutionRole**
+ **aws\-controltower\-AuditReadOnlyRole**

`ReadOnlyExecutionRole:` Note that this role allows the audit account to read objects in S3 buckets across the entire organization \(in contrast to the `SecurityAudit` policy, which allows for metadata access only\)\.

**aws\-controltower\-AdministratorExecutionRole:**
+ Has administrator permissions
+ Cannot be assumed from the console
+ Can be assumed only by a role in the audit account – the `aws-controltower-AuditAdministratorRole` 

The following artifact shows the trust relationship for `aws-controltower-AdministratorExecutionRole`\. The placeholder number `012345678901` will be replaced by the `Audit_acct_ID` number for your audit account\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::012345678901:role/aws-controltower-AuditAdministratorRole"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**aws\-controltower\-AuditAdministratorRole:**
+ Can be assumed by the AWS Lambda service only 
+ Has permission to perform read \(Get\) and write \(Put\) operations on Amazon S3 objects with names that start with the string **log**

**Attached policies:**

1\. **AWSLambdaExecute** – AWS managed policy

2\. **AssumeRole\-aws\-controltower\-AuditAdministratorRole** – inline policy – Created by AWS Control Tower, artifact follows\.

```
{
  "Version": "2012-10-17",
  "Statement": [
	{
	"Action": [
		 "sts:AssumeRole"
		 ],
	"Resource": [
		 "arn:aws:iam::*:role/aws-controltower-AdministratorExecutionRole"
		 ],
	"Effect": "Allow"
	}
   ]
}
```

The following artifact shows the trust relationship for `aws-controltower-AuditAdministratorRole`:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**aws\-controltower\-ReadOnlyExecutionRole:**
+ Cannot be assumed from the console
+ Can be assumed only by another role in the audit account – the `AuditReadOnlyRole`

The following artifact shows the trust relationship for `aws-controltower-ReadOnlyExecutionRole`\. The placeholder number `012345678901` will be replaced by the `Audit_acct_ID` number for your audit account\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::012345678901:role/aws-controltower-AuditReadOnlyRole "
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**aws\-controltower\-AuditReadOnlyRole:**
+ Can be assumed by the AWS Lambda service only
+ Has permission to perform read \(Get\) and write \(Put\) operations on Amazon S3 objects with names that start with the string **log**

**Attached policies:**

1\. **AWSLambdaExecute** – AWS managed policy

2\. **AssumeRole\-aws\-controltower\-AuditReadOnlyRole** – inline policy – Created by AWS Control Tower, artifact follows\.

```
{
  "Version": "2012-10-17",
  "Statement": [
	{
	"Action": [
		"sts:AssumeRole"
	],
	"Resource": [
		"arn:aws:iam::*:role/aws-controltower-ReadOnlyExecutionRole"
	],
	"Effect": "Allow"
   }
  ]
}
```

The following artifact shows the trust relationship for `aws-controltower-AuditAdministratorRole`:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

## Automated Account Provisioning With IAM Roles<a name="automated-provisioning"></a>

To configure Account Factory accounts in a more automated way, you can create Lambda functions in the AWS Control Tower management account, which [assumes the **AWSControlTowerExecution** role ](https://aws.amazon.com/premiumsupport/knowledge-center/lambda-function-assume-iam-role/) in the member account\. Then, using the role, the management account performs the desired configuration steps in each member account\.

 If you're provisioning accounts using Lambda functions, the identity that will perform this work must have the following IAM permissions policy, in addition to `AWSServiceCatalogEndUserFullAccess`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AWSControlTowerAccountFactoryAccess",
            "Effect": "Allow",
            "Action": [
                "sso:GetProfile",
                "sso:CreateProfile",
                "sso:UpdateProfile",
                "sso:AssociateProfile",
                "sso:CreateApplicationInstance",
                "sso:GetSSOStatus",
                "sso:GetTrust",
                "sso:CreateTrust",
                "sso:UpdateTrust",
                "sso:GetPeregrineStatus",
                "sso:GetApplicationInstance",
                "sso:ListDirectoryAssociations",
                "sso:ListPermissionSets",
                "sso:GetPermissionSet",
                "sso:ProvisionApplicationInstanceForAWSAccount",
                "sso:ProvisionApplicationProfileForAWSAccountInstance",
                "sso:ProvisionSAMLProvider",
                "sso:ListProfileAssociations",
                "sso-directory:ListMembersInGroup",
                "sso-directory:AddMemberToGroup",
                "sso-directory:SearchGroups",
                "sso-directory:SearchGroupsWithGroupName",
                "sso-directory:SearchUsers",
                "sso-directory:CreateUser",
                "sso-directory:DescribeGroups",
                "sso-directory:DescribeDirectory",
                "sso-directory:GetUserPoolInfo",
                "controltower:CreateManagedAccount",
                "controltower:DescribeManagedAccount",
                "controltower:DeregisterManagedAccount",
                "s3:GetObject",
                "organizations:describeOrganization",
                "sso:DescribeRegisteredRegions"
            ],
            "Resource": "*"
        }
    ]
}
```