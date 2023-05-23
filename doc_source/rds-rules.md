# Amazon Relational Database Service \(Amazon RDS\) controls<a name="rds-rules"></a>

**Topics**
+ [\[CT\.RDS\.PR\.1\] Require that an Amazon RDS database instance is configured with multiple Availability Zones](#ct-rds-pr-1-description)
+ [\[CT\.RDS\.PR\.2\] Require an Amazon RDS database instance or cluster to have enhanced monitoring configured](#ct-rds-pr-2-description)
+ [\[CT\.RDS\.PR\.3\] Require an Amazon RDS cluster to have deletion protection configured](#ct-rds-pr-3-description)
+ [\[CT\.RDS\.PR\.4\] Require an Amazon RDS database cluster to have AWS IAM database authentication configured](#ct-rds-pr-4-description)
+ [\[CT\.RDS\.PR\.5\] Require an Amazon RDS database instance to have minor version upgrades configured](#ct-rds-pr-5-description)
+ [\[CT\.RDS\.PR\.6\] Require an Amazon RDS database cluster to have backtracking configured](#ct-rds-pr-6-description)
+ [\[`CT.RDS.PR.7`\] Require Amazon RDS database instances to have IAM authentication configured](#ct-rds-pr-7-description)
+ [\[CT\.RDS\.PR\.8\] Require an Amazon RDS database instance to have automatic backups configured](#ct-rds-pr-8-description)
+ [\[CT\.RDS\.PR\.9\] Require an Amazon RDS database cluster to copy tags to snapshots](#ct-rds-pr-9-description)
+ [\[CT\.RDS\.PR\.10\] Require an Amazon RDS database instance to copy tags to snapshots](#ct-rds-pr-10-description)
+ [\[CT\.RDS\.PR\.11\] Require an Amazon RDS database instance to have a VPC configuration](#ct-rds-pr-11-description)
+ [\[CT\.RDS\.PR\.12\] Require an Amazon RDS event subscription to have critical cluster events configured](#ct-rds-pr-12-description)
+ [\[CT\.RDS\.PR\.13\] Require any Amazon RDS instance to have deletion protection configured](#ct-rds-pr-13-description)
+ [\[CT\.RDS\.PR\.14\] Require an Amazon RDS database instance to have logging configured](#ct-rds-pr-14-description)
+ [\[CT\.RDS\.PR\.15\] Require that an Amazon RDS instance does not create DB security groups](#ct-rds-pr-15-description)
+ [\[CT\.RDS\.PR\.16\] Require an Amazon RDS database cluster to have encryption at rest configured](#ct-rds-pr-16-description)
+ [\[CT\.RDS\.PR\.17\] Require an Amazon RDS event notification subscription to have critical database instance events configured](#ct-rds-pr-17-description)
+ [\[CT\.RDS\.PR\.18\] Require an Amazon RDS event notification subscription to have critical database parameter group events configured](#ct-rds-pr-18-description)
+ [\[CT\.RDS\.PR\.19\] Require an Amazon RDS event notifications subscription to have critical database security group events configured](#ct-rds-pr-19-description)
+ [\[CT\.RDS\.PR\.20\] Require an Amazon RDS database instance not to use a database engine default port](#ct-rds-pr-20-description)
+ [\[CT\.RDS\.PR\.21\] Require an Amazon RDS DB cluster to have a unique administrator username](#ct-rds-pr-21-description)
+ [\[CT\.RDS\.PR\.22\] Require an Amazon RDS database instance to have a unique administrator username](#ct-rds-pr-22-description)
+ [\[CT\.RDS\.PR\.23\] Require an Amazon RDS database instance to not be publicly accessible](#ct-rds-pr-23-description)
+ [\[CT\.RDS\.PR\.24\] Require an Amazon RDS database instance to have encryption at rest configured](#ct-rds-pr-24-description)
+ [\[CT\.RDS\.PR\.25\] Require an Amazon RDS database cluster to have logging configured](#ct-rds-pr-25-description)

## \[CT\.RDS\.PR\.1\] Require that an Amazon RDS database instance is configured with multiple Availability Zones<a name="ct-rds-pr-1-description"></a>

This control checks whether high availability is configured for your Amazon Relational Database Service \(RDS\) database instances\.
+ **Control objective: **Improve availability
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBInstance`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.1 rule specification](#ct-rds-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.1 rule specification](#ct-rds-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.1 example templates](#ct-rds-pr-1-templates) 

**Explanation**

Amazon RDS database \(DB\) instances should be configured for multiple Availability Zones \(AZs\)\. This configuration increases the availability of the stored data\. Deployment into multiple Availabiliy Zones allows for automated failover, in case an Availability Zone has an outage, and during regular RDS maintenance\.

**Usage considerations**  
This control applies only to Amazon RDS DB engine types `mariadb`, `mysql`, `oracle-ee`, `oracle-ee-cdb`, `oracle-se2`, `oracle-se2-cdb`, `postgres`, `sqlserver-ee`, `sqlserver-se`, `sqlserver-ex` and `sqlserver-web`\.

### Remediation for rule failure<a name="ct-rds-pr-1-remediation"></a>

Set `MultiAZ` to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example<a name="ct-rds-pr-1-remediation-1"></a>

Amazon RDS database instance configured with multiple Availability Zones\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "postgres",
            "EngineVersion": 14.2,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            },
            "MultiAZ": true
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: postgres
    EngineVersion: 14.2
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    MultiAZ: true
  DeletionPolicy: Delete
```

### CT\.RDS\.PR\.1 rule specification<a name="ct-rds-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_instance_multi_az_support_check
# 
# Description:
#   This control checks whether high availability is configured for your Amazon Relational Database Service (RDS) database instances.
# 
# Reports on:
#   AWS::RDS::DBInstance
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
#       And: The input document does not contain any RDS DB instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is not one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#            'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#            'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'MultiAZ' has not been specified
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#            'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'MultiAZ' has been specified
#       And: 'MultiAZ' has been set to bool(false)
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#            'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'MultiAZ' has been specified
#       And: 'MultiAZ' has been set to bool(true)
#      Then: PASS

#
# Constants
#
let RDS_DB_INSTANCE_TYPE = "AWS::RDS::DBInstance"
let INPUT_DOCUMENT = this
let SUPPORTED_RDS_INSTANCE_ENGINES = [
    "mariadb", "mysql", "oracle-ee", "oracle-ee-cdb", "oracle-se2",
    "oracle-se2-cdb", "postgres", "sqlserver-ee", "sqlserver-se",
    "sqlserver-ex", "sqlserver-web"
]

#
# Assignments
#
let rds_db_instances = Resources.*[ Type == %RDS_DB_INSTANCE_TYPE ]

#
# Primary Rules
#
rule rds_instance_multi_az_support_check when is_cfn_template(%INPUT_DOCUMENT)
                                              %rds_db_instances not empty {
    check(%rds_db_instances.Properties)
        <<
        [CT.RDS.PR.1]: Require that an Amazon RDS database instance is configured with multiple Availability Zones
        [FIX]: Set 'MultiAZ' to 'true'.
        >>
}

rule rds_instance_multi_az_support_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.1]: Require that an Amazon RDS database instance is configured with multiple Availability Zones
        [FIX]: Set 'MultiAZ' to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(rds_db_instance) {
    %rds_db_instance [filter_engine(this)] {
       # Scenario 3
       MultiAZ exists
       # Scenario 4 and 5
       MultiAZ == true
    }
}

rule filter_engine(db_properties) {
    %db_properties {
        # Scenario 2
        Engine exists
        Engine is_string
        Engine in %SUPPORTED_RDS_INSTANCE_ENGINES
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

### CT\.RDS\.PR\.1 example templates<a name="ct-rds-pr-1-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      MultiAZ: true
    DeletionPolicy: Delete
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      MultiAZ: false
    DeletionPolicy: Delete
```

## \[CT\.RDS\.PR\.2\] Require an Amazon RDS database instance or cluster to have enhanced monitoring configured<a name="ct-rds-pr-2-description"></a>

This control checks whether enhanced monitoring is activated for Amazon Relational Database Service \(RDS\) instances\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBInstance`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.2 rule specification](#ct-rds-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.2 rule specification](#ct-rds-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.2 example templates](#ct-rds-pr-2-templates) 

**Explanation**

In Amazon RDS, enhanced monitoring facilitates a more rapid response to performance changes in underlying infrastructure\. These performance changes could result in a lack of availability of the data\. Enhanced monitoring provides real\-time metrics of the operating system on which your RDS DB instance runs\. An agent, installed on the instance, can obtain metrics more accurately than is possible from the hypervisor layer\.

Enhanced monitoring metrics are useful when you want to see how different processes or threads on a database \(DB\) instance use the CPU\.

**Usage considerations**  
This control applies only to Amazon RDS DB engine types `aurora`, `aurora-mysql`, `aurora-postgresql`, `mariadb`, `mysql`, `oracle-ee`, `oracle-ee-cdb`, `oracle-se2`, `oracle-se2-cdb`, `postgres`, `sqlserver-ee`, `sqlserver-se`, `sqlserver-ex` and `sqlserver-web`

### Remediation for rule failure<a name="ct-rds-pr-2-remediation"></a>

Set `MonitoringInterval` to a supported value \(1, 5, 10, 15, 30, 60\), and set `MonitoringRoleArn` to the ARN of an AWS IAM role\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example<a name="ct-rds-pr-2-remediation-1"></a>

Amazon RDS DB instance configured with enhanced monitoring\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "postgres",
            "EngineVersion": 14.2,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            },
            "MonitoringInterval": 30,
            "MonitoringRoleArn": {
                "Fn::GetAtt": [
                    "MonitoringIAMRole",
                    "Arn"
                ]
            }
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: postgres
    EngineVersion: 14.2
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    MonitoringInterval: 30
    MonitoringRoleArn: !GetAtt 'MonitoringIAMRole.Arn'
  DeletionPolicy: Delete
```

### CT\.RDS\.PR\.2 rule specification<a name="ct-rds-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_instance_enhanced_monitoring_enabled_check
# 
# Description:
#   This control checks whether enhanced monitoring is activated for Amazon Relational Database Service (RDS) instances.
# 
# Reports on:
#   AWS::RDS::DBInstance
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
#       And: The input document does not contain any RDS DB instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is not one of 'aurora', 'aurora-mysql', 'aurora-postgresql', 'mariadb', 'mysql',
#            'oracle-ee', 'oracle-se2', 'oracle-se1', 'oracle-se', 'postgres', 'sqlserver-ee',
#            'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'aurora', 'aurora-mysql', 'aurora-postgresql', 'mariadb', 'mysql',
#            'oracle-ee', 'oracle-se2', 'oracle-se1', 'oracle-se', 'postgres', 'sqlserver-ee',
#            'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'MonitoringInterval' has not been specified
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'aurora', 'aurora-mysql', 'aurora-postgresql', 'mariadb', 'mysql',
#            'oracle-ee', 'oracle-se2', 'oracle-se1', 'oracle-se', 'postgres', 'sqlserver-ee',
#            'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'MonitoringInterval' has been specified
#       And: 'MonitoringInterval' has been set to '0'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'aurora', 'aurora-mysql', 'aurora-postgresql', 'mariadb', 'mysql',
#            'oracle-ee', 'oracle-se2', 'oracle-se1', 'oracle-se', 'postgres', 'sqlserver-ee',
#            'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'MonitoringInterval' has been specified
#       And: 'MonitoringInterval' has not been set to a value from the list 1, 5, 10, 15, 30, 60
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'aurora', 'aurora-mysql', 'aurora-postgresql', 'mariadb', 'mysql',
#            'oracle-ee', 'oracle-se2', 'oracle-se1', 'oracle-se', 'postgres', 'sqlserver-ee',
#            'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'MonitoringInterval' has been specified
#       And: 'MonitoringInterval' has been set to a value from the list 1, 5, 10, 15, 30, 60
#       And: 'MonitoringRoleArn' has not been specified or specified as an empty string
#      Then: FAIL
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'aurora', 'aurora-mysql', 'aurora-postgresql', 'mariadb', 'mysql',
#            'oracle-ee', 'oracle-se2', 'oracle-se1', 'oracle-se', 'postgres', 'sqlserver-ee',
#            'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'MonitoringInterval' has been specified
#       And: 'MonitoringInterval' has been set to a value from the list 1, 5, 10, 15, 30, 60
#       And: 'MonitoringRoleArn' has been specified with a non-empty string or valid local reference
#      Then: PASS

#
# Constants
#
let RDS_DB_INSTANCE_TYPE = "AWS::RDS::DBInstance"
let INPUT_DOCUMENT = this
let SUPPORTED_RDS_INSTANCE_ENGINES = [
    "aurora", "aurora-mysql", "aurora-postgresql", "mariadb", "mysql",
    "oracle-ee", "oracle-ee-cdb", "oracle-se2", "oracle-se2-cdb",
    "postgres", "sqlserver-ee", "sqlserver-se",
    "sqlserver-ex", "sqlserver-web"
]
let ALLOWED_EM_VALUES = [1, 5, 10, 15, 30, 60]

#
# Assignments
#
let rds_db_instances = Resources.*[ Type == %RDS_DB_INSTANCE_TYPE ]

#
# Primary Rules
#
rule rds_instance_enhanced_monitoring_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                         %rds_db_instances not empty {
    check(%rds_db_instances.Properties)
        <<
        [CT.RDS.PR.2]: Require an Amazon RDS database instance or cluster to have enhanced monitoring configured
        [FIX]: Set 'MonitoringInterval' to a supported value (1, 5, 10, 15, 30, 60), and set 'MonitoringRoleArn' to the ARN of an AWS IAM role.
        >>
}

rule rds_instance_enhanced_monitoring_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.2]: Require an Amazon RDS database instance or cluster to have enhanced monitoring configured
        [FIX]: Set 'MonitoringInterval' to a supported value (1, 5, 10, 15, 30, 60), and set 'MonitoringRoleArn' to the ARN of an AWS IAM role.
        >>
}

#
# Parameterized Rules
#
rule check(rds_db_instance) {
    %rds_db_instance [filter_engine(this)] {
        # Scenario: 3, 4, 5, 6 and 7
        MonitoringInterval exists
        MonitoringInterval in %ALLOWED_EM_VALUES
        # Scenario: 6 and 7
        MonitoringRoleArn exists
        check_for_valid_monitor_role_arn(MonitoringRoleArn)
    }
}

rule filter_engine(db_properties) {
    %db_properties {
        # Scenario: 2
        Engine exists
        Engine is_string
        Engine in %SUPPORTED_RDS_INSTANCE_ENGINES
    }
}

rule check_for_valid_monitor_role_arn(iam_role_arn) {
   %iam_role_arn {
      check_is_string_and_not_empty(this) or
      check_local_references(%INPUT_DOCUMENT, this, "AWS::IAM::Role")
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

### CT\.RDS\.PR\.2 example templates<a name="ct-rds-pr-2-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  MonitoringIAMRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
        - Effect: "Allow"
          Principal:
            Service:
            - "monitoring.rds.amazonaws.com"
          Action:
          - "sts:AssumeRole"
      Path: "/"
      ManagedPolicyArns:
      - arn:aws:iam::aws:policy/service-role/AmazonRDSEnhancedMonitoringRole
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      MonitoringInterval: 30
      MonitoringRoleArn:
        Fn::GetAtt: ["MonitoringIAMRole", "Arn"]
    DeletionPolicy: Delete
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      MonitoringInterval: 0
    DeletionPolicy: Delete
```

## \[CT\.RDS\.PR\.3\] Require an Amazon RDS cluster to have deletion protection configured<a name="ct-rds-pr-3-description"></a>

This control checks whether your Amazon Relational Database Service \(Amazon RDS\) cluster has deletion protection activated\.
+ **Control objective: **Improve availability
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBCluster`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.3 rule specification](#ct-rds-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.3 rule specification](#ct-rds-pr-3-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.3 example templates](#ct-rds-pr-3-templates) 

**Explanation**

Enabling cluster deletion protection is an additional layer of protection against accidental database deletion or deletion by an unauthorized entity\.

When deletion protection is enabled, an Amazon RDS cluster cannot be deleted\. Before a deletion request can succeed, deletion protection must be deactivated\.

### Remediation for rule failure<a name="ct-rds-pr-3-remediation"></a>

Set the value of the `DeletionProtection` parameter to true\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Cluster \- Example<a name="ct-rds-pr-3-remediation-1"></a>

Amazon RDS DB cluster with deletion protection enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RDSDBCluster": {
        "Type": "AWS::RDS::DBCluster",
        "Properties": {
            "Engine": "aurora",
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${RDSClusterSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${RDSClusterSecret}::password}}"
            },
            "DBSubnetGroupName": {
                "Ref": "TestDBSubnetGroup"
            },
            "DeletionProtection": true
        }
    }
}
```

**YAML example**

```
RDSDBCluster:
  Type: AWS::RDS::DBCluster
  Properties:
    Engine: aurora
    MasterUsername: !Sub '{{resolve:secretsmanager:${RDSClusterSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${RDSClusterSecret}::password}}'
    DBSubnetGroupName: !Ref 'TestDBSubnetGroup'
    DeletionProtection: true
```

### CT\.RDS\.PR\.3 rule specification<a name="ct-rds-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_cluster_deletion_protection_enabled_check
# 
# Description:
#   Checks if an Amazon Relational Database Service (Amazon RDS) cluster has deletion protection enabled.
# 
# Reports on:
#   AWS::RDS::DBCluster
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#  None
# 
# Scenarios:
#   Scenario: 1
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document does not contain any RDS DB cluster resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'DeletionProtection' has not been specified
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'DeletionProtection' has been specified
#       And: 'DeletionProtection' has been set to bool(false)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'DeletionProtection' has been specified
#       And: 'DeletionProtection' has been set to bool(true)
#      Then: PASS


#
# Constants
#
let RDS_DB_CLUSTER_TYPE = "AWS::RDS::DBCluster"
let INPUT_DOCUMENT = this

#
# Assignments
#
let db_clusters = Resources.*[ Type == %RDS_DB_CLUSTER_TYPE ]

#
# Primary Rules
#
rule rds_cluster_deletion_protection_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                        %db_clusters not empty {
    check(%db_clusters.Properties)
        <<
        [CT.RDS.PR.3]: Require an Amazon RDS cluster to have deletion protection configured
        [FIX]: Set the value of the 'DeletionProtection' parameter to true.
        >>
}

rule rds_cluster_deletion_protection_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_CLUSTER_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.3]: Require an Amazon RDS cluster to have deletion protection configured
        [FIX]: Set the value of the 'DeletionProtection' parameter to true.
        >>
}

rule check(properties) {
    %properties {
        # Scenario 2
        DeletionProtection exists
        # Scenario 3 and 4
        DeletionProtection == true
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

### CT\.RDS\.PR\.3 example templates<a name="ct-rds-pr-3-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/25
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.0.128/25
      AvailabilityZone:
        Fn::Select:
        - 1
        - Fn::GetAZs: ''
      VpcId:
        Ref: VPC
  DBSubnetGroup:
    Type: AWS::RDS::DBSubnetGroup
    Properties:
      DBSubnetGroupDescription: Example DB subnet group
      SubnetIds:
      - Ref: SubnetOne
      - Ref: SubnetTwo
  RDSClusterSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "exampleuser"}'
        GenerateStringKey: password
        PasswordLength: 32
        ExcludeCharacters: "/@\""
  RDSCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      Engine: aurora-mysql
      MasterUsername:
        Fn::Sub: "{{resolve:secretsmanager:${RDSClusterSecret}::username}}"
      MasterUserPassword:
        Fn::Sub: "{{resolve:secretsmanager:${RDSClusterSecret}::password}}"
      DBSubnetGroupName:
        Ref: DBSubnetGroup
      DeletionProtection: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/25
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.0.128/25
      AvailabilityZone:
        Fn::Select:
        - 1
        - Fn::GetAZs: ''
      VpcId:
        Ref: VPC
  DBSubnetGroup:
    Type: AWS::RDS::DBSubnetGroup
    Properties:
      DBSubnetGroupDescription: Example DB subnet group
      SubnetIds:
      - Ref: SubnetOne
      - Ref: SubnetTwo
  RDSClusterSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "exampleuser"}'
        GenerateStringKey: password
        PasswordLength: 32
        ExcludeCharacters: "/@\""
  RDSCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      Engine: aurora-mysql
      MasterUsername:
        Fn::Sub: "{{resolve:secretsmanager:${RDSClusterSecret}::username}}"
      MasterUserPassword:
        Fn::Sub: "{{resolve:secretsmanager:${RDSClusterSecret}::password}}"
      DBSubnetGroupName:
        Ref: DBSubnetGroup
      DeletionProtection: false
```

## \[CT\.RDS\.PR\.4\] Require an Amazon RDS database cluster to have AWS IAM database authentication configured<a name="ct-rds-pr-4-description"></a>

This control checks whether an Amazon Relational Database Service \(RDS\) database \(DB\) cluster has AWS IAM database authentication activated\.
+ **Control objective: **Use strong authentication
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBCluster`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.4 rule specification](#ct-rds-pr-4-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.4 rule specification](#ct-rds-pr-4-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.4 example templates](#ct-rds-pr-4-templates) 

**Explanation**

IAM database authentication allows for password\-free authentication to database instances\. The authentication uses an authentication token\. Network traffic to and from the database is encrypted using SSL\.

**Usage considerations**  
This control applies only to Amazon RDS DB cluster engine types `aurora`, `aurora-mysql` and `aurora-postgresql`\.

### Remediation for rule failure<a name="ct-rds-pr-4-remediation"></a>

Set `EnableIAMDatabaseAuthentication` to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Cluster \- Example<a name="ct-rds-pr-4-remediation-1"></a>

Amazon RDS DB cluster configured with AWS IAM database authentication\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBCluster": {
        "Type": "AWS::RDS::DBCluster",
        "Properties": {
            "Engine": "aurora-mysql",
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBClusterSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBClusterSecret}::password}}"
            },
            "DBSubnetGroupName": {
                "Ref": "DBSubnetGroup"
            },
            "EnableIAMDatabaseAuthentication": true
        }
    }
}
```

**YAML example**

```
DBCluster:
  Type: AWS::RDS::DBCluster
  Properties:
    Engine: aurora-mysql
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBClusterSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
    DBSubnetGroupName: !Ref 'DBSubnetGroup'
    EnableIAMDatabaseAuthentication: true
```

### CT\.RDS\.PR\.4 rule specification<a name="ct-rds-pr-4-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_cluster_iam_authentication_enabled_check
# 
# Description:
#   This control checks whether an Amazon Relational Database Service (RDS) database (DB) cluster has AWS IAM database authentication activated.
# 
# Reports on:
#   AWS::RDS::DBCluster
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
#       And: The input document does not contain any RDS DB cluster resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'Engine' provided is not one of 'aurora' or 'aurora-mysql' or 'aurora-postgresql'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'Engine' provided is one of 'aurora' or 'aurora-mysql' or 'aurora-postgresql'
#       And: 'EnableIAMDatabaseAuthentication' has not been provided
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'Engine' provided is one of 'aurora' or 'aurora-mysql' or 'aurora-postgresql'
#       And: 'EnableIAMDatabaseAuthentication' has been provided
#       And: 'EnableIAMDatabaseAuthentication' has been set to a value other than bool(true)
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation document or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'Engine' provided is one of 'aurora' or 'aurora-mysql' or 'aurora-postgresql'
#       And: 'EnableIAMDatabaseAuthentication' has been provided
#       And: 'EnableIAMDatabaseAuthentication' has been set to bool(true)
#      Then: PASS

#
# Constants
#
let RDS_DB_CLUSTER_TYPE = "AWS::RDS::DBCluster"
let SUPPORTED_DB_CLUSTER_ENGINES = ["aurora", "aurora-mysql","aurora-postgresql"]
let INPUT_DOCUMENT = this

#
# Assignments
#
let db_clusters = Resources.*[ Type == %RDS_DB_CLUSTER_TYPE ]

#
# Primary Rules
#
rule rds_cluster_iam_authentication_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                       %db_clusters not empty {
    check(%db_clusters.Properties)
        <<
        [CT.RDS.PR.4]: Require an Amazon RDS database cluster to have AWS IAM database authentication configured
            [FIX]: Set 'EnableIAMDatabaseAuthentication' to 'true'.
        >>
}

rule rds_cluster_iam_authentication_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_CLUSTER_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.4]: Require an Amazon RDS database cluster to have AWS IAM database authentication configured
            [FIX]: Set 'EnableIAMDatabaseAuthentication' to 'true'.
        >>
}

rule check(db_cluster) {
    %db_cluster [
        # Scenario 2
        filter_engine(this)
    ] {
       # Scenario 3
       EnableIAMDatabaseAuthentication exists
       # Scenario 4 and 5
       EnableIAMDatabaseAuthentication == true
    }
}

rule filter_engine(cluster_properties) {
    %cluster_properties {
        Engine exists
        Engine in %SUPPORTED_DB_CLUSTER_ENGINES
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

### CT\.RDS\.PR\.4 example templates<a name="ct-rds-pr-4-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/25
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.0.128/25
      AvailabilityZone:
        Fn::Select:
        - 1
        - Fn::GetAZs: ''
      VpcId:
        Ref: VPC
  DBSubnetGroup:
    Type: AWS::RDS::DBSubnetGroup
    Properties:
      DBSubnetGroupDescription: DB subnet group for DBCluster
      SubnetIds:
      - Ref: SubnetOne
      - Ref: SubnetTwo
  DBClusterSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasteruser"}'
        GenerateStringKey: password
        PasswordLength: 32
        ExcludeCharacters: "/@\"'\\"
  DBCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      Engine: aurora-mysql
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
      DBSubnetGroupName:
        Ref: DBSubnetGroup
      EnableIAMDatabaseAuthentication: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/25
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.0.128/25
      AvailabilityZone:
        Fn::Select:
        - 1
        - Fn::GetAZs: ''
      VpcId:
        Ref: VPC
  DBSubnetGroup:
    Type: AWS::RDS::DBSubnetGroup
    Properties:
      DBSubnetGroupDescription: DB subnet group for DBCluster
      SubnetIds:
      - Ref: SubnetOne
      - Ref: SubnetTwo
  DBClusterSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasteruser"}'
        GenerateStringKey: password
        PasswordLength: 32
        ExcludeCharacters: "/@\"'\\"
  DBCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      Engine: aurora-mysql
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
      DBSubnetGroupName:
        Ref: DBSubnetGroup
```

## \[CT\.RDS\.PR\.5\] Require an Amazon RDS database instance to have minor version upgrades configured<a name="ct-rds-pr-5-description"></a>

This control checks whether automatic minor version upgrades are enabled for an Amazon Relational Database Service \(RDS\) database instance\.
+ **Control objective: **Manage vulnerabilities
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBInstance`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.5 rule specification](#ct-rds-pr-5-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.5 rule specification](#ct-rds-pr-5-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.5 example templates](#ct-rds-pr-5-templates) 

**Explanation**

By activating automatic minor version upgrades, you can ensure that the latest minor version updates to the relational database management system \(RDBMS\) are installed\. These upgrades might include security patches and bug fixes\. Keeping up to date with patch installation is an important step in securing systems\.

**Usage considerations**  
This control applies only to Amazon RDS DB engine types `aurora`, `aurora-mysql`, `aurora-postgresql`, `mariadb`, `mysql`, `oracle-ee`, `oracle-ee-cdb`, `oracle-se2`, `oracle-se2-cdb`, `postgres`, `sqlserver-ee`, `sqlserver-se`, `sqlserver-ex` and `sqlserver-web`\.

### Remediation for rule failure<a name="ct-rds-pr-5-remediation"></a>

Omit the `AutoMinorVersionUpgrade` property or set it to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example One<a name="ct-rds-pr-5-remediation-1"></a>

Amazon RDS DB instance configured with automatic minor version upgrades, enabled by means of AWS CloudFormation defaults\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "postgres",
            "EngineVersion": 14.2,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            }
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: postgres
    EngineVersion: 14.2
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
  DeletionPolicy: Delete
```

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example Two<a name="ct-rds-pr-5-remediation-2"></a>

Amazon RDS DB instance configured with automatic minor version upgrades, enabled by means of the `AutoMinorVersionUpgrade` property\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "postgres",
            "EngineVersion": 14.2,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            },
            "AutoMinorVersionUpgrade": true
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: postgres
    EngineVersion: 14.2
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    AutoMinorVersionUpgrade: true
  DeletionPolicy: Delete
```

### CT\.RDS\.PR\.5 rule specification<a name="ct-rds-pr-5-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_instance_automatic_minor_version_upgrade_enabled_check
# 
# Description:
#   This control checks whether automatic minor version upgrades are enabled for an Amazon Relational Database Service (RDS) database instance.
# 
# Reports on:
#   AWS::RDS::DBInstance
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
#       And: The input document does not contain any RDS DB instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is not one of 'aurora', 'aurora-mysql', 'aurora-postgresql', 'mariadb', 'mysql',
#            'oracle-ee', 'oracle-ee-cdb', 'oracle-se2', 'oracle-se2-cdb',
#            'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web',
#            'postgres'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'aurora', 'aurora-mysql', 'aurora-postgresql', 'mariadb', 'mysql',
#            'oracle-ee', 'oracle-ee-cdb', 'oracle-se2', 'oracle-se2-cdb',
#            'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web',
#            'postgres'
#       And: 'AutoMinorVersionUpgrade' has been specified
#       And: 'AutoMinorVersionUpgrade' has been set to bool(false)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'aurora', 'aurora-mysql', 'aurora-postgresql', 'mariadb', 'mysql',
#            'oracle-ee', 'oracle-ee-cdb', 'oracle-se2', 'oracle-se2-cdb',
#            'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web',
#            'postgres'
#       And: 'AutoMinorVersionUpgrade' has not been specified
#      Then: PASS
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'aurora', 'aurora-mysql', 'aurora-postgresql', 'mariadb', 'mysql',
#            'oracle-ee', 'oracle-ee-cdb', 'oracle-se2', 'oracle-se2-cdb',
#            'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web',
#            'postgres'
#       And: 'AutoMinorVersionUpgrade' has been specified
#       And: 'AutoMinorVersionUpgrade' has been set to bool(true)
#      Then: PASS

#
# Constants
#
let RDS_DB_INSTANCE_TYPE = "AWS::RDS::DBInstance"
let INPUT_DOCUMENT = this
let SUPPORTED_RDS_INSTANCE_ENGINES = [
    "aurora", "aurora-mysql", "aurora-postgresql", "mariadb", "mysql",
    "oracle-ee", "oracle-ee-cdb", "oracle-se2", "oracle-se2-cdb",
    "postgres", "sqlserver-ee", "sqlserver-se",
    "sqlserver-ex", "sqlserver-web"
]

#
# Assignments
#
let rds_db_instances = Resources.*[ Type == %RDS_DB_INSTANCE_TYPE ]

#
# Primary Rules
#
rule rds_instance_automatic_minor_version_upgrade_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                               %rds_db_instances not empty {
    check(%rds_db_instances.Properties)
        <<
        [CT.RDS.PR.5]: Require an Amazon RDS database instance to have minor version upgrades configured
        [FIX]: Omit the 'AutoMinorVersionUpgrade' property or set it to 'true'.
        >>
}

rule rds_instance_automatic_minor_version_upgrade_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.5]: Require an Amazon RDS database instance to have minor version upgrades configured
        [FIX]: Omit the 'AutoMinorVersionUpgrade' property or set it to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(rds_db_instance) {
    %rds_db_instance [ filter_engine(this) ] {
        # Scenario: 4
        AutoMinorVersionUpgrade not exists or
        # Scenario: 3 and 5
        AutoMinorVersionUpgrade == true
    }
}

rule filter_engine(db_properties) {
    %db_properties {
        # Scenario: 2
        Engine exists
        Engine is_string
        Engine in %SUPPORTED_RDS_INSTANCE_ENGINES
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

### CT\.RDS\.PR\.5 example templates<a name="ct-rds-pr-5-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Test RDS DB Instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    DeletionPolicy: Delete
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Test RDS DB Instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      AutoMinorVersionUpgrade: false
    DeletionPolicy: Delete
```

## \[CT\.RDS\.PR\.6\] Require an Amazon RDS database cluster to have backtracking configured<a name="ct-rds-pr-6-description"></a>

This control checks whether an Amazon Relational Database Service \(RDS\) database \(DB\) cluster has backtracking enabled\.
+ **Control objective: **Improve resiliency
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBCluster`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.6 rule specification](#ct-rds-pr-6-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.6 rule specification](#ct-rds-pr-6-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.6 example templates](#ct-rds-pr-6-templates) 

**Explanation**

Backups help you to recover more quickly from a security incident\. Backups also strengthen the resilience of your systems\. Aurora backtracking reduces the time required to recover a database for a specific point in time, and the recovery does not require a database restore\.

**Usage considerations**  
This control applies only to Amazon RDS DB cluster engine types `aurora` and `aurora-mysql`, and to DB cluster engine modes `provisioned` and `parallelquery`
This control does not apply to Amazon RDS DB clusters that support Aurora Serverless V2 database instances \(For example, RDS DB clusters configured with a `ServerlessV2ScalingConfiguration` and Aurora Serverless V2 compatible `EngineVersion`\.\)

### Remediation for rule failure<a name="ct-rds-pr-6-remediation"></a>

Set `BacktrackWindow` to a number between `1` and `259200`\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Cluster \- Example<a name="ct-rds-pr-6-remediation-1"></a>

Amazon RDS DB cluster configured with a backtrack window of 720 seconds \(12 minutes\)\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBCluster": {
        "Type": "AWS::RDS::DBCluster",
        "Properties": {
            "Engine": "aurora-mysql",
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBClusterSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBClusterSecret}::password}}"
            },
            "DBSubnetGroupName": {
                "Ref": "DBSubnetGroup"
            },
            "BacktrackWindow": 720
        }
    }
}
```

**YAML example**

```
DBCluster:
  Type: AWS::RDS::DBCluster
  Properties:
    Engine: aurora-mysql
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBClusterSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
    DBSubnetGroupName: !Ref 'DBSubnetGroup'
    BacktrackWindow: 720
```

### CT\.RDS\.PR\.6 rule specification<a name="ct-rds-pr-6-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   aurora_cluster_backtracking_enabled_check
# 
# Description:
#   This control checks whether an Amazon Relational Database Service (RDS) database (DB) cluster has backtracking enabled.
# 
# Reports on:
#   AWS::RDS::DBCluster
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#    Scenario: 1
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document does not contain any RDS DB cluster resources
#       Then: SKIP
#    Scenario: 2
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an RDS DB cluster resource
#        And: 'Engine' provided is not one of 'aurora' or 'aurora-mysql'
#       Then: SKIP
#    Scenario: 3
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an RDS DB cluster resource
#        And: 'Engine' provided is one of 'aurora' or 'aurora-mysql'
#        And: 'EngineMode' provided is not one of 'provisioned' or 'parallelquery'
#       Then: SKIP
#    Scenario: 4
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an RDS DB cluster resource
#        And: 'ServerlessV2ScalingConfiguration' is provided
#        And: 'Engine' provided is 'aurora-mysql'
#        And: 'EngineVersion' provided is '8.0.mysql_aurora.3.02.0' or higher
#       Then: SKIP
#    Scenario: 5
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an RDS DB cluster resource
#        And: 'Engine' provided is one of 'aurora' or 'aurora-mysql'
#        And: 'EngineMode' is not provided or 'EngineMode' provided is one of 'provisioned' or 'parallelquery'
#        And: 'BacktrackWindow' has not been provided
#       Then: FAIL
#    Scenario: 6
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an RDS DB cluster resource
#        And: 'Engine' provided is one of 'aurora' or 'aurora-mysql'
#        And: 'EngineMode' is not provided or 'EngineMode' provided is one of 'provisioned' or 'parallelquery'
#        And: 'BacktrackWindow' has been provided and is set to 0
#       Then: FAIL
#    Scenario: 7
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an RDS DB cluster resource
#        And: 'Engine' provided is one of 'aurora' or 'aurora-mysql'
#        And: 'EngineMode' is not provided or 'EngineMode' provided is one of 'provisioned' or 'parallelquery'
#        And: 'BacktrackWindow' has been provided and is set to a value > 0
#       Then: PASS

#
# Constants
#
let RDS_DB_CLUSTER_TYPE = "AWS::RDS::DBCluster"
let SUPPORTED_DB_CLUSTER_ENGINES = ["aurora", "aurora-mysql"]
let SUPPORTED_DB_CLUSTER_ENGINE_MODES = ["provisioned", "parallelquery"]
let AURORA_SERVERLESS_V2_SUPPORTED_ENGINE = ["aurora-mysql"]
let AURORA_V3_SERVERLESS_V2_NOT_SUPPORTED_PATTERN = /^8\.0\.mysql_aurora\.3\.01\./
let AURORA_V3_SERVERLESS_V2_SUPPORTED_PATTERN = /^8\.0\.mysql_aurora\.3/
let INPUT_DOCUMENT = this

#
# Assignments
#
let db_clusters = Resources.*[ Type == %RDS_DB_CLUSTER_TYPE ]

#
# Primary Rules
#
rule aurora_cluster_backtracking_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                    %db_clusters not empty {
    check(%db_clusters.Properties)
        <<
        [CT.RDS.PR.6]: Require an Amazon RDS database cluster to have backtracking configured
            [FIX]: Set 'BacktrackWindow' to a number between '1' and '259200'.
        >>
}

rule aurora_cluster_backtracking_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_CLUSTER_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.6]: Require an Amazon RDS database cluster to have backtracking configured
            [FIX]: Set 'BacktrackWindow' to a number between '1' and '259200'.
        >>
}

rule check(db_cluster) {
    %db_cluster [
        filter_engine_enginemode_and_serverless_v2(this)
    ] {
        # Scenario 5
        BacktrackWindow exists
        # Scenario 6 and 7
        BacktrackWindow > 0
    }
}

rule filter_engine_enginemode_and_serverless_v2(db_cluster) {
    # Scenario 2 and 3
    %db_cluster {
        Engine exists
        Engine in %SUPPORTED_DB_CLUSTER_ENGINES
        EngineMode not exists or
        EngineMode in %SUPPORTED_DB_CLUSTER_ENGINE_MODES
    }
    #Scenario 4
    %db_cluster [
        ServerlessV2ScalingConfiguration exists
        Engine in %AURORA_SERVERLESS_V2_SUPPORTED_ENGINE
    ] {
        EngineVersion in %AURORA_V3_SERVERLESS_V2_NOT_SUPPORTED_PATTERN or
        EngineVersion not in %AURORA_V3_SERVERLESS_V2_SUPPORTED_PATTERN
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

### CT\.RDS\.PR\.6 example templates<a name="ct-rds-pr-6-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/25
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.0.128/25
      AvailabilityZone:
        Fn::Select:
        - 1
        - Fn::GetAZs: ''
      VpcId:
        Ref: VPC
  DBSubnetGroup:
    Type: AWS::RDS::DBSubnetGroup
    Properties:
      DBSubnetGroupDescription: Example DB subnet group
      SubnetIds:
      - Ref: SubnetOne
      - Ref: SubnetTwo
  DBClusterSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasteruser"}'
        GenerateStringKey: password
        PasswordLength: 32
        ExcludeCharacters: "/@\"'\\"
  DBCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      Engine: aurora-mysql
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
      DBSubnetGroupName:
        Ref: DBSubnetGroup
      BacktrackWindow: 720
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/25
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.0.128/25
      AvailabilityZone:
        Fn::Select:
        - 1
        - Fn::GetAZs: ''
      VpcId:
        Ref: VPC
  DBSubnetGroup:
    Type: AWS::RDS::DBSubnetGroup
    Properties:
      DBSubnetGroupDescription: Example DB subnet group
      SubnetIds:
      - Ref: SubnetOne
      - Ref: SubnetTwo
  DBClusterSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasteruser"}'
        GenerateStringKey: password
        PasswordLength: 32
        ExcludeCharacters: "/@\"'\\"
  DBCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      Engine: aurora-mysql
      EngineMode: provisioned
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
      DBSubnetGroupName:
        Ref: DBSubnetGroup
```

## \[`CT.RDS.PR.7`\] Require Amazon RDS database instances to have IAM authentication configured<a name="ct-rds-pr-7-description"></a>

This control checks whether an Amazon RDS database \(DB\) instance has AWS Identity and Access Management \(IAM\) database authentication activated\.
+ **Control objective: **Use strong authentication
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBInstance`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.7 rule specification](#ct-rds-pr-7-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.7 rule specification](#ct-rds-pr-7-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.7 example templates](#ct-rds-pr-7-templates) 

**Explanation**

IAM database authentication allows authentication to database instances with an authentication token instead of a password\. Network traffic to and from the database is encrypted with SSL\.

**Usage considerations**  
This control applies only to Amazon RDS DB engine types `mariadb`, `mysql` and `postgres`\.

### Remediation for rule failure<a name="ct-rds-pr-7-remediation"></a>

Set `EnableIAMDatabaseAuthentication` to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example<a name="ct-rds-pr-7-remediation-1"></a>

Amazon RDS DB instance configured with IAM database authentication\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "postgres",
            "EngineVersion": 14.2,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            },
            "EnableIAMDatabaseAuthentication": true
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: postgres
    EngineVersion: 14.2
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    EnableIAMDatabaseAuthentication: true
  DeletionPolicy: Delete
```

### CT\.RDS\.PR\.7 rule specification<a name="ct-rds-pr-7-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_instance_iam_authentication_enabled_check
# 
# Description:
#   This control checks whether an Amazon RDS database (DB) instance has AWS Identity and Access Management (IAM) database authentication activated.
# 
# Reports on:
#   AWS::RDS::DBInstance
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
#       And: The input document does not contain any RDS DB instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is not in-scope database engines - 'mariadb', 'mysql', 'postgres'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is in-scope database engines - 'mariadb', 'mysql', 'postgres'
#       And: 'EnableIAMDatabaseAuthentication' has not been specified
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is in-scope database engines - 'mariadb', 'mysql', 'postgres'
#       And: 'EnableIAMDatabaseAuthentication' has been specified
#       And: 'EnableIAMDatabaseAuthentication' has been set to bool(false)
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is in-scope database engines - 'mariadb', 'mysql', 'postgres'
#       And: 'EnableIAMDatabaseAuthentication' has been specified
#       And: 'EnableIAMDatabaseAuthentication' has been set to bool(true)
#      Then: PASS

#
# Constants
#
let RDS_DB_INSTANCE_TYPE = "AWS::RDS::DBInstance"
let INPUT_DOCUMENT = this
let SUPPORTED_RDS_INSTANCE_ENGINES = ["mariadb", "mysql", "postgres"]

#
# Assignments
#
let rds_db_instances = Resources.*[ Type == %RDS_DB_INSTANCE_TYPE ]

#
# Primary Rules
#
rule rds_instance_iam_authentication_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                        %rds_db_instances not empty {
    check(%rds_db_instances.Properties)
        <<
        [CT.RDS.PR.7]: Require Amazon RDS database instances to have AWS IAM authentication configured
        [FIX]: Set 'EnableIAMDatabaseAuthentication' to 'true'.
        >>
}

rule rds_instance_iam_authentication_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.7]: Require Amazon RDS database instances to have AWS IAM authentication configured
        [FIX]: Set 'EnableIAMDatabaseAuthentication' to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(rds_db_instance) {
    %rds_db_instance [filter_engine(this)] {
        #Scenario: 3
        EnableIAMDatabaseAuthentication exists
        #Scenario: 4 and 5
        EnableIAMDatabaseAuthentication == true
    }
}

rule filter_engine(db_properties) {
    %db_properties {
        #Scenario: 2
        Engine exists
        Engine is_string
        Engine in %SUPPORTED_RDS_INSTANCE_ENGINES
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

### CT\.RDS\.PR\.7 example templates<a name="ct-rds-pr-7-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Test RDS DB Instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      EnableIAMDatabaseAuthentication: true
    DeletionPolicy: Delete
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Test RDS DB Instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      EnableIAMDatabaseAuthentication: false
    DeletionPolicy: Delete
```

## \[CT\.RDS\.PR\.8\] Require an Amazon RDS database instance to have automatic backups configured<a name="ct-rds-pr-8-description"></a>

This control checks whether Amazon RDS database \(DB\) instances have automated backups enabled, and verifies that the backup retention period is greater than or equal to seven \(7\) days\.
+ **Control objective: **Improve resiliency
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBInstance`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.8 rule specification](#ct-rds-pr-8-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.8 rule specification](#ct-rds-pr-8-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.8 example templates](#ct-rds-pr-8-templates) 

**Explanation**

Backups help you recover more quickly from a security incident, and they strengthen the resilience of your systems\. Amazon RDS provides an easy way to configure daily, full\-instance volume snapshots\.

**Usage considerations**  
This control applies only to Amazon RDS DB engine types `mariadb`, `mysql`, `oracle-ee`, `oracle-ee-cdb`, `oracle-se2`, `oracle-se2-cdb`, `postgres`, `sqlserver-ee`, `sqlserver-se`, `sqlserver-ex` and `sqlserver-web`\.

### Remediation for rule failure<a name="ct-rds-pr-8-remediation"></a>

Set `BackupRetentionPeriod` to an integer value between 7 and 35 days \(inclusive\)\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example<a name="ct-rds-pr-8-remediation-1"></a>

Amazon RDS DB instance configured with automated backups configured and a backup retention period of 14 days\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "postgres",
            "EngineVersion": 14.2,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            },
            "BackupRetentionPeriod": 14
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: postgres
    EngineVersion: 14.2
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    BackupRetentionPeriod: 14
  DeletionPolicy: Delete
```

### CT\.RDS\.PR\.8 rule specification<a name="ct-rds-pr-8-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_instance_backup_enabled_check
# 
# Description:
#   This control checks whether Amazon RDS database (DB) instances have automated backups enabled, and verifies that the backup retention period is greater than or equal to seven (7) days.
# 
# Reports on:
#   AWS::RDS::DBInstance
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
#       And: The input document does not contain any RDS DB instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is not one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'BackupRetentionPeriod' has been specified
#       And: 'BackupRetentionPeriod' has been set to 0 (backup disabled)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'BackupRetentionPeriod' has not been specified
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'BackupRetentionPeriod' has been specified
#       And: 'BackupRetentionPeriod' has been set to < 7
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'BackupRetentionPeriod' has been specified
#       And: 'BackupRetentionPeriod' has been set to an integer >= 7
#      Then: PASS

#
# Constants
#
let RDS_DB_INSTANCE_TYPE = "AWS::RDS::DBInstance"
let INPUT_DOCUMENT = this
let SUPPORTED_RDS_INSTANCE_ENGINES = [
    "mariadb", "mysql", "oracle-ee", "oracle-ee-cdb", "oracle-se2",
    "oracle-se2-cdb", "postgres", "sqlserver-ee", "sqlserver-se",
    "sqlserver-ex", "sqlserver-web"
]

#
# Assignments
#
let rds_db_instances = Resources.*[ Type == %RDS_DB_INSTANCE_TYPE ]

#
# Primary Rules
#
rule rds_instance_backup_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                            %rds_db_instances not empty {
    check(%rds_db_instances.Properties)
        <<
        [CT.RDS.PR.8]: Require an Amazon RDS database instance to have automatic backups configured
        [FIX]: Set 'BackupRetentionPeriod' to an integer value between 7 and 35 days (inclusive).
        >>
}

rule rds_instance_backup_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.8]: Require an Amazon RDS database instance to have automatic backups configured
        [FIX]: Set 'BackupRetentionPeriod' to an integer value between 7 and 35 days (inclusive).
        >>
}

#
# Parameterized Rules
#
rule check(rds_db_instance) {
    %rds_db_instance [ filter_db_identifier_and_engine(this) ] {
        # Scenario: 3, 4, 5 and 6
        BackupRetentionPeriod exists
        BackupRetentionPeriod >= 7
    }
}

rule filter_db_identifier_and_engine(db_properties) {
    %db_properties {
        # Scenario: 2
        Engine exists
        Engine is_string
        Engine in %SUPPORTED_RDS_INSTANCE_ENGINES
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

### CT\.RDS\.PR\.8 example templates<a name="ct-rds-pr-8-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Test RDS DB Instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      BackupRetentionPeriod: 14
    DeletionPolicy: Delete
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Test RDS DB Instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      BackupRetentionPeriod: 4
    DeletionPolicy: Delete
```

## \[CT\.RDS\.PR\.9\] Require an Amazon RDS database cluster to copy tags to snapshots<a name="ct-rds-pr-9-description"></a>

This control checks whether an Amazon RDS database \(DB\) cluster is configured to copy all tags to snapshots created\.
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBCluster`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.9 rule specification](#ct-rds-pr-9-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.9 rule specification](#ct-rds-pr-9-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.9 example templates](#ct-rds-pr-9-templates) 

**Explanation**

Identification and inventory of your infrastructure assets is a crucial aspect of governance and security\. With visibility into all your Amazon RDS DB clusters, you can assess their security posture and take action on potential areas of weakness\. We recommend that you tag snapshots in the same way as their parent RDS database clusters\. Activating this setting ensures that snapshots inherit the tags of their parent database clusters\.

**Usage considerations**  
This control applies only to Amazon RDS DB cluster engine types `aurora`, `aurora-mysql`, and `aurora-postgresql`\.

### Remediation for rule failure<a name="ct-rds-pr-9-remediation"></a>

Set `CopyTagsToSnapshot` to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Cluster \- Example<a name="ct-rds-pr-9-remediation-1"></a>

Amazon RDS DB cluster configured to copy tags to snapshots\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBCluster": {
        "Type": "AWS::RDS::DBCluster",
        "Properties": {
            "Engine": "aurora-mysql",
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBClusterSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBClusterSecret}::password}}"
            },
            "DBSubnetGroupName": {
                "Ref": "DBSubnetGroup"
            },
            "CopyTagsToSnapshot": true
        }
    }
}
```

**YAML example**

```
DBCluster:
  Type: AWS::RDS::DBCluster
  Properties:
    Engine: aurora-mysql
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBClusterSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
    DBSubnetGroupName: !Ref 'DBSubnetGroup'
    CopyTagsToSnapshot: true
```

### CT\.RDS\.PR\.9 rule specification<a name="ct-rds-pr-9-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_cluster_copy_tags_to_snapshots_enabled_check
# 
# Description:
#   This control checks whether an Amazon RDS DB cluster is configured to copy all tags to snapshots created.
# 
# Reports on:
#   AWS::RDS::DBCluster
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
#       And: The input document does not contain any RDS DB cluster resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'Engine' provided is not one of 'aurora' or 'aurora-mysql' or 'aurora-postgresql'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'Engine' provided is one of 'aurora' or 'aurora-mysql' or 'aurora-postgresql'
#       And: 'CopyTagsToSnapshot' has not been provided
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'Engine' provided is one of 'aurora' or 'aurora-mysql' or 'aurora-postgresql'
#       And: 'CopyTagsToSnapshot' has been provided
#       And: 'CopyTagsToSnapshot' has been set to a value other than bool(true)
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'Engine' provided is one of 'aurora' or 'aurora-mysql' or 'aurora-postgresql'
#       And: 'CopyTagsToSnapshot' has been provided
#       And: 'CopyTagsToSnapshot' has been set to bool(true)
#      Then: PASS

#
# Constants
#
let RDS_DB_CLUSTER_TYPE = "AWS::RDS::DBCluster"
let SUPPORTED_DB_CLUSTER_ENGINES = ["aurora", "aurora-mysql","aurora-postgresql"]
let INPUT_DOCUMENT = this

#
# Assignments
#
let db_clusters = Resources.*[ Type == %RDS_DB_CLUSTER_TYPE ]

#
# Primary Rules
#
rule rds_cluster_copy_tags_to_snapshots_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                           %db_clusters not empty {
    check(%db_clusters.Properties)
        <<
        [CT.RDS.PR.9]: Require an Amazon RDS database cluster to copy tags to snapshots
            [FIX]: Set 'CopyTagsToSnapshot' to 'true'.
        >>
}

rule rds_cluster_copy_tags_to_snapshots_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_CLUSTER_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.9]: Require an Amazon RDS database cluster to copy tags to snapshots
            [FIX]: Set 'CopyTagsToSnapshot' to 'true'.
        >>
}

rule check(db_cluster) {
    %db_cluster [
        # Scenario 2
        filter_engine(this)
    ] {
       # Scenario 3
       CopyTagsToSnapshot exists
       # Scenario 4 and 5
       CopyTagsToSnapshot == true
    }
}

rule filter_engine(cluster_properties) {
    %cluster_properties {
        Engine exists
        Engine in %SUPPORTED_DB_CLUSTER_ENGINES
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

### CT\.RDS\.PR\.9 example templates<a name="ct-rds-pr-9-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/25
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.0.128/25
      AvailabilityZone:
        Fn::Select:
        - 1
        - Fn::GetAZs: ''
      VpcId:
        Ref: VPC
  DBSubnetGroup:
    Type: AWS::RDS::DBSubnetGroup
    Properties:
      DBSubnetGroupDescription: DB subnet group for DBCluster
      SubnetIds:
      - Ref: SubnetOne
      - Ref: SubnetTwo
  DBClusterSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasteruser"}'
        GenerateStringKey: password
        PasswordLength: 32
        ExcludeCharacters: "/@\"'\\"
  DBCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      Engine: aurora-mysql
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
      DBSubnetGroupName:
        Ref: DBSubnetGroup
      CopyTagsToSnapshot: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/25
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.0.128/25
      AvailabilityZone:
        Fn::Select:
        - 1
        - Fn::GetAZs: ''
      VpcId:
        Ref: VPC
  DBSubnetGroup:
    Type: AWS::RDS::DBSubnetGroup
    Properties:
      DBSubnetGroupDescription: DB subnet group for DBCluster
      SubnetIds:
      - Ref: SubnetOne
      - Ref: SubnetTwo
  DBClusterSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasteruser"}'
        GenerateStringKey: password
        PasswordLength: 32
        ExcludeCharacters: "/@\"'\\"
  DBCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      Engine: aurora-mysql
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
      DBSubnetGroupName:
        Ref: DBSubnetGroup
```

## \[CT\.RDS\.PR\.10\] Require an Amazon RDS database instance to copy tags to snapshots<a name="ct-rds-pr-10-description"></a>

This control checks whether Amazon RDS database \(DB\) instances are configured to copy all tags to snapshots created\.
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBInstance`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.10 rule specification](#ct-rds-pr-10-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.10 rule specification](#ct-rds-pr-10-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.10 example templates](#ct-rds-pr-10-templates) 

**Explanation**

Identification and inventory of your IT assets is a crucial aspect of governance and security\. With visibility of all your RDS DB instances, you can assess their security posture and take action on potential areas of weakness\. Snapshots should be tagged to match their parent RDS database instances\. Enabling this setting ensures that snapshots inherit the tags from their parent database instances\.

**Usage considerations**  
This control applies only to Amazon RDS DB engine types `mariadb`, `mysql`, `oracle-ee`, `oracle-ee-cdb`, `oracle-se2`, `oracle-se2-cdb`, `postgres`, `sqlserver-ee`, `sqlserver-se`, `sqlserver-ex` and `sqlserver-web`\.

### Remediation for rule failure<a name="ct-rds-pr-10-remediation"></a>

Set `CopyTagsToSnapshot` to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example<a name="ct-rds-pr-10-remediation-1"></a>

Amazon RDS DB instance configured to copy all tags to snapshots created\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "postgres",
            "EngineVersion": 14.2,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            },
            "CopyTagsToSnapshot": true
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: postgres
    EngineVersion: 14.2
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    CopyTagsToSnapshot: true
  DeletionPolicy: Delete
```

### CT\.RDS\.PR\.10 rule specification<a name="ct-rds-pr-10-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_instance_copy_tags_to_snapshots_enabled_check
# 
# Description:
#   This control checks whether Amazon RDS database (DB) instances are configured to copy all tags to snapshots created.
# 
# Reports on:
#   AWS::RDS::DBInstance
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
#       And: The input document does not contain any RDS DB instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is not one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'CopyTagsToSnapshot' has not been specified
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'CopyTagsToSnapshot' has been specified
#       And: 'CopyTagsToSnapshot' has been set to bool(false)
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'CopyTagsToSnapshot' has been specified
#       And: 'CopyTagsToSnapshot' has been set to bool(true)
#      Then: PASS

#
# Constants
#
let RDS_DB_INSTANCE_TYPE = "AWS::RDS::DBInstance"
let INPUT_DOCUMENT = this
let SUPPORTED_RDS_INSTANCE_ENGINES = [
    "mariadb", "mysql", "oracle-ee", "oracle-ee-cdb", "oracle-se2",
    "oracle-se2-cdb", "postgres", "sqlserver-ee", "sqlserver-se",
    "sqlserver-ex", "sqlserver-web"
]

#
# Assignments
#
let rds_db_instances = Resources.*[ Type == %RDS_DB_INSTANCE_TYPE ]

#
# Primary Rules
#
rule rds_instance_copy_tags_to_snapshots_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                      %rds_db_instances not empty {
    check(%rds_db_instances.Properties)
        <<
        [CT.RDS.PR.10]: Require an Amazon RDS database instance to copy tags to snapshots
        [FIX]: Set 'CopyTagsToSnapshot' to 'true'.
        >>
}

rule rds_instance_copy_tags_to_snapshots_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.10]: Require an Amazon RDS database instance to copy tags to snapshots
        [FIX]: Set 'CopyTagsToSnapshot' to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(rds_db_instance) {
    %rds_db_instance [filter_engine(this)] {
       # Scenario: 3
       CopyTagsToSnapshot exists
       # Scenario: 4 and 5
       CopyTagsToSnapshot == true
    }
}

rule filter_engine(db_properties) {
    %db_properties {
        # Scenario: 2
        Engine exists
        Engine is_string
        Engine in %SUPPORTED_RDS_INSTANCE_ENGINES
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

### CT\.RDS\.PR\.10 example templates<a name="ct-rds-pr-10-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Test RDS DB Instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      CopyTagsToSnapshot: true
    DeletionPolicy: Delete
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Test RDS DB Instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      CopyTagsToSnapshot: false
    DeletionPolicy: Delete
```

## \[CT\.RDS\.PR\.11\] Require an Amazon RDS database instance to have a VPC configuration<a name="ct-rds-pr-11-description"></a>

This control checks whether an Amazon RDS database \(DB\) instance is deployed in a VPC \(that is, with an EC2\-VPC instance\)\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBInstance`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.11 rule specification](#ct-rds-pr-11-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.11 rule specification](#ct-rds-pr-11-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.11 example templates](#ct-rds-pr-11-templates) 

**Explanation**

Amazon Virtual Private Cloud \(Amazon VPC\) provides a number of network controls to create secure access to RDS resources\. These controls include VPC endpoints, network ACLs, and security groups\. To take advantage of these controls, create your Amazon RDS instances as EC2 VPC instances\.

**Usage considerations**  
This control applies only to Amazon RDS DB engine types `mariadb`, `mysql`, `oracle-ee`, `oracle-ee-cdb`, `oracle-se2`, `oracle-se2-cdb`, `postgres`, `sqlserver-ee`, `sqlserver-se`, `sqlserver-ex` and `sqlserver-web`\.

### Remediation for rule failure<a name="ct-rds-pr-11-remediation"></a>

Set a `DBSubnetGroupName`\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example<a name="ct-rds-pr-11-remediation-1"></a>

Amazon RDS DB instance configured to deploy in an Amazon VPC with an RDS DB subnet group\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "postgres",
            "EngineVersion": 14.2,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            },
            "DBSubnetGroupName": {
                "Ref": "DBSubnetGroup"
            }
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: postgres
    EngineVersion: 14.2
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    DBSubnetGroupName: !Ref 'DBSubnetGroup'
  DeletionPolicy: Delete
```

### CT\.RDS\.PR\.11 rule specification<a name="ct-rds-pr-11-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_instance_deployed_in_vpc_check
# 
# Description:
#   This control checks whether an Amazon RDS database (DB) instance is deployed in a VPC (that is, an EC2 VPC instance).
# 
# Reports on:
#   AWS::RDS::DBInstance
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
#       And: The input document does not contain any RDS DB instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is not one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#            'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#            'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'DBSubnetGroupName' has not been specified
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#            'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'DBSubnetGroupName' has been specified but is an empty string
#            or invalid local reference to a DB Subnet Group
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#            'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'DBSubnetGroupName' has been specified but is a non-empty string
#            or valid local reference to a DB Subnet Group
#      Then: PASS

#
# Constants
#
let RDS_DB_INSTANCE_TYPE = "AWS::RDS::DBInstance"
let SUPPORTED_RDS_INSTANCE_ENGINES = [
    "mariadb", "mysql", "oracle-ee", "oracle-ee-cdb", "oracle-se2",
    "oracle-se2-cdb", "postgres", "sqlserver-ee", "sqlserver-se",
    "sqlserver-ex", "sqlserver-web"
]
let INPUT_DOCUMENT = this

#
# Assignments
#
let rds_db_instances = Resources.*[ Type == %RDS_DB_INSTANCE_TYPE ]

#
# Primary Rules
#
rule rds_instance_deployed_in_vpc_check when is_cfn_template(%INPUT_DOCUMENT)
                                             %rds_db_instances not empty {
    check(%rds_db_instances.Properties)
        <<
        [CT.RDS.PR.11]: Require an Amazon RDS database instance to have a VPC configuration
        [FIX]: Set a 'DBSubnetGroupName'.
        >>
}

rule rds_instance_deployed_in_vpc_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.11]: Require an Amazon RDS database instance to have a VPC configuration
        [FIX]: Set a 'DBSubnetGroupName'.
        >>
}

#
# Parameterized Rules
#
rule check(rds_db_instance) {
    %rds_db_instance [filter_engine(this)] {
       # Scenario: 3
       DBSubnetGroupName exists

       # Scenario: 4 and 5
       check_db_subnet_group(DBSubnetGroupName)
    }
}

rule filter_engine(db_properties) {
    %db_properties {
        # Scenario: 2
        Engine exists
        Engine is_string
        Engine in %SUPPORTED_RDS_INSTANCE_ENGINES
    }
}

rule check_db_subnet_group(db_subnet_group) {
   %db_subnet_group {
      check_is_string_and_not_empty(this) or
      check_local_references(%INPUT_DOCUMENT, this, "AWS::RDS::DBSubnetGroup")
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

### CT\.RDS\.PR\.11 example templates<a name="ct-rds-pr-11-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.96.0/19
      AvailabilityZone:
        Fn::Select:
        - '0'
        - Fn::GetAZs: {Ref: 'AWS::Region'}
      VpcId:
        Ref: VPC
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.128.0/19
      AvailabilityZone:
        Fn::Select:
        - '1'
        - Fn::GetAZs: {Ref: 'AWS::Region'}
      VpcId:
        Ref: VPC
  DBSubnetGroup:
    Type: AWS::RDS::DBSubnetGroup
    Properties:
      DBSubnetGroupDescription: Test DB subnet group
      SubnetIds:
      - Ref: SubnetOne
      - Ref: SubnetTwo
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Test RDS DB Instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      DBSubnetGroupName:
        Ref: DBSubnetGroup
    DeletionPolicy: Delete
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Test RDS DB Instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    DeletionPolicy: Delete
```

## \[CT\.RDS\.PR\.12\] Require an Amazon RDS event subscription to have critical cluster events configured<a name="ct-rds-pr-12-description"></a>

This control checks whether your Amazon RDS event subscriptions for RDS clusters are configured to notify on event categories of `maintenance` and `failure.`
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::EventSubscription`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.12 rule specification](#ct-rds-pr-12-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.12 rule specification](#ct-rds-pr-12-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.12 example templates](#ct-rds-pr-12-templates) 

**Explanation**

Amazon RDS event notifications uses Amazon SNS to make you aware of changes in the availability or configuration of your RDS resources\. These notifications allow for rapid response\.

**Usage considerations**  
This control applies only to Amazon RDS event subscriptions for RDS clusters \(`SourceType` of `db-cluster`\)\.

### Remediation for rule failure<a name="ct-rds-pr-12-remediation"></a>

When `SourceType` is set to `db-cluster`, set `Enabled` to true and ensure that `EventCategories` contains both `maintenance` and `failure` values\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS Event Subscription \- Example One<a name="ct-rds-pr-12-remediation-1"></a>

Amazon RDS event subscription for RDS clusters configured to notify on all event categories\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RDSEventSubscription": {
        "Type": "AWS::RDS::EventSubscription",
        "Properties": {
            "SnsTopicArn": {
                "Ref": "SnsTopic"
            },
            "SourceType": "db-cluster",
            "Enabled": true
        }
    }
}
```

**YAML example**

```
RDSEventSubscription:
  Type: AWS::RDS::EventSubscription
  Properties:
    SnsTopicArn: !Ref 'SnsTopic'
    SourceType: db-cluster
    Enabled: true
```

The examples that follow show how to implement this remediation\.

#### Amazon RDS Event Subscription \- Example Two<a name="ct-rds-pr-12-remediation-2"></a>

Amazon RDS event subscription for RDS clusters configured to notify on `maintenance` and `failure` event categories\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RDSEventSubscription": {
        "Type": "AWS::RDS::EventSubscription",
        "Properties": {
            "SnsTopicArn": {
                "Ref": "SnsTopic"
            },
            "EventCategories": [
                "maintenance",
                "failure"
            ],
            "SourceType": "db-cluster",
            "Enabled": true
        }
    }
}
```

**YAML example**

```
RDSEventSubscription:
  Type: AWS::RDS::EventSubscription
  Properties:
    SnsTopicArn: !Ref 'SnsTopic'
    EventCategories:
      - maintenance
      - failure
    SourceType: db-cluster
    Enabled: true
```

### CT\.RDS\.PR\.12 rule specification<a name="ct-rds-pr-12-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_cluster_event_notifications_configured_check
# 
# Description:
#   Checks whether an Amazon RDS event subscriptions for RDS clusters is configured to notify on event categories of "maintenance" and "failure".
# 
# Reports on:
#   AWS::RDS::EventSubscription
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
#       And: The input document does not contain any Amazon RDS event subscription resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription resource
#       And: 'SourceType' is provided and is not 'db-cluster'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription resource
#       And: 'SourceType' is 'db-cluster'
#       And: 'Enabled' is not provided or set to bool(false)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription resource
#       And: 'SourceType' is provided and is 'db-cluster'
#       And: 'Enabled' is provided and set to bool(true)
#       And: 'EventCategories' does not contain both 'maintenance' and 'failure'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription resource
#       And: 'SourceType' is provided and is 'db-cluster'
#       And: 'Enabled' is provided and set to bool(true)
#       And: 'EventCategories' does not exist or is an empty list
#      Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription resource
#       And: 'SourceType' is provided and is 'db-cluster'
#       And: 'Enabled' is provided and set to bool(true)
#       And: 'EventCategories' contains both 'maintenance' and 'failure'
#      Then: PASS

#
# Constants
#
let RDS_EVENTSUBSCRIPTION_TYPE = "AWS::RDS::EventSubscription"
let INPUT_DOCUMENT = this
let EVENT_CATEGORIES = ["maintenance","failure"]
let EVENT_SOURCE_TYPE = "db-cluster"

#
# Assignments
#
let rds_event_subscriptions = Resources.*[ Type == %RDS_EVENTSUBSCRIPTION_TYPE ]

#
# Primary Rules
#
rule rds_cluster_event_notifications_configured_check when is_cfn_template(%INPUT_DOCUMENT)
                                                           %rds_event_subscriptions not empty {
    check(%rds_event_subscriptions.Properties)
        <<
        [CT.RDS.PR.12]: Require an Amazon RDS event subscription to have critical cluster events configured
        [FIX]: When 'SourceType' is set to 'db-cluster', set 'Enabled' to true and ensure that 'EventCategories' contains both 'maintenance' and 'failure' values.
        >>
}

rule rds_cluster_event_notifications_configured_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_EVENTSUBSCRIPTION_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_EVENTSUBSCRIPTION_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.12]: Require an Amazon RDS event subscription to have critical cluster events configured
        [FIX]: When 'SourceType' is set to 'db-cluster', set 'Enabled' to true and ensure that 'EventCategories' contains both 'maintenance' and 'failure' values.
        >>
}

#
# Parameterized Rules
#
rule check(resource) {
    %resource [ SourceType == %EVENT_SOURCE_TYPE ] {
        Enabled exists
        # Scenario 4
        Enabled == true
        # Scenario 5
        EventCategories not exists or
        # Scenario 6
        check_event_categories_for_required_events(EventCategories)
    }
}

rule check_event_categories_for_required_events(event_categories) {
    %event_categories {
        this exists
        this is_list
        this empty or
        %EVENT_CATEGORIES.* in this
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

### CT\.RDS\.PR\.12 example templates<a name="ct-rds-pr-12-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  SNSTopic:
    Type: AWS::SNS::Topic
    Properties: {}
  RDSEventSubscription:
    Type: AWS::RDS::EventSubscription
    Properties:
      SnsTopicArn:
        Ref: SNSTopic
      SourceType: db-cluster
      Enabled: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  SNSTopic:
    Type: AWS::SNS::Topic
    Properties: {}
  RDSEventSubscription:
    Type: AWS::RDS::EventSubscription
    Properties:
      SnsTopicArn:
        Ref: SNSTopic
      EventCategories:
      - maintenance
      - deletion
      SourceType: db-cluster
      Enabled: true
```

## \[CT\.RDS\.PR\.13\] Require any Amazon RDS instance to have deletion protection configured<a name="ct-rds-pr-13-description"></a>

This control checks whether an Amazon Relational Database Service \(Amazon RDS\) instance has deletion protection activated\.
+ **Control objective: **Improve availability
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBInstance`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.13 rule specification](#ct-rds-pr-13-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.13 rule specification](#ct-rds-pr-13-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.13 example templates](#ct-rds-pr-13-templates) 

**Explanation**

When active, instance deletion protection provides an additional layer of protection against accidental database deletion, or deletion by an unauthorized entity\.

While deletion protection is active, an RDS DB instance cannot be deleted\. Before a deletion request can succeed, deletion protection must be turned off\.

**Usage considerations**  
This control applies only to Amazon RDS DB engine types `mariadb`, `mysql`, `oracle-ee`, `oracle-ee-cdb`, `oracle-se2`, `oracle-se2-cdb`, `postgres`, `sqlserver-ee`, `sqlserver-se`, `sqlserver-ex` and `sqlserver-web`\.

### Remediation for rule failure<a name="ct-rds-pr-13-remediation"></a>

Set `DeletionProtection` to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB instance \- Example<a name="ct-rds-pr-13-remediation-1"></a>

Amazon RDS DB instance configured with deletion protection active\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "postgres",
            "EngineVersion": 5.7,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            },
            "StorageEncrypted": true,
            "DeletionProtection": true
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: postgres
    EngineVersion: 5.7
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    StorageEncrypted: true
    DeletionProtection: true
  DeletionPolicy: Delete
```

### CT\.RDS\.PR\.13 rule specification<a name="ct-rds-pr-13-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_instance_deletion_protection_enabled_check
# 
# Description:
#   This control checks whether an Amazon Relational Database Service (Amazon RDS) instance has deletion protection activated.
# 
# Reports on:
#   AWS::RDS::DBInstance
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
#       And: The input document does not contain any RDS DB instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is not one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'DeletionProtection' has not been specified
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'DeletionProtection' has been specified
#       And: 'DeletionProtection' has been set to bool(false)
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'DeletionProtection' has been specified
#       And: 'DeletionProtection' has been set to bool(true)
#      Then: PASS

#
# Constants
#
let RDS_DB_INSTANCE_TYPE = "AWS::RDS::DBInstance"
let INPUT_DOCUMENT = this
let SUPPORTED_RDS_INSTANCE_ENGINES = [
    "mariadb", "mysql", "oracle-ee", "oracle-ee-cdb", "oracle-se2",
    "oracle-se2-cdb", "postgres", "sqlserver-ee", "sqlserver-se",
    "sqlserver-ex", "sqlserver-web"
]

#
# Assignments
#
let rds_db_instances = Resources.*[ Type == %RDS_DB_INSTANCE_TYPE ]

#
# Primary Rules
#
rule rds_instance_deletion_protection_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                                         %rds_db_instances not empty {
    check(%rds_db_instances.Properties)
        <<
        [CT.RDS.PR.13]: Require any Amazon RDS instance to have deletion protection configured
        [FIX]: Set 'DeletionProtection' to 'true'.
        >>
}

rule rds_instance_deletion_protection_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.13]: Require any Amazon RDS instance to have deletion protection configured
        [FIX]: Set 'DeletionProtection' to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(rds_db_instance) {
    %rds_db_instance [filter_engine(this)] {
       #Scenario: 3
       DeletionProtection exists
       #Scenario: 4 and 5
       DeletionProtection == true
    }
}

rule filter_engine(db_properties) {
    %db_properties {
        #Scenario: 2
        Engine exists
        Engine is_string
        Engine in %SUPPORTED_RDS_INSTANCE_ENGINES
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

### CT\.RDS\.PR\.13 example templates<a name="ct-rds-pr-13-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB Instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      DeletionProtection: true
    DeletionPolicy: Delete
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB Instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      DeletionProtection: false
    DeletionPolicy: Delete
```

## \[CT\.RDS\.PR\.14\] Require an Amazon RDS database instance to have logging configured<a name="ct-rds-pr-14-description"></a>

This rules checks whether Amazon Relational Database Service \(RDS\) instances have all available log types configured for export to Amazon CloudWatch Logs\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBInstance`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.14 rule specification](#ct-rds-pr-14-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.14 rule specification](#ct-rds-pr-14-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.14 example templates](#ct-rds-pr-14-templates) 

**Explanation**

We recommend that you enable relevant logs for all Amazon RDS databases\. Database logging provides detailed records of requests made to RDS\. Database logs can assist with security and access audits, and they can help you diagnose availability issues\.

**Usage considerations**  
This control applies only to Amazon RDS DB engine types `mariadb`, `mysql`, `postgres`, `sqlserver-ee`, `sqlserver-se`, `sqlserver-ex`, `sqlserver-web`, `oracle-ee`, `oracle-se2`, `oracle-se1`, and `oracle-se`\.

### Remediation for rule failure<a name="ct-rds-pr-14-remediation"></a>

Specify `EnableCloudwatchLogsExports` with a list of all supported log types for the Amazon RDS database instance engine\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example One<a name="ct-rds-pr-14-remediation-1"></a>

Amazon RDS DB instance configured with an engine type of `mysql` and all supported log types, for the `mysql` engine type\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "mysql",
            "EngineVersion": 5.7,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            },
            "StorageEncrypted": true,
            "EnableCloudwatchLogsExports": [
                "error",
                "general",
                "slowquery",
                "audit"
            ]
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: mysql
    EngineVersion: 5.7
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    StorageEncrypted: true
    EnableCloudwatchLogsExports:
      - error
      - general
      - slowquery
      - audit
  DeletionPolicy: Delete
```

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example Two<a name="ct-rds-pr-14-remediation-2"></a>

Amazon RDS DB instance configured with an engine type of `postgres` and all supported log types, for the `postgres` engine type\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "postgres",
            "EngineVersion": 14.2,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            },
            "EnableCloudwatchLogsExports": [
                "postgresql",
                "upgrade"
            ]
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: postgres
    EngineVersion: 14.2
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    EnableCloudwatchLogsExports:
      - postgresql
      - upgrade
  DeletionPolicy: Delete
```

### CT\.RDS\.PR\.14 rule specification<a name="ct-rds-pr-14-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_instance_logging_enabled_check
# 
# Description:
#   This rules checks whether Amazon Relational Database Service (RDS) instances have all available log types configured for export to Amazon CloudWatch Logs.
# 
# Reports on:
#   AWS::RDS::DBInstance
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
#       And: The input document does not contain any RDS DB instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is not one of 'mariadb', 'mysql',
#            'oracle-ee', 'oracle-se2', 'oracle-ee-cdb', 'oracle-se2-cdb',
#            'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql',
#            'oracle-ee', 'oracle-se2', 'oracle-ee-cdb', 'oracle-se2-cdb',
#            'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'EnableCloudwatchLogsExports' has not been specified or has been specified
#             and is an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql',
#            'oracle-ee', 'oracle-se2', 'oracle-ee-cdb', 'oracle-se2-cdb',
#            'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'EnableCloudwatchLogsExports' has been specified and is a non-empty list
#       And: One or more log types in 'EnableCloudwatchLogsExports' are not supported by the specified 'Engine'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql',
#            'oracle-ee', 'oracle-se2', 'oracle-ee-cdb', 'oracle-se2-cdb',
#            'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'EnableCloudwatchLogsExports' has been specified and is a non-empty list
#       And: 'EnableCloudwatchLogsExports' does not contain all log types supported by the specified 'Engine'
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine'  is one of 'mariadb', 'mysql'
#       And: 'EnableCloudwatchLogsExports' has been specified
#       And: 'EnableCloudwatchLogsExports' value is a non-empty and all supported log types
#             are enabled - 'audit', 'error', 'general', 'slowquery'
#      Then: PASS
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is 'postgres'
#       And: 'EnableCloudwatchLogsExports' has been specified
#       And: 'EnableCloudwatchLogsExports' value is a non-empty and all supported log types
#             are enabled - 'postgresql', 'upgrade'
#      Then: PASS
#   Scenario: 8
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex',
#            'sqlserver-web'
#       And: 'EnableCloudwatchLogsExports' has been specified
#       And: 'EnableCloudwatchLogsExports' value is a non-empty and all supported log types
#             are enabled - 'agent', 'error'
#      Then: PASS
#   Scenario: 9
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'oracle-ee', 'oracle-se2', 'oracle-ee-cdb', 'oracle-se2-cdb',
#       And: 'EnableCloudwatchLogsExports' has been specified
#       And: 'EnableCloudwatchLogsExports' value is a non-empty and all supported log types
#             are enabled - 'alert', 'audit', 'listener', 'oemagent', 'trace'
#      Then: PASS

#
# Constants
#
let RDS_DB_INSTANCE_TYPE = "AWS::RDS::DBInstance"
let INPUT_DOCUMENT = this
let SUPPORTED_RDS_INSTANCE_ENGINES = [
    "mariadb", "mysql", "oracle-ee", "oracle-ee-cdb", "oracle-se2",
    "oracle-se2-cdb", "postgres", "sqlserver-ee", "sqlserver-se",
    "sqlserver-ex", "sqlserver-web"
]

let MYSQL_OR_MARIA_ENGINES_SUBTYPES = [ "mariadb", "mysql" ]
let POSTGRES_ENGINES_SUBTYPES = [ "postgres" ]
let SQLSERVER_ENGINES_SUBTYPES = [ "sqlserver-ee", "sqlserver-se", "sqlserver-ex", "sqlserver-web" ]
let ORACLE_ENGINES_SUBTYPES = [ "oracle-ee", "oracle-se2", "oracle-se1", "oracle-se" ]

let MYSQL_OR_MARIA_SUPPORTED_LOG_TYPES = [ "audit", "error", "general", "slowquery" ]
let POSTGRES_SUPPORTED_LOG_TYPES = [ "postgresql", "upgrade" ]
let SQLSERVER_SUPPORTED_LOG_TYPES = [ "agent", "error" ]
let ORACLE_SUPPORTED_LOG_TYPES = [ "alert", "audit", "listener", "oemagent", "trace" ]

#
# Assignments
#
let rds_db_instances = Resources.*[ Type == %RDS_DB_INSTANCE_TYPE ]

#
# Primary Rules
#
rule rds_instance_logging_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                             %rds_db_instances not empty {
    check(%rds_db_instances.Properties)
        <<
        [CT.RDS.PR.14]: Require an Amazon RDS database instance to have logging configured
            [FIX]: Specify 'EnableCloudwatchLogsExports' with a list of all supported log types for the Amazon RDS database instance engine.
        >>
}

rule rds_instance_logging_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.14]: Require an Amazon RDS database instance to have logging configured
            [FIX]: Specify 'EnableCloudwatchLogsExports' with a list of all supported log types for the Amazon RDS database instance engine.
        >>
}

#
# Parameterized Rules
#
rule check(rds_db_instance) {
    %rds_db_instance [filter_engine(this)] {
        # Scenario: 3
        EnableCloudwatchLogsExports exists
        check_is_list_and_not_empty(EnableCloudwatchLogsExports)

        # Scenario: 4 and 5
        when Engine IN %MYSQL_OR_MARIA_ENGINES_SUBTYPES {
            %MYSQL_OR_MARIA_SUPPORTED_LOG_TYPES.* IN EnableCloudwatchLogsExports[*]
            EnableCloudwatchLogsExports.* IN %MYSQL_OR_MARIA_SUPPORTED_LOG_TYPES[*]
        }

        # Scenario: 4 and 6
        when Engine IN %POSTGRES_ENGINES_SUBTYPES {
            %POSTGRES_SUPPORTED_LOG_TYPES.* IN EnableCloudwatchLogsExports[*]
            EnableCloudwatchLogsExports.* IN %POSTGRES_SUPPORTED_LOG_TYPES[*]
        }

        # Scenario: 4 and 7
        when Engine IN %SQLSERVER_ENGINES_SUBTYPES {
            %SQLSERVER_SUPPORTED_LOG_TYPES.* in EnableCloudwatchLogsExports[*]
            EnableCloudwatchLogsExports.* IN %SQLSERVER_SUPPORTED_LOG_TYPES[*]
        }

        # Scenario: 4 and 8
        when Engine IN %ORACLE_ENGINES_SUBTYPES {
            %ORACLE_SUPPORTED_LOG_TYPES.* in EnableCloudwatchLogsExports[*]
            EnableCloudwatchLogsExports.* IN %ORACLE_SUPPORTED_LOG_TYPES[*]
        }
    }
}

rule filter_engine(db_properties) {
    %db_properties {
        # Scenario: 2
        Engine exists
        Engine in %SUPPORTED_RDS_INSTANCE_ENGINES
    }
}

#
# Utility Rules
#
rule check_is_list_and_not_empty(value) {
    %value {
        this is_list
        this not empty
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

### CT\.RDS\.PR\.14 example templates<a name="ct-rds-pr-14-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasteruser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: mysql
      EngineVersion: 5.7
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      StorageEncrypted: true
      EnableCloudwatchLogsExports:
      - error
      - general
      - slowquery
      - audit
    DeletionPolicy: Delete
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasteruser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: mysql
      EngineVersion: 5.7
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      StorageEncrypted: true
      EnableCloudwatchLogsExports:
      - error
      - general
      - slowquery
    DeletionPolicy: Delete
```

## \[CT\.RDS\.PR\.15\] Require that an Amazon RDS instance does not create DB security groups<a name="ct-rds-pr-15-description"></a>

This control checks whether any Amazon Relational Database Service \(RDS\) database \(DB\) security groups are created by, or associated to, an RDS DB instance, because DB security groups are intended for the EC2\-Classic platform only\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBInstance`, `AWS::RDS::DBSecurityGroup`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.15 rule specification](#ct-rds-pr-15-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.15 rule specification](#ct-rds-pr-15-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.15 example templates](#ct-rds-pr-15-templates) 

**Explanation**

We recommend that all Amazon Relational Database Service \(RDS\) databases use Amazon VPC security groups to secure their access\. Amazon DB security groups are for the EC2\-Classic platform only, and they are not recommended for use\.

### Remediation for rule failure<a name="ct-rds-pr-15-remediation"></a>

Omit the `DBSecurityGroups` property\. Instead, configure Amazon VPC security groups by means of the `VPCSecurityGroups` property\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example<a name="ct-rds-pr-15-remediation-1"></a>

Amazon RDS DB instance configured with an Amazon VPC security group\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "mysql",
            "EngineVersion": 5.7,
            "DBInstanceClass": "db.t3.small",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            },
            "StorageEncrypted": true,
            "Port": 6733,
            "DBSubnetGroupName": {
                "Ref": "DBSubnetGroup"
            },
            "VPCSecurityGroups": [
                {
                    "Ref": "SecurityGroup"
                }
            ]
        }
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: mysql
    EngineVersion: 5.7
    DBInstanceClass: db.t3.small
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    StorageEncrypted: true
    Port: 6733
    DBSubnetGroupName: !Ref 'DBSubnetGroup'
    VPCSecurityGroups:
      - !Ref 'SecurityGroup'
```

### CT\.RDS\.PR\.15 rule specification<a name="ct-rds-pr-15-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_db_security_group_not_allowed_check
# 
# Description:
#   This control checks whether any Amazon Relational Database Service (RDS) database (DB) security groups are created by, or associated to, an RDS DB instance, because DB security groups are intended for the EC2-Classic platform only.
# 
# Reports on:
#   AWS::RDS::DBSecurityGroup, AWS::RDS::DBInstance
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document does not contain any DB security group resources
#        And: The input document does not contain any RDS DB instance resources
#       Then: SKIP
#   Scenario: 2
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains a DB security group resource
#       Then: FAIL
#   Scenario: 3
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document does not contain any DBsecurity group resources
#        And: The input document contains an RDS DB instance resource
#        And: 'DBSecurityGroups' has been specified on the RDS DB instance as a non empty list
#       Then: FAIL
#   Scenario: 4
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document does not contain any DB security group resources
#        And: The input document contains an RDS DB instance resource
#        And: 'DBSecurityGroups' has not been specified on the RDS DB instance or specified as an empty list
#       Then: PASS

#
# Constants
#
let DB_INSTANCE_TYPE = "AWS::RDS::DBInstance"
let DB_SECURITY_GROUP_TYPE = "AWS::RDS::DBSecurityGroup"
let INPUT_DOCUMENT = this

#
# Assignments
#
let db_instances = Resources.*[ Type == %DB_INSTANCE_TYPE ]
let db_security_groups = Resources.*[ Type == %DB_SECURITY_GROUP_TYPE ]

#
# Primary Rules
#
rule rds_db_security_group_not_allowed_check when is_cfn_template(this)
                                                  %db_security_groups not empty {
    check_db_security_group(%db_security_groups)
        <<
        [CT.RDS.PR.15]: Require that an Amazon RDS instance does not create DB security groups
            [FIX]: Omit the 'DBSecurityGroups' property. Instead, configure Amazon VPC security groups by means of the 'VPCSecurityGroups' property.
        >>
}

rule rds_db_security_group_not_allowed_check when is_cfn_template(this)
                                                  %db_instances not empty {
    check_db_instance(%db_instances.Properties)
        <<
        [CT.RDS.PR.15]: Require that an Amazon RDS instance does not create DB security groups
            [FIX]: Omit the 'DBSecurityGroups' property. Instead, configure Amazon VPC security groups by means of the 'VPCSecurityGroups' property.
        >>
}

rule rds_db_security_group_not_allowed_check when is_cfn_hook(%INPUT_DOCUMENT, %DB_SECURITY_GROUP_TYPE) {
    check_db_security_group(%INPUT_DOCUMENT.%DB_SECURITY_GROUP_TYPE)
        <<
        [CT.RDS.PR.15]: Require that an Amazon RDS instance does not create DB security groups
            [FIX]: Omit the 'DBSecurityGroups' property. Instead, configure Amazon VPC security groups by means of the 'VPCSecurityGroups' property.
        >>
}

rule rds_db_security_group_not_allowed_check when is_cfn_hook(%INPUT_DOCUMENT, %DB_INSTANCE_TYPE) {
    check_db_instance(%INPUT_DOCUMENT.%DB_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.15]: Require that an Amazon RDS instance does not create DB security groups
            [FIX]: Omit the 'DBSecurityGroups' property. Instead, configure Amazon VPC security groups by means of the 'VPCSecurityGroups' property.
        >>
}

#
# Parameterized Rules
#
rule check_db_security_group(db_security_group) {
    # Scenario 2
    %db_security_group empty
}

rule check_db_instance(db_instance) {
    %db_instance {
        # Scenario 3 and 4
        DBSecurityGroups not exists or
        check_is_empty_list(this)
    }
}

rule check_is_empty_list(db_instance_configuration) {
    %db_instance_configuration {
        DBSecurityGroups is_list
        DBSecurityGroups empty
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

### CT\.RDS\.PR\.15 example templates<a name="ct-rds-pr-15-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.96.0/19
      AvailabilityZone:
        Fn::Select:
        - '0'
        - Fn::GetAZs: {Ref: 'AWS::Region'}
      VpcId:
        Ref: VPC
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.128.0/19
      AvailabilityZone:
        Fn::Select:
        - '1'
        - Fn::GetAZs: {Ref: 'AWS::Region'}
      VpcId:
        Ref: VPC
  DBSubnetGroup:
    Type: AWS::RDS::DBSubnetGroup
    Properties:
      DBSubnetGroupDescription: DB subnet group
      SubnetIds:
      - Ref: SubnetOne
      - Ref: SubnetTwo
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasterusername"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  SecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Example Security Group
      VpcId:
        Ref: VPC
      SecurityGroupIngress:
      - IpProtocol: tcp
        FromPort: 6733
        ToPort: 6733
        CidrIp: 10.0.0.0/16
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: mysql
      EngineVersion: 5.7
      DBInstanceClass: db.t3.small
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      StorageEncrypted: true
      Port: 6733
      DBSubnetGroupName:
        Ref: DBSubnetGroup
      VPCSecurityGroups:
      - Ref: SecurityGroup
    DeletionPolicy: Delete
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBSecurityGroup:
    Type: AWS::RDS::DBSecurityGroup
    Properties:
      DBSecurityGroupIngress:
      - CIDRIP: "0.0.0.0/0"
      GroupDescription: "Ingress for Amazon EC2 security group"
```

## \[CT\.RDS\.PR\.16\] Require an Amazon RDS database cluster to have encryption at rest configured<a name="ct-rds-pr-16-description"></a>

This control checks whether the storage encryption is configured on Amazon Relational Database Service \(RDS\) database \(DB\) clusters that are not being restored from an existing cluster\.
+ **Control objective: **Encrypt data at rest
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBCluster`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.16 rule specification](#ct-rds-pr-16-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.16 rule specification](#ct-rds-pr-16-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.16 example templates](#ct-rds-pr-16-templates) 

**Explanation**

We recommend that you configure your Amazon RDS DB clusters to be encrypted at rest, to give an added layer of security for your sensitive data\. To encrypt your RDS DB clusters and snapshots at rest, enable the encryption option for your RDS DB clusters\. Data that is encrypted at rest includes the underlying storage for DB clusters, its automated backups, read replicas, and snapshots\.

Encrypted RDS DB clusters use the open standard AES\-256 encryption algorithm to encrypt your data on the server that hosts the clusters\. After your data is encrypted, Amazon RDS handles authentication of access and decryption of your data with a minimal impact on performance\. You do not need to modify your database client applications to use encryption\.

**Usage considerations**  
This control applies only to Amazon RDS DB clusters that are not being restored from an existing cluster or created as a read replica\. \(For example, when `SourceDBClusterIdentifier` or `ReplicationSourceIdentifier` properties have been provided\.\)

### Remediation for rule failure<a name="ct-rds-pr-16-remediation"></a>

Set `StorageEncrypted` to `true`\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Cluster \- Example<a name="ct-rds-pr-16-remediation-1"></a>

Amazon RDS DB cluster configured with storage encryption enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBCluster": {
        "Type": "AWS::RDS::DBCluster",
        "Properties": {
            "Engine": "aurora-mysql",
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBClusterSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBClusterSecret}::password}}"
            },
            "StorageEncrypted": true
        }
    }
}
```

**YAML example**

```
DBCluster:
  Type: AWS::RDS::DBCluster
  Properties:
    Engine: aurora-mysql
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBClusterSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
    StorageEncrypted: true
```

### CT\.RDS\.PR\.16 rule specification<a name="ct-rds-pr-16-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_cluster_storage_encrypted_check
# 
# Description:
#   This control checks whether the storage encryption is configured on Amazon Relational Database Service (RDS) database (DB) clusters that are not being restored from an existing cluster.
# 
# Reports on:
#   AWS::RDS::DBCluster
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document does not contain any RDS DB cluster resources
#       Then: SKIP
#   Scenario: 2
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an RDS DB cluster resource
#        And: 'SourceDBClusterIdentifier' or 'ReplicationSourceIdentifier' has been provided
#       Then: SKIP
#   Scenario: 3
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an RDS DB cluster resource
#        And: 'SourceDBClusterIdentifier' or 'ReplicationSourceIdentifier' has not been provided
#        And: 'StorageEncrypted' has not been provided
#       Then: FAIL
#   Scenario: 4
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an RDS DB cluster resource
#        And: 'SourceDBClusterIdentifier' or 'ReplicationSourceIdentifier' has not been provided
#        And: 'StorageEncrypted' has been provided and set to bool(false)
#       Then: FAIL
#   Scenario: 5
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an RDS DB cluster resource
#        And: 'SourceDBClusterIdentifier' or 'ReplicationSourceIdentifier' has not been provided
#        And: 'StorageEncrypted' has been provided and set to bool(true)
#       Then: PASS

#
# Constants
#
let RDS_CLUSTER_TYPE = "AWS::RDS::DBCluster"
let INPUT_DOCUMENT = this

#
# Assignments
#
let rds_cluster = Resources.*[ Type == %RDS_CLUSTER_TYPE ]

#
# Primary Rules
#
rule rds_cluster_storage_encrypted_check when is_cfn_template(%INPUT_DOCUMENT)
                                              %rds_cluster not empty {
    check(%rds_cluster.Properties)
        <<
        [CT.RDS.PR.16]: Require an Amazon RDS database cluster to have encryption at rest configured
            [FIX]: Set 'StorageEncrypted' to 'true'.
        >>
}

rule rds_cluster_storage_encrypted_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_CLUSTER_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.16]: Require an Amazon RDS database cluster to have encryption at rest configured
            [FIX]: Set 'StorageEncrypted' to 'true'.
        >>
}

#
# Parameterized Rules
#
rule check(rds_cluster) {
    %rds_cluster [
        # Scenario 2
        filter_sources(this)
    ] {
        # Scenario 3
        StorageEncrypted exists

        # Scenario 4 and 5
        StorageEncrypted == true
    }
}

rule filter_sources(rds_cluster) {
    %rds_cluster {
        # Scenario 2
        SourceDBClusterIdentifier not exists or
        filter_property_is_empty_string(SourceDBClusterIdentifier) or
        filter_is_not_valid_local_reference(%INPUT_DOCUMENT, SourceDBClusterIdentifier, "AWS::RDS::DBCluster")

        ReplicationSourceIdentifier not exists or
        filter_property_is_empty_string(ReplicationSourceIdentifier) or
        filter_replication_source_identifier(ReplicationSourceIdentifier)
    }
}

rule filter_property_is_empty_string(value) {
    %value {
        this is_string
        this == /\A\s*\z/
    }
}

rule filter_is_not_valid_local_reference(doc, reference_properties, referenced_resource_type) {
    %reference_properties {
        this not is_string
        this is_struct

        when this.'Ref' exists {
            'Ref' {
                when query_for_resource(%doc, this, %referenced_resource_type) {
                    this not exists
                }
                this exists
            }
        }
        when this.'Ref' not exists {
            this exists
        }
    }
}

rule filter_replication_source_identifier(reference_properties) {
    filter_is_not_valid_local_reference_via_join(%INPUT_DOCUMENT, %reference_properties, "AWS::RDS::DBCluster")
    filter_is_not_valid_local_reference_via_join(%INPUT_DOCUMENT, %reference_properties, "AWS::RDS::DBInstance")
}

rule filter_is_not_valid_local_reference_via_join(doc, reference_properties, referenced_resource_type) {
    %reference_properties {
        this not is_string
        this is_struct

        when this.'Fn::Join' exists {
            'Fn::Join' {
                when filter_list_contains_valid_local_reference(this[1], %doc, %referenced_resource_type) {
                    this not exists
                }
                this exists
            }
        }
        when this.'Fn::Join' not exists {
            this exists
        }
    }
}

rule filter_list_contains_valid_local_reference(list, doc, referenced_resource_type) {
     some %list.* {
         check_local_references(%doc, this, %referenced_resource_type)
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

### CT\.RDS\.PR\.16 example templates<a name="ct-rds-pr-16-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DBClusterSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS Cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasteruser"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: '"@/\'
  DBCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      Engine: aurora-mysql
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
      StorageEncrypted: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBClusterSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS Cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasteruser"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: '"@/\'
  DBCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      Engine: aurora-mysql
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
      StorageEncrypted: false
```

## \[CT\.RDS\.PR\.17\] Require an Amazon RDS event notification subscription to have critical database instance events configured<a name="ct-rds-pr-17-description"></a>

This control checks whether your Amazon RDS event subscriptions for RDS instances are configured to notify on event categories of `maintenance`, `failure`, and `configuration change`\.
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::EventSubscription`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.17 rule specification](#ct-rds-pr-17-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.17 rule specification](#ct-rds-pr-17-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.17 example templates](#ct-rds-pr-17-templates) 

**Explanation**

Amazon RDS event notifications use Amazon SNS to make you aware of changes in the availability or configuration of your RDS resources\. These notifications allow for rapid response\.

**Usage considerations**  
This control applies only to Amazon RDS Event Subscriptions for RDS instances \(`SourceType` of `db-instance`\)\.

### Remediation for rule failure<a name="ct-rds-pr-17-remediation"></a>

When `SourceType` is set to `db-instance`, set `Enabled` to true and ensure that the parameter `EventCategories` contains `maintenance`, `failure`, and `configuration change` values\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS Event Subscription \- Example One<a name="ct-rds-pr-17-remediation-1"></a>

Amazon RDS Event Subscription for RDS instances configured to notify on all event categories\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RDSEventSubscription": {
        "Type": "AWS::RDS::EventSubscription",
        "Properties": {
            "SnsTopicArn": {
                "Ref": "SnsTopic"
            },
            "SourceType": "db-instance",
            "Enabled": true
        }
    }
}
```

**YAML example**

```
RDSEventSubscription:
  Type: AWS::RDS::EventSubscription
  Properties:
    SnsTopicArn: !Ref 'SnsTopic'
    SourceType: db-instance
    Enabled: true
```

The examples that follow show how to implement this remediation\.

#### Amazon RDS Event Subscription \- Example Two<a name="ct-rds-pr-17-remediation-2"></a>

Amazon RDS Event Subscription for RDS instances configured to notify on `maintenance`, `failure`, and `configuration change` event categories\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RDSEventSubscription": {
        "Type": "AWS::RDS::EventSubscription",
        "Properties": {
            "SnsTopicArn": {
                "Ref": "SnsTopic"
            },
            "EventCategories": [
                "maintenance",
                "failure",
                "configuration change"
            ],
            "SourceType": "db-instance",
            "Enabled": true
        }
    }
}
```

**YAML example**

```
RDSEventSubscription:
  Type: AWS::RDS::EventSubscription
  Properties:
    SnsTopicArn: !Ref 'SnsTopic'
    EventCategories:
      - maintenance
      - failure
      - configuration change
    SourceType: db-instance
    Enabled: true
```

### CT\.RDS\.PR\.17 rule specification<a name="ct-rds-pr-17-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_instance_event_notifications_configured_check
# 
# Description:
#   Checks whether Amazon RDS event subscriptions for RDS instances are configured to notify on event categories of 'maintenance', 'failure', and 'configuration change'.
# 
# Reports on:
#   AWS::RDS::EventSubscription
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
#       And: The input document does not contain any Amazon RDS event subscription Resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription resource
#       And: 'SourceType' is provided and is not 'db-instance'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription resource
#       And: 'SourceType' is 'db-instance'
#       And: 'Enabled' is not provided or set to bool(false)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription resource
#       And: 'SourceType' is provided and is 'db-instance'
#       And: 'Enabled' is provided and set to bool(true)
#       And: 'EventCategories' does not contain 'maintenance', 'failure', and 'configuration change'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription resource
#       And: 'SourceType' is provided and is 'db-instance'
#       And: 'Enabled' is provided and set to bool(true)
#       And: 'EventCategories' does not exist or is an empty list
#      Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription resource
#       And: 'SourceType' is provided and is 'db-instance'
#       And: 'Enabled' is provided and set to bool(true)
#       And: 'EventCategories' contains 'maintenance', 'failure', and 'configuration change'
#      Then: PASS

#
# Constants
#
let RDS_EVENTSUBSCRIPTION_TYPE = "AWS::RDS::EventSubscription"
let INPUT_DOCUMENT = this
let EVENT_CATEGORIES = ["maintenance","failure","configuration change"]
let EVENT_SOURCE_TYPE = "db-instance"

#
# Assignments
#
let rds_event_subscriptions = Resources.*[ Type == %RDS_EVENTSUBSCRIPTION_TYPE ]

#
# Primary Rules
#
rule rds_instance_event_notifications_configured_check when is_cfn_template(%INPUT_DOCUMENT)
                                                            %rds_event_subscriptions not empty  {
    check(%rds_event_subscriptions.Properties)
        <<
        [CT.RDS.PR.17]: Require an Amazon RDS event notification subscription to have critical database instance events configured
        [FIX]: When 'SourceType' is set to 'db-instance', set 'Enabled' to true and ensure that the parameter 'EventCategories' contains 'maintenance', 'failure', and 'configuration change' values.
        >>
}

rule rds_instance_event_notifications_configured_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_EVENTSUBSCRIPTION_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_EVENTSUBSCRIPTION_TYPE.resourceProperties)
       <<
        [CT.RDS.PR.17]: Require an Amazon RDS event notification subscription to have critical database instance events configured
        [FIX]: When 'SourceType' is set to 'db-instance', set 'Enabled' to true and ensure that the parameter 'EventCategories' contains 'maintenance', 'failure', and 'configuration change' values.
        >>
}

#
# Parameterized Rules
#
rule check(resource) {
    %resource [ SourceType == %EVENT_SOURCE_TYPE ] {
        Enabled exists
        # Scenario 4
        Enabled == true
        # Scenario 5
        EventCategories not exists or
        # Scenario 6
        check_event_categories_for_required_events(EventCategories)
    }
}

rule check_event_categories_for_required_events(event_categories) {
    %event_categories {
        this exists
        this is_list
        this empty or
        %EVENT_CATEGORIES.* in this
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

### CT\.RDS\.PR\.17 example templates<a name="ct-rds-pr-17-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  SNSTopic:
    Type: AWS::SNS::Topic
    Properties: {}
  RDSEventSubscription:
    Type: AWS::RDS::EventSubscription
    Properties:
      SnsTopicArn:
        Ref: SNSTopic
      SourceType: db-instance
      Enabled: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  SNSTopic:
    Type: AWS::SNS::Topic
    Properties: {}
  RDSEventSubscription:
    Type: AWS::RDS::EventSubscription
    Properties:
      SnsTopicArn:
        Ref: SNSTopic
      EventCategories:
      - maintenance
      - failure
      SourceType: db-instance
      Enabled: true
```

## \[CT\.RDS\.PR\.18\] Require an Amazon RDS event notification subscription to have critical database parameter group events configured<a name="ct-rds-pr-18-description"></a>

This control checks whether your Amazon RDS event subscriptions for RDS parameter groups are configured to notify on event categories of `configuration change.`
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::EventSubscription`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.18 rule specification](#ct-rds-pr-18-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.18 rule specification](#ct-rds-pr-18-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.18 example templates](#ct-rds-pr-18-templates) 

**Explanation**

Amazon RDS event notifications use Amazon SNS to make you aware of changes in the availability or configuration of your RDS resources\. These notifications allow for rapid response\.

**Usage considerations**  
This control applies only to Amazon RDS event subscriptions for RDS parameter groups \(`SourceType` of `db-parameter-group`\)\.

### Remediation for rule failure<a name="ct-rds-pr-18-remediation"></a>

When `SourceType` is set to `db-parameter-group`, set `Enabled` to true and ensure that the parameter `EventCategories` contains `configuration change` as a value\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS Event Subscription \- Example One<a name="ct-rds-pr-18-remediation-1"></a>

Amazon RDS event subscription for RDS parameter groups configured to notify on all event categories\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RDSEventSubscription": {
        "Type": "AWS::RDS::EventSubscription",
        "Properties": {
            "SnsTopicArn": {
                "Ref": "SnsTopic"
            },
            "SourceType": "db-parameter-group",
            "Enabled": true
        }
    }
}
```

**YAML example**

```
RDSEventSubscription:
  Type: AWS::RDS::EventSubscription
  Properties:
    SnsTopicArn: !Ref 'SnsTopic'
    SourceType: db-parameter-group
    Enabled: true
```

The examples that follow show how to implement this remediation\.

#### Amazon RDS Event Subscription \- Example Two<a name="ct-rds-pr-18-remediation-2"></a>

Amazon RDS event subscription for RDS parameter groups configured to notify on the `configuration change` event category\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RDSEventSubscription": {
        "Type": "AWS::RDS::EventSubscription",
        "Properties": {
            "SnsTopicArn": {
                "Ref": "SnsTopic"
            },
            "EventCategories": [
                "configuration change"
            ],
            "SourceType": "db-parameter-group",
            "Enabled": true
        }
    }
}
```

**YAML example**

```
RDSEventSubscription:
  Type: AWS::RDS::EventSubscription
  Properties:
    SnsTopicArn: !Ref 'SnsTopic'
    EventCategories:
      - configuration change
    SourceType: db-parameter-group
    Enabled: true
```

### CT\.RDS\.PR\.18 rule specification<a name="ct-rds-pr-18-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_pg_event_notifications_configured_check
# 
# Description:
#   Checks whether Amazon RDS event subscriptions for RDS parameter groups are configured to notify on event categories of 'configuration change'.
# 
# Reports on:
#   AWS::RDS::EventSubscription
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
#       And: The input document does not contain any RDS event subscription resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an RDS event subscription resource
#       And: 'SourceType' is provided and is not 'db-parameter-group'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an RDS event subscription resource
#       And: 'SourceType' is 'db-parameter-group'
#       And: 'Enabled' is not provided or set to bool(false)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an RDS event subscription resource
#       And: 'SourceType' is provided and is 'db-parameter-group'
#       And: 'Enabled' is provided and set to bool(true)
#       And: 'EventCategories' does not contain 'configuration change'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an RDS event subscription resource
#       And: 'SourceType' is provided and is 'db-parameter-group'
#       And: 'Enabled' is provided and set to bool(true)
#       And: 'EventCategories' does not exist or is an empty list
#      Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an RDS event subscription resource
#       And: 'SourceType' is provided and is 'db-parameter-group'
#       And: 'Enabled' is provided and set to bool(true)
#       And: 'EventCategories' contains 'configuration change'
#      Then: PASS

#
# Constants
#
let RDS_EVENTSUBSCRIPTION_TYPE = "AWS::RDS::EventSubscription"
let INPUT_DOCUMENT = this
let EVENT_CATEGORIES = ["configuration change"]
let EVENT_SOURCE_TYPE = "db-parameter-group"

#
# Assignments
#
let rds_event_subscriptions = Resources.*[ Type == %RDS_EVENTSUBSCRIPTION_TYPE ]

#
# Primary Rules
#
rule rds_pg_event_notifications_configured_check when is_cfn_template(%INPUT_DOCUMENT)
                                                      %rds_event_subscriptions not empty  {
    check(%rds_event_subscriptions.Properties)
        <<
        [CT.RDS.PR.18]: Require an Amazon RDS event notification subscription to have critical database parameter group events configured
        [FIX]: When 'SourceType' is set to 'db-parameter-group', set 'Enabled' to true and ensure that the parameter 'EventCategories' contains 'configuration change' as a value.
        >>
}

rule rds_pg_event_notifications_configured_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_EVENTSUBSCRIPTION_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_EVENTSUBSCRIPTION_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.18]: Require an Amazon RDS event notification subscription to have critical database parameter group events configured
        [FIX]: When 'SourceType' is set to 'db-parameter-group', set 'Enabled' to true and ensure that the parameter 'EventCategories' contains 'configuration change' as a value.
        >>
}

