# Amazon Redshift controls<a name="redshift-rules"></a>

**Topics**
+ [\[CT\.REDSHIFT\.PR\.1\] Require an Amazon Redshift cluster to prohibit public access](#ct-redshift-pr-1-description)
+ [\[CT\.REDSHIFT\.PR\.2\] Require an Amazon Redshift cluster to have automatic snapshots configured](#ct-redshift-pr-2-description)
+ [\[CT\.REDSHIFT\.PR\.3\] Require an Amazon Redshift cluster to have audit logging configured](#ct-redshift-pr-3-description)
+ [\[CT\.REDSHIFT\.PR\.4\] Require an Amazon Redshift cluster to have automatic upgrades to major versions configured](#ct-redshift-pr-4-description)
+ [\[CT\.REDSHIFT\.PR\.5\] Require an Amazon Redshift cluster to have enhanced VPC routing](#ct-redshift-pr-5-description)
+ [\[CT\.REDSHIFT\.PR\.6\] Require an Amazon Redshift cluster to have a unique administrator username](#ct-redshift-pr-6-description)
+ [\[CT\.REDSHIFT\.PR\.7\] Require an Amazon Redshift cluster to have a unique database name](#ct-redshift-pr-7-description)

## \[CT\.REDSHIFT\.PR\.1\] Require an Amazon Redshift cluster to prohibit public access<a name="ct-redshift-pr-1-description"></a>

This control checks whether Amazon Redshift clusters are configured to prohibit public access\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Redshift::Cluster`
+ **AWS CloudFormation guard rule: ** [CT\.REDSHIFT\.PR\.1 rule specification](#ct-redshift-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.REDSHIFT\.PR\.1 rule specification](#ct-redshift-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.REDSHIFT\.PR\.1 example templates](#ct-redshift-pr-1-templates) 

**Explanation**

The PubliclyAccessible attribute of the Amazon Redshift cluster configuration indicates whether the cluster is publicly accessible\. When the cluster is configured with the `PubliclyAccessible` parameter set to `true`, it is an internet\-facing instance that has a publicly resolvable DNS name, which resolves to a public IP address\.

When the cluster is not publicly accessible, it is an internal instance with a DNS name that resolves to a private IP address\. Unless you intend for your cluster to be publicly accessible, the cluster should not be configured with `PubliclyAccessible` set to `true`\.

### Remediation for rule failure<a name="ct-redshift-pr-1-remediation"></a>

Set `PubliclyAccessible` to `false`\.

The examples that follow show how to implement this remediation\.

#### Amazon Redshift Cluster \- Example<a name="ct-redshift-pr-1-remediation-1"></a>

Amazon Redshift cluster configured to prohibit public access\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RedshiftCluster": {
        "Type": "AWS::Redshift::Cluster",
        "Properties": {
            "ClusterType": "single-node",
            "DBName": "sampledb",
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftClusterSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftClusterSecret}::password}}"
            },
            "NodeType": "ds2.xlarge",
            "PubliclyAccessible": false
        }
    }
}
```

**YAML example**

```
RedshiftCluster:
  Type: AWS::Redshift::Cluster
  Properties:
    ClusterType: single-node
    DBName: sampledb
    MasterUsername: !Sub '{{resolve:secretsmanager:${RedshiftClusterSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${RedshiftClusterSecret}::password}}'
    NodeType: ds2.xlarge
    PubliclyAccessible: false
```

### CT\.REDSHIFT\.PR\.1 rule specification<a name="ct-redshift-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   redshift_cluster_public_access_check
# 
# Description:
#   This control checks whether Amazon Redshift clusters are configured to prohibit public access.
# 
# Reports on:
#   AWS::Redshift::Cluster
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document does not contain any Redshift cluster resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'PubliclyAccessible' has not been specified
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'PubliclyAccessible' has been specified
#       And: 'PubliclyAccessible' has been set to bool(true)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'PubliclyAccessible' has been specified
#       And: 'PubliclyAccessible' has been set to bool(false)
#      Then: PASS

#
# Constants
#
let REDSHIFT_CLUSTER_TYPE = "AWS::Redshift::Cluster"
let INPUT_DOCUMENT = this

#
# Assignments
#
let redshift_clusters = Resources.*[ Type == %REDSHIFT_CLUSTER_TYPE ]

#
# Primary Rules
#
rule redshift_cluster_public_access_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %redshift_clusters not empty  {
    check(%redshift_clusters.Properties)
         <<
        [CT.REDSHIFT.PR.1]: Require an Amazon Redshift cluster to prohibit public access
        [FIX]: Set 'PubliclyAccessible' to 'false'.
        >>
}

rule redshift_cluster_public_access_check when is_cfn_hook(%INPUT_DOCUMENT, %REDSHIFT_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%REDSHIFT_CLUSTER_TYPE.resourceProperties)
         <<
        [CT.REDSHIFT.PR.1]: Require an Amazon Redshift cluster to prohibit public access
        [FIX]: Set 'PubliclyAccessible' to 'false'.
        >>
}

#
# Parameterized Rules
#
rule check(redshift_cluster) {
    %redshift_cluster {
        # Scenario 2
        PubliclyAccessible exists
        # Scenario 3 and 4
        PubliclyAccessible == false
    }
}

#
# Utility Rules
#
rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}
```

### CT\.REDSHIFT\.PR\.1 example templates<a name="ct-redshift-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  RedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Redshift cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasterusername"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "'\"@/\\"
  RedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      ClusterType: single-node
      DBName: exampledb
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
      NodeType: dc2.large
      PubliclyAccessible: false
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  RedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Redshift cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasterusername"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "'\"@/\\"
  RedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      ClusterType: single-node
      DBName: exampledb
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
      NodeType: dc2.large
      PubliclyAccessible: true
```

## \[CT\.REDSHIFT\.PR\.2\] Require an Amazon Redshift cluster to have automatic snapshots configured<a name="ct-redshift-pr-2-description"></a>

This control checks whether Amazon Redshift clusters have automated snapshots enabled, and that the clusters are set with an automated snapshot retention period greater than or equal to seven \(7\) days\.
+ **Control objective: **Improve resiliency
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Redshift::Cluster`
+ **AWS CloudFormation guard rule: ** [CT\.REDSHIFT\.PR\.2 rule specification](#ct-redshift-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.REDSHIFT\.PR\.2 rule specification](#ct-redshift-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.REDSHIFT\.PR\.2 example templates](#ct-redshift-pr-2-templates) 

**Explanation**

Backups help you to recover more quickly from a security incident\. They strengthen the resilience of your systems\. Amazon Redshift takes periodic snapshots by default\. This control checks whether automatic snapshots are created and retained for at least seven days\.

### Remediation for rule failure<a name="ct-redshift-pr-2-remediation"></a>

Set `AutomatedSnapshotRetentionPeriod` to an integer value greater than or equal to 7 days\.

The examples that follow show how to implement this remediation\.

#### Amazon Redshift Cluster \- Example<a name="ct-redshift-pr-2-remediation-1"></a>

Amazon Redshift cluster configured with automatic snapshots active\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RedshiftCluster": {
        "Type": "AWS::Redshift::Cluster",
        "Properties": {
            "ClusterType": "single-node",
            "DBName": "sampledb",
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftSecret}::password}}"
            },
            "NodeType": "ds2.xlarge",
            "AutomatedSnapshotRetentionPeriod": 7
        }
    }
}
```

**YAML example**

```
RedshiftCluster:
  Type: AWS::Redshift::Cluster
  Properties:
    ClusterType: single-node
    DBName: sampledb
    MasterUsername: !Sub '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
    NodeType: ds2.xlarge
    AutomatedSnapshotRetentionPeriod: 7
```

### CT\.REDSHIFT\.PR\.2 rule specification<a name="ct-redshift-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   redshift_backup_enabled_check
# 
# Description:
#   This control checks whether Amazon Redshift clusters have automated snapshots enabled, and that the clusters are set with an automated snapshot retention period greater than or equal to seven (7) days.
# 
# Reports on:
#   AWS::Redshift::Cluster
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any Redshift cluster resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'AutomatedSnapshotRetentionPeriod' has not been specified
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'AutomatedSnapshotRetentionPeriod' has been specified
#       And: 'AutomatedSnapshotRetentionPeriod' has been set to '0'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'AutomatedSnapshotRetentionPeriod' has been specified
#       And: 'AutomatedSnapshotRetentionPeriod' has been set to a value <7
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'AutomatedSnapshotRetentionPeriod' has been specified
#       And: 'AutomatedSnapshotRetentionPeriod' has been set to a value >= 7
#      Then: PASS

#
# Constants
#
let REDSHIFT_CLUSTER_TYPE = "AWS::Redshift::Cluster"
let INPUT_DOCUMENT = this

#
# Assignments
#
let redshift_clusters = Resources.*[ Type == %REDSHIFT_CLUSTER_TYPE ]

#
# Primary Rules
#
rule redshift_backup_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                        %redshift_clusters not empty  {
    check(%redshift_clusters.Properties)
        <<
        [CT.REDSHIFT.PR.2]: Require an Amazon Redshift cluster to have automatic snapshots configured
        [FIX]: Set 'AutomatedSnapshotRetentionPeriod' to an integer value greater than or equal to 7 days.
        >>
}

rule redshift_backup_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %REDSHIFT_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%REDSHIFT_CLUSTER_TYPE.resourceProperties)
        <<
        [CT.REDSHIFT.PR.2]: Require an Amazon Redshift cluster to have automatic snapshots configured
        [FIX]: Set 'AutomatedSnapshotRetentionPeriod' to an integer value greater than or equal to 7 days.
        >>
}

#
# Parameterized Rules
#
rule check(redshift_cluster) {
    %redshift_cluster {
        # Scenario 2
        AutomatedSnapshotRetentionPeriod exists
        # Scenario 3, 4 and 5
        AutomatedSnapshotRetentionPeriod >= 7
    }
}

#
# Utility Rules
#
rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}
```

### CT\.REDSHIFT\.PR\.2 example templates<a name="ct-redshift-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  RedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Redshift cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasterusername"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "'\"@/\\"
  RedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      ClusterType: single-node
      DBName: exampledb
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
      NodeType: dc2.large
      PubliclyAccessible: false
      AutomatedSnapshotRetentionPeriod: 7
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  RedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Redshift cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasterusername"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "'\"@/\\"
  RedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      ClusterType: single-node
      DBName: exampledb
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
      NodeType: dc2.large
      PubliclyAccessible: false
      AutomatedSnapshotRetentionPeriod: 5
```

## \[CT\.REDSHIFT\.PR\.3\] Require an Amazon Redshift cluster to have audit logging configured<a name="ct-redshift-pr-3-description"></a>

This control checks whether an Amazon Redshift cluster has audit logging activated\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Redshift::Cluster`
+ **AWS CloudFormation guard rule: ** [CT\.REDSHIFT\.PR\.3 rule specification](#ct-redshift-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.REDSHIFT\.PR\.3 rule specification](#ct-redshift-pr-3-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.REDSHIFT\.PR\.3 example templates](#ct-redshift-pr-3-templates) 

**Explanation**

Amazon Redshift audit logging provides additional information about connections and user activities in your cluster\. This data can be stored and secured in Amazon S3, and it can be helpful for security audits and investigations\.

### Remediation for rule failure<a name="ct-redshift-pr-3-remediation"></a>

Provide a `LoggingProperties` configuration and set `BucketName` to the name of an Amazon S3 bucket configured to receive Amazon Redshift audit logs\.

The examples that follow show how to implement this remediation\.

#### Amazon Redshift Cluster \- Example<a name="ct-redshift-pr-3-remediation-1"></a>

Amazon Redshift cluster configured with audit logging enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RedshiftCluster": {
        "Type": "AWS::Redshift::Cluster",
        "Properties": {
            "ClusterType": "single-node",
            "DBName": "sampledb",
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftSecret}::password}}"
            },
            "NodeType": "dc2.large",
            "PubliclyAccessible": false,
            "LoggingProperties": {
                "BucketName": {
                    "Ref": "S3Bucket"
                },
                "S3KeyPrefix": "sample-cluster-logs"
            }
        }
    }
}
```

**YAML example**

```
RedshiftCluster:
  Type: AWS::Redshift::Cluster
  Properties:
    ClusterType: single-node
    DBName: sampledb
    MasterUsername: !Sub '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
    NodeType: dc2.large
    PubliclyAccessible: false
    LoggingProperties:
      BucketName: !Ref 'S3Bucket'
      S3KeyPrefix: sample-cluster-logs
```

### CT\.REDSHIFT\.PR\.3 rule specification<a name="ct-redshift-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   redshift_cluster_audit_logging_enabled_check
# 
# Description:
#   This control checks whether an Amazon Redshift cluster has audit logging activated.
# 
# Reports on:
#   AWS::Redshift::Cluster
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any Redshift cluster resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'LoggingProperties' has not been specified
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'LoggingProperties' has been specified
#       And: 'BucketName' on 'LoggingProperties' has been specified and is an empty string or invalid local reference
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'LoggingProperties' has been specified
#       And: 'BucketName' on 'LoggingProperties' has been specified and is a non-empty string or valid local reference
#      Then: PASS

#
# Constants
#
let REDSHIFT_CLUSTER_TYPE = "AWS::Redshift::Cluster"
let INPUT_DOCUMENT = this

#
# Assignments
#
let redshift_clusters = Resources.*[ Type == %REDSHIFT_CLUSTER_TYPE ]

#
# Primary Rules
#
rule redshift_cluster_audit_logging_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                       %redshift_clusters not empty  {
    check(%redshift_clusters.Properties)
        <<
        [CT.REDSHIFT.PR.3]: Require an Amazon Redshift cluster to have audit logging configured
            [FIX]: Provide a 'LoggingProperties' configuration and set 'BucketName' to the name of an Amazon S3 bucket configured to receive Amazon Redshift audit logs.
        >>
}

rule redshift_cluster_audit_logging_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %REDSHIFT_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%REDSHIFT_CLUSTER_TYPE.resourceProperties)
        <<
        [CT.REDSHIFT.PR.3]: Require an Amazon Redshift cluster to have audit logging configured
            [FIX]: Provide a 'LoggingProperties' configuration and set 'BucketName' to the name of an Amazon S3 bucket configured to receive Amazon Redshift audit logs.
        >>
}