#
# Parameterized Rules
#
rule check(resource) {
    %resource [ SourceType == %EVENT_SOURCE_TYPE ] {
        Enabled exists
        # Scenario 4
        Enabled == true
        # Scenario 5
        EventCategories not exists or
        # Scenario 6
        check_event_categories_for_required_events(EventCategories)
    }
}

rule check_event_categories_for_required_events(event_categories) {
    %event_categories {
        this exists
        this is_list
        this empty or
        %EVENT_CATEGORIES.* in this
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

### CT\.RDS\.PR\.18 example templates<a name="ct-rds-pr-18-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  SNSTopic:
    Type: AWS::SNS::Topic
    Properties: {}
  RDSEventSubscription:
    Type: AWS::RDS::EventSubscription
    Properties:
      SnsTopicArn:
        Ref: SNSTopic
      SourceType: db-parameter-group
      Enabled: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  SNSTopic:
    Type: AWS::SNS::Topic
    Properties: {}
  RDSEventSubscription:
    Type: AWS::RDS::EventSubscription
    Properties:
      SnsTopicArn:
        Ref: SNSTopic
      Enabled: false
      SourceType: db-parameter-group
```

## \[CT\.RDS\.PR\.19\] Require an Amazon RDS event notifications subscription to have critical database security group events configured<a name="ct-rds-pr-19-description"></a>

This control checks whether your Amazon RDS event subscriptions for RDS security groups are configured to notify on event categories of `failure` and `configuration change`
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::EventSubscription`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.19 rule specification](#ct-rds-pr-19-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.19 rule specification](#ct-rds-pr-19-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.19 example templates](#ct-rds-pr-19-templates) 

**Explanation**

Amazon RDS event notifications use Amazon SNS to make you aware of changes in the availability or configuration of your RDS resources\. These notifications allow for a rapid response\.

**Usage considerations**  
This control applies only to Amazon RDS Event Subscriptions for RDS security groups \(`SourceType` of `db-security-group`\)

### Remediation for rule failure<a name="ct-rds-pr-19-remediation"></a>

When `SourceType` is set to `db-security-group`, set `Enabled` to `true` and ensure that the parameter `EventCategories` contains both `failure` and `configuration change` values\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS Event Subscription \- Example One<a name="ct-rds-pr-19-remediation-1"></a>

Amazon RDS Event Subscription for RDS security groups configured to notify on all event categories\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RDSEventSubscription": {
        "Type": "AWS::RDS::EventSubscription",
        "Properties": {
            "SnsTopicArn": {
                "Ref": "SnsTopic"
            },
            "SourceType": "db-security-group",
            "Enabled": true
        }
    }
}
```

**YAML example**

```
RDSEventSubscription:
  Type: AWS::RDS::EventSubscription
  Properties:
    SnsTopicArn: !Ref 'SnsTopic'
    SourceType: db-security-group
    Enabled: true
```

The examples that follow show how to implement this remediation\.

#### Amazon RDS Event Subscription \- Example Two<a name="ct-rds-pr-19-remediation-2"></a>

Amazon RDS Event Subscription for RDS security groups configured to notify on `failure` and `configuration change` event categories\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RDSEventSubscription": {
        "Type": "AWS::RDS::EventSubscription",
        "Properties": {
            "SnsTopicArn": {
                "Ref": "SnsTopic"
            },
            "EventCategories": [
                "failure",
                "configuration change"
            ],
            "SourceType": "db-security-group",
            "Enabled": true
        }
    }
}
```

**YAML example**

```
RDSEventSubscription:
  Type: AWS::RDS::EventSubscription
  Properties:
    SnsTopicArn: !Ref 'SnsTopic'
    EventCategories:
      - failure
      - configuration change
    SourceType: db-security-group
    Enabled: true
```

### CT\.RDS\.PR\.19 rule specification<a name="ct-rds-pr-19-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_sg_event_notifications_configured_check
# 
# Description:
#   Checks whether an Amazon RDS event subscription for RDS security groups are configured to notify on event categories of 'failure' and 'configuration change'.
# 
# Reports on:
#   AWS::RDS::EventSubscription
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
#       And: The input document does not contain any Amazon RDS event subscription resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription Resource
#       And: 'SourceType' is provided and is not 'db-security-group'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription resource
#       And: 'SourceType' is 'db-security-group'
#       And: 'Enabled' is not provided or set to bool(false)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription resource
#       And: 'SourceType' is provided and is 'db-security-group'
#       And: 'Enabled' is provided and set to bool(true)
#       And: 'EventCategories' does not contain both 'failure' and 'configuration change'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription resource
#       And: 'SourceType' is provided and is 'db-security-group'
#       And: 'Enabled' is provided and set to bool(true)
#       And: 'EventCategories' does not exist or is an empty list
#      Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an Amazon RDS event subscription resource
#       And: 'SourceType' is provided and is 'db-security-group'
#       And: 'Enabled' is provided and set to bool(true)
#       And: 'EventCategories' contains both 'failure' and 'configuration change'
#      Then: PASS