#
# Parameterized Rules
#
rule check(redshift_cluster) {
    %redshift_cluster {
        # Scenario 2
        LoggingProperties exists
        LoggingProperties is_struct

        LoggingProperties {
            # Scenario 3 and 4
            BucketName exists
            check_is_string_and_not_empty(BucketName) or
            check_local_references(%INPUT_DOCUMENT, BucketName, "AWS::S3::Bucket")
        }
    }
}

#
# Utility Rules
#
rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}

rule check_is_string_and_not_empty(value) {
    %value {
        this is_string
        this != /\A\s*\z/
    }
}

rule check_local_references(doc, reference_properties, referenced_resource_type) {
    %reference_properties {
        'Fn::GetAtt' {
            query_for_resource(%doc, this[0], %referenced_resource_type)
                <<Local Stack reference was invalid>>
        } or Ref {
            query_for_resource(%doc, this, %referenced_resource_type)
                <<Local Stack reference was invalid>>
        }
    }
}

rule query_for_resource(doc, resource_key, referenced_resource_type) {
    let referenced_resource = %doc.Resources[ keys == %resource_key ]
    %referenced_resource not empty
    %referenced_resource {
        Type == %referenced_resource_type
    }
}
```

### CT\.REDSHIFT\.PR\.3 example templates<a name="ct-redshift-pr-3-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  S3Bucket:
    Type: AWS::S3::Bucket
  BucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket:
        Ref: S3Bucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
        - Effect: Allow
          Principal:
            Service: redshift.amazonaws.com
          Action:
          - s3:GetBucketAcl
          Resource:
          - Fn::GetAtt:
            - S3Bucket
            - Arn
        - Effect: Allow
          Principal:
            Service: redshift.amazonaws.com
          Action:
          - s3:PutObject
          Resource:
          - Fn::Join:
            - ''
            - - Fn::GetAtt:
                - S3Bucket
                - Arn
              - /*
  RedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Redshift cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasterusername"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "'\"@/\\"
  RedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      ClusterType: single-node
      DBName: exampledb
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
      NodeType: dc2.large
      PubliclyAccessible: false
      LoggingProperties:
        BucketName:
          Ref: S3Bucket
        S3KeyPrefix: example-cluster-logs
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  RedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Redshift cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasterusername"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "'\"@/\\"
  RedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      ClusterType: single-node
      DBName: exampledb
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
      NodeType: dc2.large
      PubliclyAccessible: false
```