#
# Constants
#
let RDS_EVENTSUBSCRIPTION_TYPE = "AWS::RDS::EventSubscription"
let INPUT_DOCUMENT = this
let EVENT_CATEGORIES = ["failure","configuration change"]
let EVENT_SOURCE_TYPE = "db-security-group"

#
# Assignments
#
let rds_event_subscriptions = Resources.*[ Type == %RDS_EVENTSUBSCRIPTION_TYPE ]

#
# Primary Rules
#
rule rds_sg_event_notifications_configured_check when is_cfn_template(%INPUT_DOCUMENT)
                                                      %rds_event_subscriptions not empty  {
    check(%rds_event_subscriptions.Properties)
        <<
        [CT.RDS.PR.19]: Require an Amazon RDS event notifications subscription to have critical database security group events configured
        [FIX]: When 'SourceType' is set to 'db-security-group', set 'Enabled' to true and ensure that the parameter 'EventCategories' contains both 'failure' and 'configuration change' values.
        >>
}

rule rds_sg_event_notifications_configured_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_EVENTSUBSCRIPTION_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_EVENTSUBSCRIPTION_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.19]: Require an Amazon RDS event notifications subscription to have critical database security group events configured
        [FIX]: When 'SourceType' is set to 'db-security-group', set 'Enabled' to true and ensure that the parameter 'EventCategories' contains both 'failure' and 'configuration change' values.
        >>
}