## \[CT\.REDSHIFT\.PR\.4\] Require an Amazon Redshift cluster to have automatic upgrades to major versions configured<a name="ct-redshift-pr-4-description"></a>

This control checks whether automatic major version upgrades are enabled for your Amazon Redshift cluster\.
+ **Control objective: **Manage vulnerabilities
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Redshift::Cluster`
+ **AWS CloudFormation guard rule: ** [CT\.REDSHIFT\.PR\.4 rule specification](#ct-redshift-pr-4-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.REDSHIFT\.PR\.4 rule specification](#ct-redshift-pr-4-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.REDSHIFT\.PR\.4 example templates](#ct-redshift-pr-4-templates) 

**Explanation**

Enabling automatic major version upgrades ensures that the latest major version updates to Amazon Redshift clusters are installed during the maintenance window\. These updates might include security patches and bug fixes\. Keeping up to date with patch installation is an important step in securing systems\.

### Remediation for rule failure<a name="ct-redshift-pr-4-remediation"></a>

Set the `AllowVersionUpgrade` property to true or do not specify it \(default\)\.

The examples that follow show how to implement this remediation\.

#### Amazon Redshift Cluster \- Example One<a name="ct-redshift-pr-4-remediation-1"></a>

Amazon Redshift cluster with automatic major version upgrades enabled through AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RedshiftCluster": {
        "Type": "AWS::Redshift::Cluster",
        "Properties": {
            "ClusterType": "single-node",
            "DBName": "exampledb",
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftSecret}::password}}"
            },
            "NodeType": "ds2.xlarge"
        }
    }
}
```