#
# Parameterized Rules
#
rule check(resource) {
    %resource [ SourceType == %EVENT_SOURCE_TYPE ] {
        Enabled exists
        # Scenario 4
        Enabled == true
        # Scenario 5
        EventCategories not exists or
        # Scenario 6
        check_event_categories_for_required_events(EventCategories)
    }
}

rule check_event_categories_for_required_events(event_categories) {
    %event_categories {
        this exists
        this is_list
        this empty or
        %EVENT_CATEGORIES.* in this
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

### CT\.RDS\.PR\.19 example templates<a name="ct-rds-pr-19-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  SNSTopic:
    Type: AWS::SNS::Topic
    Properties: {}
  RDSEventSubscription:
    Type: AWS::RDS::EventSubscription
    Properties:
      SnsTopicArn:
        Ref: SNSTopic
      SourceType: db-security-group
      Enabled: true
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  SNSTopic:
    Type: AWS::SNS::Topic
    Properties: {}
  RDSEventSubscription:
    Type: AWS::RDS::EventSubscription
    Properties:
      SnsTopicArn:
        Ref: SNSTopic
      EventCategories:
      - failure
      SourceType: db-security-group
      Enabled: true
```

## \[CT\.RDS\.PR\.20\] Require an Amazon RDS database instance not to use a database engine default port<a name="ct-rds-pr-20-description"></a>

This control checks whether Amazon Relational Database Service \(RDS\) database instances are configured for default database port for their specific engine types\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBInstance`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.20 rule specification](#ct-rds-pr-20-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.20 rule specification](#ct-rds-pr-20-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.20 example templates](#ct-rds-pr-20-templates) 

**Explanation**

If you use a known port to deploy an Amazon RDS cluster or instance, an attacker can guess information about the cluster or instance\. The attacker can use this information in conjunction with other information to connect to an Amazon RDS cluster or instance, or to gain additional information about your application\.

When you change the port, you must also update the existing connection strings that were used to connect to the old port\. You also should check the security group of the DB instance to ensure that it includes an ingress rule that allows connectivity on the new port\.

**Usage considerations**  
This control applies only to Amazon RDS DB engine types `mariadb`, `mysql`, `oracle-ee`, `oracle-se2`, `oracle-ee-cdb`, `oracle-se2-cdb`, `postgres`, `sqlserver-ee`, `sqlserver-se`, `sqlserver-ex` and `sqlserver-web`\.

### Remediation for rule failure<a name="ct-rds-pr-20-remediation"></a>

Set a value for `Port` that is different than the default value for the Amazon RDS DB instance engine type\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example One<a name="ct-rds-pr-20-remediation-1"></a>

Amazon RDS DB instance configured with a port that's different than the `mysql` engine default port\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "mysql",
            "EngineVersion": 5.7,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            },
            "StorageEncrypted": true,
            "Port": 6733
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: mysql
    EngineVersion: 5.7
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    StorageEncrypted: true
    Port: 6733
  DeletionPolicy: Delete
```

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example Two<a name="ct-rds-pr-20-remediation-2"></a>

Amazon RDS DB instance configured with a port that's different than the `postgres` engine default port\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "postgres",
            "EngineVersion": 14.2,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            },
            "Port": 5723
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: postgres
    EngineVersion: 14.2
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    Port: 5723
  DeletionPolicy: Delete
```

### CT\.RDS\.PR\.20 rule specification<a name="ct-rds-pr-20-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_instance_no_default_ports_check
# 
# Description:
#   This control checks whether Amazon Relational Database Service (RDS) database instances are configured for default database port for their specific engine types.
# 
# Reports on:
#   AWS::RDS::DBInstance
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
#       And: The input document does not contain any Amazon RDS DB instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is not one of  'mariadb', 'mysql',
#            'oracle-ee', 'oracle-se2', 'oracle-ee-cdb', 'oracle-se2-cdb',
#            'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of  'mariadb', 'mysql',
#            'oracle-ee', 'oracle-se2', 'oracle-ee-cdb', 'oracle-se2-cdb',
#            'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'Port' has not been specified
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of  'mariadb', 'mysql',
#            'oracle-ee', 'oracle-se2', 'oracle-ee-cdb', 'oracle-se2-cdb',
#            'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'Port' has been specified
#       And: 'Port' value is default port (includes mysql/mariadb port '3306', sqlserver
#            port '1433', postgres port '5432', and oracle port '1521')
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine'  is one of 'mariadb', 'mysql'
#       And: 'Port' has been specified
#       And: 'Port' value is not equal to '3306'
#      Then: PASS
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is 'postgres'
#       And: 'Port' has been specified
#       And: 'Port' value is not equal to '5432'
#      Then: PASS
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'Port' has been specified
#       And: 'Port' value is not equal to '1433'
#      Then: PASS
#   Scenario: 8
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'oracle-ee', 'oracle-se2', 'oracle-ee-cdb', 'oracle-se2-cdb',
#       And: 'Port' has been specified
#       And: 'Port' value is not equal to '1521'
#      Then: PASS

#
# Constants
#
let RDS_DB_INSTANCE_TYPE = "AWS::RDS::DBInstance"
let INPUT_DOCUMENT = this
let ORACLE_ENGINES = [ "oracle-ee", "oracle-se2", "oracle-se1", "oracle-se" ]
let SQLSERVER_ENGINES = [ "sqlserver-ee", "sqlserver-se", "sqlserver-ex", "sqlserver-web" ]
let MYSQL_OR_MARIA_ENGINES = [ "mariadb", "mysql" ]
let POSTGRES_ENGINES = [ "postgres" ]
let MYSQL_MARIA_DEFAULT_PORTS = [3306, "3306"]
let POSTGRES_DEFAULT_PORTS = [5432, "5432"]
let SQL_DEFAULT_PORTS = [1433, "1433"]
let ORACLE_DEFAULT_PORTS = [1521, "1521"]

#
# Assignments
#
let rds_db_instances = Resources.*[ Type == %RDS_DB_INSTANCE_TYPE ]

#
# Primary Rules
#
rule rds_instance_no_default_ports_check when is_cfn_template(%INPUT_DOCUMENT)
                                              %rds_db_instances not empty {
    check(%rds_db_instances.Properties)
        <<
        [CT.RDS.PR.20]: Require an Amazon RDS database instance not to use a database engine default port
        [FIX]: Set a value for 'Port' that is different than the default value for the Amazon RDS DB instance engine type.
        >>
}

rule rds_instance_no_default_ports_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.20]: Require an Amazon RDS database instance not to use a database engine default port
        [FIX]: Set a value for 'Port' that is different than the default value for the Amazon RDS DB instance engine type.
        >>
}

#
# Parameterized Rules
#
rule check(rds_db_instance) {
    # Scenario: 4 and 5
    %rds_db_instance[ filter_engine(this, %MYSQL_OR_MARIA_ENGINES) ] {
        check_port(Port, %MYSQL_MARIA_DEFAULT_PORTS)
    }
    # Scenario: 4 and 6
    %rds_db_instance[ filter_engine(this, %POSTGRES_ENGINES) ] {
        check_port(Port, %POSTGRES_DEFAULT_PORTS)
    }
    # Scenario: 4 and 7
    %rds_db_instance[ filter_engine(this, %SQLSERVER_ENGINES) ] {
        check_port(Port, %SQL_DEFAULT_PORTS)
    }
    # Scenario: 4 and 8
    %rds_db_instance[ filter_engine(this, %ORACLE_ENGINES) ] {
        check_port(Port, %ORACLE_DEFAULT_PORTS)
    }
}

rule filter_engine(db_properties, engine) {
    %db_properties {
        # Scenario: 2
        Engine exists
        Engine is_string
        Engine in %engine
    }
}

rule check_port(port, default_ports) {
    # Scenario: 3
    %port exists
    %port not in %default_ports
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

### CT\.RDS\.PR\.20 example templates<a name="ct-rds-pr-20-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: mysql
      EngineVersion: 5.7
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      Port: 6733
    DeletionPolicy: Delete
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: mysql
      EngineVersion: 5.7
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      Port: 3306
    DeletionPolicy: Delete
```

## \[CT\.RDS\.PR\.21\] Require an Amazon RDS DB cluster to have a unique administrator username<a name="ct-rds-pr-21-description"></a>

This control checks whether an Amazon Relational Database Service \(RDS\) database \(DB\) cluster has changed the administrator username from its default value\.
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBCluster`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.21 rule specification](#ct-rds-pr-21-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.21 rule specification](#ct-rds-pr-21-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.21 example templates](#ct-rds-pr-21-templates) 

**Explanation**

When you create an Amazon RDS database, we recommend that you change the default administrator username to a unique value\. Default user names are public knowledge, and they should be changed, because changing these user names reduces the risk of unintended access\.

**Usage considerations**  
This control applies only to Amazon RDS DB clusters that set the `MasterUsername` property\.

### Remediation for rule failure<a name="ct-rds-pr-21-remediation"></a>

Set `MasterUsername` to a value other than `admin` or `postgres`\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Cluster \- Example<a name="ct-rds-pr-21-remediation-1"></a>

Amazon RDS DB cluster configured with a unique administrator username\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBCluster": {
        "Type": "AWS::RDS::DBCluster",
        "Properties": {
            "Engine": "aurora-mysql",
            "MasterUsername": "samplemasteruser",
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${RDSClusterSecret}::password}}"
            },
            "DBSubnetGroupName": {
                "Ref": "DBSubnetGroup"
            }
        }
    }
}
```

**YAML example**

```
DBCluster:
  Type: AWS::RDS::DBCluster
  Properties:
    Engine: aurora-mysql
    MasterUsername: samplemasteruser
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${RDSClusterSecret}::password}}'
    DBSubnetGroupName: !Ref 'DBSubnetGroup'