**YAML example**

```
RedshiftCluster:
  Type: AWS::Redshift::Cluster
  Properties:
    ClusterType: single-node
    DBName: exampledb
    MasterUsername: !Sub '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
    NodeType: ds2.xlarge
```

The examples that follow show how to implement this remediation\.

#### Amazon Redshift Cluster \- Example Two<a name="ct-redshift-pr-4-remediation-2"></a>

Amazon Redshift cluster configured with automatic major version upgrades enabled through the `AllowVersionUpgrade` property\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RedshiftCluster": {
        "Type": "AWS::Redshift::Cluster",
        "Properties": {
            "ClusterType": "single-node",
            "DBName": "exampledb",
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftSecret}::password}}"
            },
            "NodeType": "ds2.xlarge",
            "AllowVersionUpgrade": true
        }
    }
}
```

**YAML example**

```
RedshiftCluster:
  Type: AWS::Redshift::Cluster
  Properties:
    ClusterType: single-node
    DBName: exampledb
    MasterUsername: !Sub '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
    NodeType: ds2.xlarge
    AllowVersionUpgrade: true
```

### CT\.REDSHIFT\.PR\.4 rule specification<a name="ct-redshift-pr-4-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   redshift_cluster_allow_version_upgrade_check
# 
# Description:
#   Checks whether automatic major version upgrades are enabled for the Amazon Redshift cluster.
# 
# Reports on:
#   AWS::Redshift::Cluster
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document does not contain any Redshift cluster resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'AllowVersionUpgrade' has been provided
#       And: 'AllowVersionUpgrade' has been set to bool(false)
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'AllowVersionUpgrade' has not been provided
#      Then: PASS
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'AllowVersionUpgrade' has been provided
#       And: 'AllowVersionUpgrade' has been set to bool(true)
#      Then: PASS

#
# Constants
#
let REDSHIFT_CLUSTER_TYPE = "AWS::Redshift::Cluster"
let INPUT_DOCUMENT = this

#
# Assignments
#
let redshift_clusters = Resources.*[ Type == %REDSHIFT_CLUSTER_TYPE ]

#
# Primary Rules
#
rule redshift_cluster_allow_version_upgrade_check when is_cfn_template(%INPUT_DOCUMENT)
                                                       %redshift_clusters not empty  {
    check(%redshift_clusters.Properties)
        <<
        [CT.REDSHIFT.PR.4]: Require an Amazon Redshift cluster to have automatic upgrades to major versions configured
        [FIX]: Set the 'AllowVersionUpgrade' property to true or do not specify it (default).
        >>
}

rule redshift_cluster_allow_version_upgrade_check when is_cfn_hook(%INPUT_DOCUMENT, %REDSHIFT_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%REDSHIFT_CLUSTER_TYPE.resourceProperties)
        <<
        [CT.REDSHIFT.PR.4]: Require an Amazon Redshift cluster to have automatic upgrades to major versions configured
        [FIX]: Set the 'AllowVersionUpgrade' property to true or do not specify it (default).
        >>
}

#
# Parameterized Rules
#
rule check(redshift_cluster) {
    %redshift_cluster {
        # Scenario 3
        AllowVersionUpgrade not exists or
        # Scenario 2 and 4
        AllowVersionUpgrade == true
    }
}

#
# Utility Rules
#
rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}
```