```

### CT\.RDS\.PR\.21 rule specification<a name="ct-rds-pr-21-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_cluster_default_admin_check
# 
# Description:
#   This control checks whether an Amazon Relational Database Service (RDS) database (DB) cluster has changed the administrator username from its default value.
# 
# Reports on:
#   AWS::RDS::DBCluster
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
#       And: The input document does not contain any RDS DB cluster resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'MasterUsername' has not been provided
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'MasterUsername' has been provided and it is set to 'admin' or 'postgres'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'MasterUsername' has been provided and is not set to 'admin' or 'postgres'
#      Then: PASS

#
# Constants
#
let RDS_DB_CLUSTER_TYPE = "AWS::RDS::DBCluster"
let DISALLOWED_MASTER_USERNAMES = ["admin", "postgres"]
let INPUT_DOCUMENT = this

#
# Assignments
#
let db_clusters = Resources.*[ Type == %RDS_DB_CLUSTER_TYPE ]

#
# Primary Rules
#
rule rds_cluster_default_admin_check when is_cfn_template(%INPUT_DOCUMENT)
                                          %db_clusters not empty {
    check(%db_clusters.Properties)
        <<
        [CT.RDS.PR.21]: Require an Amazon RDS DB cluster to have a unique administrator username
            [FIX]: Set 'MasterUsername' to a value other than 'admin' or 'postgres'.
        >>
}

rule rds_cluster_default_admin_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_CLUSTER_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.21]: Require an Amazon RDS DB cluster to have a unique administrator username
            [FIX]: Set 'MasterUsername' to a value other than 'admin' or 'postgres'.
        >>
}

rule check(db_cluster) {
    %db_cluster [
        # scenario 2
        filter_master_username_provided(this)
    ] {
        # scenario 3 and 4
        MasterUsername not in %DISALLOWED_MASTER_USERNAMES
    }
}

#
# Utility Rules
#
rule filter_master_username_provided(dbcluster_properties) {
    %dbcluster_properties{
        MasterUsername exists
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

### CT\.RDS\.PR\.21 example templates<a name="ct-rds-pr-21-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/25
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.0.128/25
      AvailabilityZone:
        Fn::Select:
        - 1
        - Fn::GetAZs: ''
      VpcId:
        Ref: VPC
  DBSubnetGroup:
    Type: AWS::RDS::DBSubnetGroup
    Properties:
      DBSubnetGroupDescription: DB subnet group for DBCluster
      SubnetIds:
      - Ref: SubnetOne
      - Ref: SubnetTwo
  DBClusterSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasteruser"}'
        GenerateStringKey: password
        PasswordLength: 32
        ExcludeCharacters: "/@\"'\\"
  DBCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      Engine: aurora-mysql
      MasterUsername: examplemasteruser
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
      DBSubnetGroupName:
        Ref: DBSubnetGroup
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
  SubnetOne:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId:
        Ref: VPC
      CidrBlock: 10.0.0.0/25
      AvailabilityZone:
        Fn::Select:
        - 0
        - Fn::GetAZs: ''
  SubnetTwo:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.0.128/25
      AvailabilityZone:
        Fn::Select:
        - 1
        - Fn::GetAZs: ''
      VpcId:
        Ref: VPC
  DBSubnetGroup:
    Type: AWS::RDS::DBSubnetGroup
    Properties:
      DBSubnetGroupDescription: DB subnet group for DBCluster
      SubnetIds:
      - Ref: SubnetOne
      - Ref: SubnetTwo
  DBClusterSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB cluster secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasteruser"}'
        GenerateStringKey: password
        PasswordLength: 32
        ExcludeCharacters: "/@\"'\\"
  DBCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      Engine: aurora-mysql
      MasterUsername: admin
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
      DBSubnetGroupName:
        Ref: DBSubnetGroup
```

## \[CT\.RDS\.PR\.22\] Require an Amazon RDS database instance to have a unique administrator username<a name="ct-rds-pr-22-description"></a>

This control checks whether an Amazon Relational Database Service \(RDS\) database has changed the adminstrator username from its default value\.
+ **Control objective: **Protect configurations
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBInstance`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.22 rule specification](#ct-rds-pr-22-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.22 rule specification](#ct-rds-pr-22-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.22 example templates](#ct-rds-pr-22-templates) 

**Explanation**

Default administrative usernames on Amazon RDS databases are public knowledge\. When creating an Amazon RDS database, you should change the default administrative username to a unique value, thereby reducing the risk of unintended access\.

**Usage considerations**  
This control applies only to Amazon RDS DB engine types `mariadb`, `mysql`, `oracle-ee`, `oracle-ee-cdb`, `oracle-se2`, `oracle-se2-cdb`, `postgres`, `sqlserver-ee`, `sqlserver-se`, `sqlserver-ex`, and `sqlserver-web`\.

### Remediation for rule failure<a name="ct-rds-pr-22-remediation"></a>

Set `MasterUsername` to a value other than `postgres` or `admin`\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example<a name="ct-rds-pr-22-remediation-1"></a>

Amazon RDS DB instance configured with a custom administrator username\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "postgres",
            "EngineVersion": 14.2,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": "testUser",
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            }
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: postgres
    EngineVersion: 14.2
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: testUser
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
  DeletionPolicy: Delete
```

### CT\.RDS\.PR\.22 rule specification<a name="ct-rds-pr-22-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_instance_default_admin_check
# 
# Description:
#   This control checks whether an Amazon Relational Database Service (RDS) database has changed the adminstrator username from its default value.
# 
# Reports on:
#   AWS::RDS::DBInstance
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
#       And: The input document does not contain any RDS DB instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is not one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'MasterUsername' has been specified and is one of 'postgres' or 'admin'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#       And: 'MasterUsername' has been specified and is not one of 'postgres' or 'admin'
#      Then: PASS

#
# Constants
#
let RDS_DB_INSTANCE_TYPE = "AWS::RDS::DBInstance"
let INPUT_DOCUMENT = this
let SUPPORTED_RDS_INSTANCE_ENGINES = [
    "mariadb", "mysql", "oracle-ee", "oracle-ee-cdb", "oracle-se2",
    "oracle-se2-cdb", "postgres", "sqlserver-ee", "sqlserver-se",
    "sqlserver-ex", "sqlserver-web"
]
let RDS_DEFAULT_USERNAMES = [ "postgres", "admin" ]

#
# Assignments
#
let rds_db_instances = Resources.*[ Type == %RDS_DB_INSTANCE_TYPE ]

#
# Primary Rules
#
rule rds_instance_default_admin_check when is_cfn_template(%INPUT_DOCUMENT)
                                           %rds_db_instances not empty {
    check(%rds_db_instances.Properties)
        <<
        [CT.RDS.PR.22]: Require an Amazon RDS database instance to have a unique administrator username
        [FIX]: Set 'MasterUsername' to a value other than 'postgres' or 'admin'.
        >>
}

rule rds_instance_default_admin_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.22]: Require an Amazon RDS database instance to have a unique administrator username
        [FIX]: Set 'MasterUsername' to a value other than 'postgres' or 'admin'.
        >>
}

#
# Parameterized Rules
#
rule check(rds_db_instance) {
    %rds_db_instance [ filter_engine_and_master_username_provided(this) ] {
        # Scenario: 3 and 4
        MasterUsername not in %RDS_DEFAULT_USERNAMES
    }
}

rule filter_engine_and_master_username_provided(db_properties) {
    %db_properties {
        # Scenario: 2
        MasterUsername exists
        Engine exists
        Engine is_string
        Engine in %SUPPORTED_RDS_INSTANCE_ENGINES
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

### CT\.RDS\.PR\.22 example templates<a name="ct-rds-pr-22-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Test RDS DB Instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername: testUser
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    DeletionPolicy: Delete
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: Test RDS DB Instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "testUser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername: postgres
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    DeletionPolicy: Delete
```

## \[CT\.RDS\.PR\.23\] Require an Amazon RDS database instance to not be publicly accessible<a name="ct-rds-pr-23-description"></a>

This rule checks whether Amazon Relational Database Service \(RDS\) database \(DB\) instances are publicly accessible, as determined by checking the `PubliclyAccessible` configuration property\.
+ **Control objective: **Limit network access
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBInstance`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.23 rule specification](#ct-rds-pr-23-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.23 rule specification](#ct-rds-pr-23-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.23 example templates](#ct-rds-pr-23-templates) 

**Explanation**

The `PubliclyAccessible` property in the RDS instance CloudFormation resource indicates whether the DB instance is publicly accessible\. When the DB instance is configured with `PubliclyAccessible` set to `true`, it is an internet\-facing instance with a publicly resolvable DNS name, which resolves to a public IP address\. When the DB instance isn't publicly accessible, it is an internal instance with a DNS name that resolves to a private IP address\.

Unless you intend for your RDS instance to be publicly accessible, do not configure the RDS instance with the `PubliclyAccessible` value set to `true`, because this configuration may allow unwanted traffic to your database instance\.

### Remediation for rule failure<a name="ct-rds-pr-23-remediation"></a>

Set the value of `PubliclyAccessible` to `false`\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Instance \- Example<a name="ct-rds-pr-23-remediation-1"></a>

Amazon RDS DB instance configured as an internal instance, by mmeans of a publicly accessible configuration\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "postgres",
            "EngineVersion": 14.2,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBInstanceSecret}::password}}"
            },
            "PubliclyAccessible": false
        },
        "DeletionPolicy": "Delete"
    }
}
```

**YAML example**

```
DBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: postgres
    EngineVersion: 14.2
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
    PubliclyAccessible: false
  DeletionPolicy: Delete