### CT\.REDSHIFT\.PR\.4 example templates<a name="ct-redshift-pr-4-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  RedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Redshift cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasterusername"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "'\"@/\\"
  RedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      ClusterType: single-node
      DBName: exampledb
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
      NodeType: dc2.large
      PubliclyAccessible: false
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  RedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Redshift cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasterusername"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "'\"@/\\"
  RedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      ClusterType: single-node
      DBName: exampledb
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
      NodeType: dc2.large
      PubliclyAccessible: false
      AllowVersionUpgrade: false
```

## \[CT\.REDSHIFT\.PR\.5\] Require an Amazon Redshift cluster to have enhanced VPC routing<a name="ct-redshift-pr-5-description"></a>

This control checks whether an Amazon Redshift cluster has enhanced VPC routing configured\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Redshift::Cluster`
+ **AWS CloudFormation guard rule: ** [CT\.REDSHIFT\.PR\.5 rule specification](#ct-redshift-pr-5-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.REDSHIFT\.PR\.5 rule specification](#ct-redshift-pr-5-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.REDSHIFT\.PR\.5 example templates](#ct-redshift-pr-5-templates) 

**Explanation**

Enhanced VPC routing forces all `copy` and `unload` traffic between the cluster and the data repositories to go through your VPC\. With enhanced routing active, you can use VPC features, such as security groups and network access control lists, to secure network traffic\. You can also use VPC Flow Logs to monitor network traffic\.

### Remediation for rule failure<a name="ct-redshift-pr-5-remediation"></a>

Set `EnhancedVpcRouting` to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon Redshift Cluster \- Example<a name="ct-redshift-pr-5-remediation-1"></a>

Amazon Redshift cluster configured with enhanced VPC routing\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RedshiftCluster": {
        "Type": "AWS::Redshift::Cluster",
        "Properties": {
            "ClusterType": "single-node",
            "DBName": "sampledb",
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftSecret}::password}}"
            },
            "NodeType": "ds2.xlarge",
            "EnhancedVpcRouting": true
        }
    }
}
```

**YAML example**

```
RedshiftCluster:
  Type: AWS::Redshift::Cluster
  Properties:
    ClusterType: single-node
    DBName: sampledb
    MasterUsername: !Sub '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
    NodeType: ds2.xlarge
    EnhancedVpcRouting: true
```

### CT\.REDSHIFT\.PR\.5 rule specification<a name="ct-redshift-pr-5-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   redshift_enhanced_vpc_routing_enabled_check
# 
# Description:
#   This control checks whether an Amazon Redshift cluster has enhanced VPC routing configured.
# 
# Reports on:
#   AWS::Redshift::Cluster
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any Redshift cluster resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'EnhancedVpcRouting' has not been specified
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'EnhancedVpcRouting' has been specified
#       And: 'EnhancedVpcRouting' has been set to bool(false)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'EnhancedVpcRouting' has been specified
#       And: 'EnhancedVpcRouting' has been set to bool(true)
#      Then: PASS

#
# Constants
#
let REDSHIFT_CLUSTER_TYPE = "AWS::Redshift::Cluster"
let INPUT_DOCUMENT = this

#
# Assignments
#
let redshift_clusters = Resources.*[ Type == %REDSHIFT_CLUSTER_TYPE ]

#
# Primary Rules
#
rule redshift_enhanced_vpc_routing_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                      %redshift_clusters not empty {
    check(%redshift_clusters.Properties)
        <<
        [CT.REDSHIFT.PR.5]: Require an Amazon Redshift cluster to have enhanced VPC routing
        [FIX]: Set 'EnhancedVpcRouting' to 'true'.
        >>
}

rule redshift_enhanced_vpc_routing_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %REDSHIFT_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%REDSHIFT_CLUSTER_TYPE.resourceProperties)
        <<
        [CT.REDSHIFT.PR.5]: Require an Amazon Redshift cluster to have enhanced VPC routing
        [FIX]: Set 'EnhancedVpcRouting' to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(redshift_cluster) {
    %redshift_cluster {
        # Scenario 2
        EnhancedVpcRouting exists
        # Scenario 3 and 4
        EnhancedVpcRouting == true
    }
}

#
# Utility Rules
#
rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}
```

### CT\.REDSHIFT\.PR\.5 example templates<a name="ct-redshift-pr-5-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  RedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Redshift cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasterusername"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "\"@/\\"
  RedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      ClusterType: single-node
      DBName: exampledb
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
      NodeType: dc2.large
      PubliclyAccessible: false
      EnhancedVpcRouting: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  RedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Redshift cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasterusername"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "\"@/\\"
  RedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      ClusterType: single-node
      DBName: exampledb
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
      NodeType: dc2.large
      PubliclyAccessible: false
      EnhancedVpcRouting: false