```

### CT\.RDS\.PR\.23 rule specification<a name="ct-rds-pr-23-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_instance_public_access_check
# 
# Description:
#   This rule checks whether Amazon Relational Database Service (RDS) database (DB) instances are publicly accessible, as determined by checking the 'PubliclyAccessible' configuration property.
# 
# Reports on:
#   AWS::RDS::DBInstance
# 
# Evaluates:
#   AWS CloudFormation, AWS CloudFormation hook
# 
# Rule Parameters:
#   None
# 
# Scenarios:
#   Scenario: 1
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document does not contain any RDS DB instance resources
#       Then: SKIP
#   Scenario: 2
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an RDS DB instance resource
#        And: 'PubliclyAccessible' has not been specified
#       Then: FAIL
#   Scenario: 3
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an RDS DB instance resource
#        And: 'PubliclyAccessible' is present and is a value other than bool(false)
#       Then: FAIL
#   Scenario: 4
#      Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#        And: The input document contains an RDS DB instance resource
#        And: 'PubliclyAccessible' has been specified and set to bool(false)
#       Then: PASS

#
# Constants
#
let RDS_DB_INSTANCE_TYPE = "AWS::RDS::DBInstance"
let INPUT_DOCUMENT = this

#
# Assignments
#
let rds_db_instances = Resources.*[ Type == %RDS_DB_INSTANCE_TYPE ]

#
# Primary Rules
#
rule rds_instance_public_access_check when is_cfn_template(%INPUT_DOCUMENT)
                                           %rds_db_instances not empty {
    check(%rds_db_instances.Properties)
        <<
        [CT.RDS.PR.23]: Require an Amazon RDS database instance to not be publicly accessible
            [FIX]: Set the value of 'PubliclyAccessible' to 'false'.
        >>
}

rule rds_instance_public_access_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.23]: Require an Amazon RDS database instance to not be publicly accessible
            [FIX]: Set the value of 'PubliclyAccessible' to 'false'.
        >>
}

#
# Parameterized Rules
#
rule check(rds_db_instance) {
    %rds_db_instance{
       #Scenario: 2
       PubliclyAccessible exists
       #Scenario: 3 and 4
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

### CT\.RDS\.PR\.23 example templates<a name="ct-rds-pr-23-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasteruser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      PubliclyAccessible: false
    DeletionPolicy: Delete
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS DB instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "examplemasteruser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: '"@/\'
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      PubliclyAccessible: true
    DeletionPolicy: Delete
```

## \[CT\.RDS\.PR\.24\] Require an Amazon RDS database instance to have encryption at rest configured<a name="ct-rds-pr-24-description"></a>

This control checks whether storage encryption is enabled for your Amazon RDS database \(DB\) instance\.
+ **Control objective: **Encrypt data at rest
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBInstance`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.24 rule specification](#ct-rds-pr-24-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.24 rule specification](#ct-rds-pr-24-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.24 example templates](#ct-rds-pr-24-templates) 

**Explanation**

For an added layer of security for your sensitive data in Amazon RDS DB instances, you should configure your RDS DB instances to be encrypted at rest\. To encrypt your RDS DB instances and snapshots at rest, enable the encryption option for your RDS DB instances\. Data that is encrypted at rest includes the underlying storage for DB instances, its automated backups, read replicas, and snapshots\.

Encrypted Amazon RDS DB instances use the open standard AES\-256 encryption algorithm to encrypt your data on the server that hosts your RDS DB instances\. After your data is encrypted, Amazon RDS handles authentication of access and decryption of your data transparently with a minimal impact on performance\. You do not need to modify your database client applications to use encryption\.

Amazon RDS encryption currently is available for all database engines and storage types\. Amazon RDS encryption is available for most DB instance classes\.

**Usage considerations**  
This control applies only to Amazon RDS DB engine types `mariadb`, `mysql`, `oracle-ee`, `oracle-ee-cdb`, `oracle-se2`, `oracle-se2-cdb`, `postgres`, `sqlserver-ee`, `sqlserver-se`, `sqlserver-ex` and `sqlserver-web`

### Remediation for rule failure<a name="ct-rds-pr-24-remediation"></a>

The parameter `StorageEncrypted` must be set to true for RDS DB Instances\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB instance \- Example<a name="ct-rds-pr-24-remediation-1"></a>

Amazon RDS DB instance with storage encryption enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "RDSDBInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "Engine": "postgres",
            "EngineVersion": 14.2,
            "DBInstanceClass": "db.m5.large",
            "StorageType": "gp2",
            "AllocatedStorage": 5,
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${RDSDBInstanceSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${RDSDBInstanceSecret}::password}}"
            },
            "StorageEncrypted": true
        }
    }
}
```

**YAML example**

```
RDSDBInstance:
  Type: AWS::RDS::DBInstance
  Properties:
    Engine: postgres
    EngineVersion: 14.2
    DBInstanceClass: db.m5.large
    StorageType: gp2
    AllocatedStorage: 5
    MasterUsername: !Sub '{{resolve:secretsmanager:${RDSDBInstanceSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${RDSDBInstanceSecret}::password}}'
    StorageEncrypted: true
```

### CT\.RDS\.PR\.24 rule specification<a name="ct-rds-pr-24-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_instance_storage_encrypted_check
# 
# Description:
#   Checks whether storage encryption is enabled for your Amazon RDS DB instances.
# 
# Reports on:
#   AWS::RDS::DBInstance
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
#       And: The input document does not contain any RDS DB instance resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or CloudFormation hook document
#       And: The input document contains an RDS DB instance resource
#       And: 'Engine' is not one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#            'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#      Then: SKIP
#   Scenario: 3
#      Given: The input document is an AWS CloudFormation or CloudFormation hook document
#        And: The input document contains an RDS DB instance resource
#        And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#        And: 'StorageEncrypted' has not been provided
#       Then: FAIL
#   Scenario: 4
#      Given: The input document is an AWS CloudFormation or CloudFormation hook document
#        And: The input document contains an RDS DB instance resource
#        And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#        And: 'StorageEncrypted' has been provided and set to bool(false)
#       Then: FAIL
#   Scenario: 5
#      Given: The input document is an AWS CloudFormation or CloudFormation hook document
#        And: The input document contains an RDS DB instance resource
#        And: 'Engine' is one of 'mariadb', 'mysql', 'oracle-ee', 'oracle-ee-cdb', 'oracle-se2',
#             'oracle-se2-cdb', 'postgres', 'sqlserver-ee', 'sqlserver-se', 'sqlserver-ex', 'sqlserver-web'
#        And: 'StorageEncrypted' has been provided and set to bool(true)
#       Then: PASS

#
# Constants
#
let RDS_DB_INSTANCE_TYPE = "AWS::RDS::DBInstance"
let INPUT_DOCUMENT = this
let SUPPORTED_RDS_INSTANCE_ENGINES = [
    "mariadb", "mysql", "oracle-ee", "oracle-ee-cdb", "oracle-se2",
    "oracle-se2-cdb", "postgres", "sqlserver-ee", "sqlserver-se",
    "sqlserver-ex", "sqlserver-web"
]

#
# Assignments
#
let rds_db_instances = Resources.*[ Type == %RDS_DB_INSTANCE_TYPE ]

#
# Primary Rules
#
rule rds_instance_storage_encrypted_check when is_cfn_template(%INPUT_DOCUMENT)
                                               %rds_db_instances not empty {
    check(%rds_db_instances.Properties)
        <<
        [CT.RDS.PR.24]: Require an Amazon RDS database instance to have encryption at rest configured
        [FIX]: The parameter 'StorageEncrypted' must be set to true for RDS DB Instances.
        >>
}

rule rds_instance_storage_encrypted_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_INSTANCE_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_INSTANCE_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.24]: Require an Amazon RDS database instance to have encryption at rest configured
        [FIX]: The parameter 'StorageEncrypted' must be set to true for RDS DB Instances.
        >>
}

#
# Parameterized Rules
#
rule check(rds_db_instance) {
    %rds_db_instance [filter_restore_and_engine(this)] {
        #Scenario: 3
        StorageEncrypted exists
        #Scenario: 4 and 5
        StorageEncrypted == true
    }
}

rule filter_restore_and_engine(db_properties) {
    %db_properties {
        #Scenario: 2
        Engine exists
        Engine is_string
        Engine in %SUPPORTED_RDS_INSTANCE_ENGINES
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

### CT\.RDS\.PR\.24 example templates<a name="ct-rds-pr-24-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "exampleuser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: "/@\""
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      StorageEncrypted: true
    DeletionPolicy: Delete
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBInstanceSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: RDS instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "exampleuser"}'
        GenerateStringKey: password
        PasswordLength: 22
        ExcludeCharacters: "/@\""
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: 14.2
      DBInstanceClass: db.m5.large
      StorageType: gp2
      AllocatedStorage: 5
      MasterUsername:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::username}}'
      MasterUserPassword:
        Fn::Sub: '{{resolve:secretsmanager:${DBInstanceSecret}::password}}'
      StorageEncrypted: false
    DeletionPolicy: Delete
```

## \[CT\.RDS\.PR\.25\] Require an Amazon RDS database cluster to have logging configured<a name="ct-rds-pr-25-description"></a>

This control checks whether Amazon RDS database clusters have all available log types enabled for export to Amazon CloudWatch Logs\.
+ **Control objective: **Establish logging and monitoring
+ **Implementation: **AWS CloudFormation guard rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::RDS::DBCluster`
+ **AWS CloudFormation guard rule: ** [CT\.RDS\.PR\.25 rule specification](#ct-rds-pr-25-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.RDS\.PR\.25 rule specification](#ct-rds-pr-25-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [CT\.RDS\.PR\.25 example templates](#ct-rds-pr-25-templates) 

**Explanation**

Amazon RDS databases should have relevant logs enabled\. Database logging provides detailed records of requests made to RDS\. Database logs can assist with security and access audits, and they can help to diagnose availability issues\.

**Usage considerations**  
This control applies only to Amazon RDS DB cluster engine types `aurora`, `aurora-mysql`, `aurora-postgresql`, `mysql` and `postgres`\.

### Remediation for rule failure<a name="ct-rds-pr-25-remediation"></a>

Specify `EnableCloudwatchLogsExports` with a list of all supported log types for the Amazon RDS database cluster engine\.

The examples that follow show how to implement this remediation\.

#### Amazon RDS database \(DB\) Cluster \- Example One<a name="ct-rds-pr-25-remediation-1"></a>

Amazon RDS Aurora DB cluster configured with all available log types enabled for export to Amazon CloudWatch Logs\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBCluster": {
        "Type": "AWS::RDS::DBCluster",
        "Properties": {
            "Engine": "aurora",
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBClusterSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBClusterSecret}::password}}"
            },
            "DBSubnetGroupName": {
                "Ref": "DBSubnetGroup"
            },
            "EnableCloudwatchLogsExports": [
                "audit",
                "error",
                "general",
                "slowquery"
            ]
        }
    }
}
```

**YAML example**

```
DBCluster:
  Type: AWS::RDS::DBCluster
  Properties:
    Engine: aurora
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBClusterSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
    DBSubnetGroupName: !Ref 'DBSubnetGroup'
    EnableCloudwatchLogsExports:
      - audit
      - error
      - general
      - slowquery
```

The examples that follow show how to implement this remediation\.

#### Amazon RDS DB Cluster \- Example Two<a name="ct-rds-pr-25-remediation-2"></a>

Amazon RDS Multi\-AZ Postgres DB cluster configured with all available log types enabled for export to Amazon CloudWatch Logs\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "DBCluster": {
        "Type": "AWS::RDS::DBCluster",
        "Properties": {
            "Engine": "aurora",
            "MasterUsername": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBClusterSecret}::username}}"
            },
            "MasterUserPassword": {
                "Fn::Sub": "{{resolve:secretsmanager:${DBClusterSecret}::password}}"
            },
            "DBSubnetGroupName": {
                "Ref": "DBSubnetGroup"
            },
            "EnableCloudwatchLogsExports": [
                "audit",
                "error",
                "general",
                "slowquery"
            ]
        }
    }
}
```

**YAML example**

```
DBCluster:
  Type: AWS::RDS::DBCluster
  Properties:
    Engine: aurora
    MasterUsername: !Sub '{{resolve:secretsmanager:${DBClusterSecret}::username}}'
    MasterUserPassword: !Sub '{{resolve:secretsmanager:${DBClusterSecret}::password}}'
    DBSubnetGroupName: !Ref 'DBSubnetGroup'
    EnableCloudwatchLogsExports:
      - audit
      - error
      - general
      - slowquery
```

### CT\.RDS\.PR\.25 rule specification<a name="ct-rds-pr-25-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   rds_cluster_logging_enabled_check
# 
# Description:
#   This control checks whether Amazon RDS database clusters have all available log types enabled for export to Amazon CloudWatch Logs.
# 
# Reports on:
#   AWS::RDS::DBCluster
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
#       And: The input document does not contain any RDS DB cluster resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster Resource
#       And: 'Engine' is not one of 'aurora', 'aurora-mysql', 'aurora-postgresql', 'mysql' or 'postgres'
#      Then: SKIP
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'Engine' is one of 'aurora', 'aurora-mysql', 'aurora-postgresql', 'mysql' or 'postgres'
#       And: 'EnableCloudwatchLogsExports' has not been specified or has been specified as an empty list
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'Engine' is one of 'aurora', 'aurora-mysql', 'aurora-postgresql', 'mysql' or 'postgres'
#       And: 'EnableCloudwatchLogsExports' has been specified and is a non-empty list
#       And: One or more log types in 'EnableCloudwatchLogsExports' are not supported by the specified 'Engine'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'Engine' is one of 'aurora', 'aurora-mysql', 'aurora-postgresql', 'mysql' or 'postgres'
#       And: 'EnableCloudwatchLogsExports' has been specified and is a non-empty list
#       And: 'EnableCloudwatchLogsExports' does not contain all log types supported by the specified 'Engine'
#      Then: FAIL
#   Scenario: 6
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'Engine' is one of 'aurora', 'aurora-mysql' or 'mysql'
#       And: 'EnableCloudwatchLogsExports' has been specified as a list with all supported log types
#             for the 'Engine' ('audit', 'error', 'general' and 'slowquery')
#      Then: PASS
#   Scenario: 7
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'Engine' is 'aurora-postgresql'
#       And: 'EnableCloudwatchLogsExports' has been specified as a list with all supported log types
#             for the 'Engine' ('postgresql')
#      Then: PASS
#   Scenario: 8
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an RDS DB cluster resource
#       And: 'Engine' is 'postgres'
#       And: 'EnableCloudwatchLogsExports' has been specified as a list with all supported log types
#             for the 'Engine' ('postgresql', 'upgrade')
#      Then: PASS

#
# Constants
#
let RDS_DB_CLUSTER_TYPE = "AWS::RDS::DBCluster"
let INPUT_DOCUMENT = this

let SUPPORTED_RDS_CLUSTER_ENGINES = [
    "aurora", "aurora-mysql", "aurora-postgresql", "mysql", "postgres"
]

let MYSQL_ENGINE_SUBTYPES = [ "aurora", "aurora-mysql", "mysql" ]
let AURORA_POSTGRES_ENGINE_SUBTYPES = [ "aurora-postgresql" ]
let POSTGRES_ENGINE_SUBTYPES = [ "postgres" ]

let MYSQL_SUPPORTED_LOG_TYPES = [ "audit", "error", "general", "slowquery" ]
let AURORA_POSTGRES_SUPPORTED_LOG_TYPES  = [ "postgresql" ]
let POSTGRES_SUPPORTED_LOG_TYPES = [ "postgresql", "upgrade" ]

#
# Assignments
#
let rds_db_clusters = Resources.*[ Type == %RDS_DB_CLUSTER_TYPE ]

#
# Primary Rules
#
rule rds_cluster_logging_enabled_check when is_cfn_template(%INPUT_DOCUMENT)
                                             %rds_db_clusters not empty {
    check(%rds_db_clusters.Properties)
        <<
        [CT.RDS.PR.25]: Require an Amazon RDS database cluster to have logging configured
            [FIX]: Specify 'EnableCloudwatchLogsExports' with a list of all supported log types for the Amazon RDS database cluster engine.
        >>
}

rule rds_cluster_logging_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %RDS_DB_CLUSTER_TYPE) {
    check(%INPUT_DOCUMENT.%RDS_DB_CLUSTER_TYPE.resourceProperties)
        <<
        [CT.RDS.PR.25]: Require an Amazon RDS database cluster to have logging configured
            [FIX]: Specify 'EnableCloudwatchLogsExports' with a list of all supported log types for the Amazon RDS database cluster engine.
        >>
}

#
# Parameterized Rules
#
rule check(rds_db_cluster) {
    %rds_db_cluster [
        filter_engine(this)
    ] {
        # Scenario 3
        EnableCloudwatchLogsExports exists
        check_is_list_and_not_empty(EnableCloudwatchLogsExports)

        # Scenario 4 and 6
        when Engine IN %MYSQL_ENGINE_SUBTYPES {
            %MYSQL_SUPPORTED_LOG_TYPES.* IN EnableCloudwatchLogsExports[*]
            EnableCloudwatchLogsExports.* IN %MYSQL_SUPPORTED_LOG_TYPES[*]
        }

        # Scenario 4 and 7
        when Engine IN %AURORA_POSTGRES_ENGINE_SUBTYPES {
            %AURORA_POSTGRES_SUPPORTED_LOG_TYPES.* IN EnableCloudwatchLogsExports[*]
            EnableCloudwatchLogsExports.* IN %AURORA_POSTGRES_SUPPORTED_LOG_TYPES[*]
        }

        # Scenario 4 and 8
        when Engine IN %POSTGRES_ENGINE_SUBTYPES {
            %POSTGRES_SUPPORTED_LOG_TYPES.* in EnableCloudwatchLogsExports[*]
            EnableCloudwatchLogsExports.* IN %POSTGRES_SUPPORTED_LOG_TYPES[*]
        }
    }
}

rule filter_engine(db_properties) {
    %db_properties {
        # Scenario 2
        Engine exists
        Engine in %SUPPORTED_RDS_CLUSTER_ENGINES
    }
}

#
# Utility Rules
#
rule check_is_list_and_not_empty(value) {
    %value {
        this is_list
        this not empty
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

### CT\.RDS\.PR\.25 example templates<a name="ct-rds-pr-25-templates"></a>

You can view examples of the PASS and FAIL test artifacts for the AWS Control Tower proactive controls\.

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DBCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      MasterUsername: exampleusername
      MasterUserPassword: example-password
      DBSubnetGroupName: example-db-subnet-group
      Engine: aurora
      EnableCloudwatchLogsExports:
        - audit
        - error
        - general
        - slowquery
```

PASS Example \- Use this template to verify a compliant resource creation\.

```
Resources:
  DBCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      DBClusterInstanceClass: db.m6gd.large
      MasterUsername: exampleusername
      MasterUserPassword: example-password
      DBSubnetGroupName: example-db-subnet-group
      Engine: postgres
      AllocatedStorage: 100
      StorageType: io1
      Iops: 3000
      EnableCloudwatchLogsExports:
        - postgresql
        - upgrade
```

FAIL Example \- Use this template to verify that the control prevents non\-compliant resource creation\.

```
Resources:
  DBCluster:
    Type: AWS::RDS::DBCluster
    Properties:
      MasterUsername: exampleusername
      MasterUserPassword: example-password
      DBSubnetGroupName: example-db-subnet-group
      Engine: aurora
```