```

## \[CT\.REDSHIFT\.PR\.6\] Require an Amazon Redshift cluster to have a unique administrator username<a name="ct-redshift-pr-6-description"></a>

This control checks whether an Amazon Redshift cluster has changed the administrator username from its default value\.
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Redshift::Cluster`
+ **AWS CloudFormation guard rule: ** [CT\.REDSHIFT\.PR\.6 rule specification](#ct-redshift-pr-6-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.REDSHIFT\.PR\.6 rule specification](#ct-redshift-pr-6-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.REDSHIFT\.PR\.6 example templates](#ct-redshift-pr-6-templates) 

**Explanation**

When creating an Amazon Redshift cluster, you should change the default administrator username to a unique value\. Default usernames are public knowledge, so they should be changed upon configuration\. Changing the default username reduces the risk of unintended access\.

### Remediation for rule failure<a name="ct-redshift-pr-6-remediation"></a>

Set `MasterUsername` to a value other than `awsuser`\.

The examples that follow show how to implement this remediation\.

#### Amazon Redshift Cluster \- Example<a name="ct-redshift-pr-6-remediation-1"></a>

Amazon Redshift cluster configured with an administrator username different from the default value of `awsuser`\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RedshiftCluster": {
        "Type": "AWS::Redshift::Cluster",
        "Properties": {
            "ClusterType": "single-node",
            "DBName": "sampledb",
            "MasterUsername": "samplemasterusername",
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftClusterSecret}::password}}"
            },
            "NodeType": "ds2.xlarge"
        }
    }
}
```

**YAML example**

```
RedshiftCluster:
  Type: AWS::Redshift::Cluster
  Properties:
    ClusterType: single-node
    DBName: sampledb
    MasterUsername: samplemasterusername
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${RedshiftClusterSecret}::password}}'
    NodeType: ds2.xlarge
```

### CT\.REDSHIFT\.PR\.6 rule specification<a name="ct-redshift-pr-6-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   redshift_default_admin_check
# 
# Description:
#   This control checks whether an Amazon Redshift cluster has changed the administrator username from its default value.
# 
# Reports on:
#   AWS::Redshift::Cluster
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any Redshift cluster resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'MasterUsername' has not been specified
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'MasterUsername' has been specified
#       And: 'MasterUsername' has been set to 'awsuser'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: 'MasterUsername' has been specified
#       And: 'MasterUsername' has been set to a value not equal to 'awsuser'
#      Then: PASS

#
# Constants
#
let REDSHIFT_CLUSTER_TYPE = "AWS::Redshift::Cluster"
let INPUT_DOCUMENT = this

#
# Assignments
#
let redshift_clusters = Resources.*[ Type == %REDSHIFT_CLUSTER_TYPE ]

#
# Primary Rules
#
rule redshift_default_admin_check when is_cfn_template(%INPUT_DOCUMENT)
                                       %redshift_clusters not empty  {
    check(%redshift_clusters.Properties)
        <<
        [CT.REDSHIFT.PR.6]: Require an Amazon Redshift cluster to have a unique administrator username
        [FIX]: Set 'MasterUsername' to a value other than 'awsuser'.
        >>
}

rule redshift_default_admin_check when is_cfn_hook(%INPUT_DOCUMENT, %REDSHIFT_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%REDSHIFT_CLUSTER_TYPE.resourceProperties)
        <<
        [CT.REDSHIFT.PR.6]: Require an Amazon Redshift cluster to have a unique administrator username
        [FIX]: Set 'MasterUsername' to a value other than 'awsuser'.
        >>
}

#
# Parameterized Rules
#
rule check(redshift_cluster) {
    %redshift_cluster {
        # Scenario 2
        MasterUsername exists
        check_is_string_and_not_empty(MasterUsername)

        # Scenario 3 and 4
        MasterUsername != "awsuser"
    }
}

#
# Utility Rules
#
rule check_is_string_and_not_empty(value) {
    %value {
        this is_string
        this != /\A\s*\z/
    }
}

rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}
```

### CT\.REDSHIFT\.PR\.6 example templates<a name="ct-redshift-pr-6-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  RedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Redshift cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasterusername"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "'\"@/\\"
  RedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      ClusterType: single-node
      DBName: exampledb
      MasterUsername: examplemasterusername
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
      NodeType: dc2.large
      PubliclyAccessible: false
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  RedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Redshift cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "awsuser"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "'\"@/\\"
  RedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      ClusterType: single-node
      DBName: exampledb
      MasterUsername: awsuser
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
      NodeType: dc2.large
      PubliclyAccessible: false
```

## \[CT\.REDSHIFT\.PR\.7\] Require an Amazon Redshift cluster to have a unique database name<a name="ct-redshift-pr-7-description"></a>

This control checks whether an Amazon Redshift cluster has changed its database name from the default value\.
+ **Control objective: **Use strong authentication
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::Redshift::Cluster`
+ **AWS CloudFormation guard rule: ** [CT\.REDSHIFT\.PR\.7 rule specification](#ct-redshift-pr-7-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.REDSHIFT\.PR\.7 rule specification](#ct-redshift-pr-7-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.REDSHIFT\.PR\.7 example templates](#ct-redshift-pr-7-templates) 

**Explanation**

When creating a Redshift cluster, you should change the default database name to a unique value\. Default names are public knowledge, so they should be changed upon configuration\. For example, a well\-known name can lead to inadvertent access, if included in IAM policy conditions\.

### Remediation for rule failure<a name="ct-redshift-pr-7-remediation"></a>

Set `DBName` to a database name that is different from the default value of `dev`\.

The examples that follow show how to implement this remediation\.

#### Amazon Redshift Cluster \- Example<a name="ct-redshift-pr-7-remediation-1"></a>

Amazon Redshift cluster configured with a database name different from the default value of `dev`\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RedshiftCluster": {
        "Type": "AWS::Redshift::Cluster",
        "Properties": {
            "ClusterType": "single-node",
            "DBName": "sampledb",
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftClusterSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${RedshiftClusterSecret}::password}}"
            },
            "NodeType": "dc2.large",
            "PubliclyAccessible": false
        }
    }
}
```

**YAML example**

```
RedshiftCluster:
  Type: AWS::Redshift::Cluster
  Properties:
    ClusterType: single-node
    DBName: sampledb
    MasterUsername: !Sub '{{resolve:secretsmanager:${RedshiftClusterSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${RedshiftClusterSecret}::password}}'
    NodeType: dc2.large
    PubliclyAccessible: false
```

### CT\.REDSHIFT\.PR\.7 rule specification<a name="ct-redshift-pr-7-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   redshift_default_db_name_check
# 
# Description:
#   This control checks whether an Amazon Redshift cluster has changed its database name from the default value.
# 
# Reports on:
#   AWS::Redshift::Cluster
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document does not contain any Redshift cluster resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains a Redshift cluster resource
#       And: The 'DBName' property has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains Redshift cluster resource
#       And: The 'DBName' property has been provided with a value of 'dev' or an empty string
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains Redshift cluster resource
#       And: The 'DBName' property has been provided with a non-empty string that is not equal to 'dev'
#      Then: PASS

#
# Constants
#
let RESOURCE_TYPE = "AWS::Redshift::Cluster"
let INPUT_DOCUMENT = this
let INVALID_DB_NAME_STRING = "dev"

#
# Assignments
#
let redshift_clusters = Resources.*[ Type == %RESOURCE_TYPE ]

#
# Primary Rules
#
rule redshift_default_db_name_check when is_cfn_template(%INPUT_DOCUMENT)
                                         %redshift_clusters not empty {
    check_db_name(%redshift_clusters.Properties)
        <<
        [CT.REDSHIFT.PR.7]: Require an Amazon Redshift cluster to have a unique database name
        [FIX]: Set 'DBName' to a database name that is different from the default value of 'dev'.
        >>
}

rule redshift_default_db_name_check when is_cfn_hook(%INPUT_DOCUMENT, %RESOURCE_TYPE) {
    check_db_name(%INPUT_DOCUMENT.%RESOURCE_TYPE.resourceProperties)
        <<
        [CT.REDSHIFT.PR.7]: Require an Amazon Redshift cluster to have a unique database name
        [FIX]: Set 'DBName' to a database name that is different from the default value of 'dev'.
        >>
}

#
# Parameterized Rules
#
rule check_db_name(redshift_cluster) {
    %redshift_cluster {
        # Scenario 2
        DBName exists

        # Scenario 3 and 4
        check_is_string_and_not_empty(DBName)
        DBName != %INVALID_DB_NAME_STRING
    }
}

#
# Utility Rules
#
rule is_cfn_template(doc) {
    %doc {
        AWSTemplateFormatVersion exists  or
        Resources exists
    }
}

rule is_cfn_hook(doc, RESOURCE_TYPE) {
    %doc.%RESOURCE_TYPE.resourceProperties exists
}

rule check_is_string_and_not_empty(value) {
    %value {
        this is_string
        this != /\A\s*\z/
    }
}
```

### CT\.REDSHIFT\.PR\.7 example templates<a name="ct-redshift-pr-7-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  RedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Redshift cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasterusername"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "'\"@/\\"
  RedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      ClusterType: "single-node"
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
      NodeType: dc2.large
      PubliclyAccessible: false
      DBName: exampledb
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  RedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Redshift cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasterusername"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "'\"@/\\"
  RedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      ClusterType: "single-node"
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${RedshiftSecret}::password}}'
      NodeType: dc2.large
      PubliclyAccessible: false
      DBName: dev
```