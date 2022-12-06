# Amazon Elastic File System controls<a name="efs-rules"></a>

**Topics**
+ [\[CT\.ELASTICFILESYSYSTEM\.PR\.1\] Require an Amazon EFS file system to encrypt file data at rest using AWS KMS](#ct-elasticfilesysystem-pr-1-description)
+ [\[CT\.ELASTICFILESYSYSTEM\.PR\.2\] Require an Amazon EFS volume to have an automated backup plan](#ct-elasticfilesysystem-pr-2-description)
+ [\[CT\.ELASTICFILESYSYSTEM\.PR\.3\] Require Amazon EFS access points to have a root directory](#ct-elasticfilesysystem-pr-3-description)
+ [\[CT\.ELASTICFILESYSYSTEM\.PR\.4\] Require Amazon EFS access points to enforce a user identity](#ct-elasticfilesysystem-pr-4-description)

## \[CT\.ELASTICFILESYSYSTEM\.PR\.1\] Require an Amazon EFS file system to encrypt file data at rest using AWS KMS<a name="ct-elasticfilesysystem-pr-1-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether an Amazon Elastic File System \(Amazon EFS\) file system is configured to encrypt file data using AWS KMS\.
+ **Control objective: **Encrypt data at rest
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EFS::FileSystem`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICFILESYSYSTEM\.PR\.1 rule specification](#ct-elasticfilesysystem-pr-1-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICFILESYSYSTEM\.PR\.1 rule specification](#ct-elasticfilesysystem-pr-1-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.ELASTICFILESYSYSTEM.PR.1) 

**Explanation**

For an added layer of security for your sensitive data in Amazon EFS, you should create encrypted file systems\. Amazon EFS supports encryption for file systems at rest\. You can enable encryption of data at rest when you create an Amazon EFS file system\.

**Usage considerations**  
This control requires only the `Encrypted` property to be set to `true`, and it does not require the `KmsKeyId` property to be provided\.
If the `KmsKeyId` property is not provided, the default AWS KMS key for Amazon EFS, `/aws/elasticfilesystem`, is used to protect the encrypted file system\.

### Remediation for rule failure<a name="ct-elasticfilesysystem-pr-1-remediation"></a>

Set `Encrypted` to `true` and optionally set `KmsKeyId` to a valid AWS KMS key identifier\.

The examples that follow show how to implement this remediation\.

#### Amazon EFS File System \- Example One<a name="ct-elasticfilesysystem-pr-1-remediation-1"></a>

Amazon EFS file system configured with encryption enabled, by means of the default AWS KMS key for Amazon EFS\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "EFSFileSystem": {
        "Type": "AWS::EFS::FileSystem",
        "Properties": {
            "Encrypted": true
        }
    }
}
```

**YAML example**

```
EFSFileSystem:
  Type: AWS::EFS::FileSystem
  Properties:
    Encrypted: true
```

The examples that follow show how to implement this remediation\.

#### Amazon EFS File System \- Example Two<a name="ct-elasticfilesysystem-pr-1-remediation-2"></a>

Amazon EFS file system configured with encryption enabled, by means of a customer\-managed AWS KMS key\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "EFSFileSystem": {
        "Type": "AWS::EFS::FileSystem",
        "Properties": {
            "Encrypted": true,
            "KmsKeyId": {
                "Ref": "KMSKey"
            }
        }
    }
}
```

**YAML example**

```
EFSFileSystem:
  Type: AWS::EFS::FileSystem
  Properties:
    Encrypted: true
    KmsKeyId: !Ref 'KMSKey'
```

### CT\.ELASTICFILESYSYSTEM\.PR\.1 rule specification<a name="ct-elasticfilesysystem-pr-1-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# Rule Identifier:
#   efs_encrypted_check
# 
# Description:
#   This control checks whether an Amazon Elastic File System (Amazon EFS) file system is configured to encrypt file data using AWS KMS.
# 
# Reports on:
#   AWS::EFS::FileSystem
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
#       And: The input document does not contain any Amazon EFS file system resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EFS file system resource
#       And: 'Encrypted' is not present
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EFS file system resource
#       And: 'Encrypted' is present and set to bool(false)
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EFS file system resource
#       And: 'Encrypted' is present and set to bool(true)
#      Then: PASS

#
# Constants
#
let RESOURCE_TYPE = "AWS::EFS::FileSystem"
let INPUT_DOCUMENT = this

#
# Assignments
#
let efs_file_systems = Resources.*[ Type == %RESOURCE_TYPE ]

#
# Primary Rules
#
rule efs_encrypted_check when is_cfn_template(%INPUT_DOCUMENT)
                              %efs_file_systems not empty {
    check(%efs_file_systems.Properties)
        <<
        [CT.ELASTICFILESYSYSTEM.PR.1]: Require an Amazon EFS file system to encrypt file data at rest using AWS KMS
        [FIX]: Set 'Encrypted' to 'true' and optionally set 'KmsKeyId' to a valid AWS KMS key identifier.
        >>
}

rule efs_encrypted_check when is_cfn_hook(%INPUT_DOCUMENT, %RESOURCE_TYPE) {
    check(%INPUT_DOCUMENT.%RESOURCE_TYPE.resourceProperties)
        <<
        [CT.ELASTICFILESYSYSTEM.PR.1]: Require an Amazon EFS file system to encrypt file data at rest using AWS KMS
        [FIX]: Set 'Encrypted' to 'true' and optionally set 'KmsKeyId' to a valid AWS KMS key identifier.
        >>
}

#
# Parameterized Rules
#
rule check(efs_file_systems) {
    %efs_file_systems {
        # Scenario 2
        Encrypted exists
        # Scenario 3 and 4
        Encrypted == true
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

## \[CT\.ELASTICFILESYSYSTEM\.PR\.2\] Require an Amazon EFS volume to have an automated backup plan<a name="ct-elasticfilesysystem-pr-2-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether your Amazon Elastic File System \(Amazon EFS\) file system has been configured with automatic backups through AWS Backup\.
+ **Control objective: **Improve resiliency
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EFS::FileSystem`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICFILESYSYSTEM\.PR\.2 rule specification](#ct-elasticfilesysystem-pr-2-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICFILESYSYSTEM\.PR\.2 rule specification](#ct-elasticfilesysystem-pr-2-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.ELASTICFILESYSYSTEM.PR.2) 

**Explanation**

Including Amazon EFS file systems in the backup plans helps you to protect your data from deletion and data loss\.

### Remediation for rule failure<a name="ct-elasticfilesysystem-pr-2-remediation"></a>

Enable automatic backups by setting `BackupPolicy.Status` to `ENABLED`\.

The examples that follow show how to implement this remediation\.

#### Amazon EFS File System \- Example<a name="ct-elasticfilesysystem-pr-2-remediation-1"></a>

Amazon EFS file system configured with automatic backups enabled\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "EFSFileSystem": {
        "Type": "AWS::EFS::FileSystem",
        "Properties": {
            "BackupPolicy": {
                "Status": "ENABLED"
            }
        }
    }
}
```

**YAML example**

```
EFSFileSystem:
  Type: AWS::EFS::FileSystem
  Properties:
    BackupPolicy:
      Status: ENABLED
```

### CT\.ELASTICFILESYSYSTEM\.PR\.2 rule specification<a name="ct-elasticfilesysystem-pr-2-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   efs_automatic_backups_enabled_check
# 
# Description:
#   This control checks whether your Amazon Elastic File System (Amazon EFS) file system has been configured with automatic backups through AWS Backup.
# 
# Reports on:
#   AWS::EFS::FileSystem
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
#       And: The input document does not contain any Amazon EFS file system resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EFS file system resource
#       And: 'BackupPolicy' is not present
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EFS file system resource
#       And: 'BackupPolicy' is present and 'Status' is set to 'DISABLED'
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EFS file system resource
#       And: 'BackupPolicy' is present and 'Status' is set to 'ENABLED'
#      Then: PASS

#
# Constants
#
let RESOURCE_TYPE = "AWS::EFS::FileSystem"
let INPUT_DOCUMENT = this

#
# Assignments
#
let efs_file_systems = Resources.*[ Type == %RESOURCE_TYPE ]

#
# Primary Rules
#
rule efs_automatic_backups_enabled_check when is_cfn_template(this)
                                              %efs_file_systems not empty {
    check(%efs_file_systems.Properties)
        <<
        [CT.ELASTICFILESYSYSTEM.PR.2]: Require an Amazon EFS volume to have an automated backup plan
        [FIX]: Enable automatic backups by setting 'BackupPolicy.Status' to 'ENABLED'.
        >>

}

rule efs_automatic_backups_enabled_check when is_cfn_hook(%INPUT_DOCUMENT, %RESOURCE_TYPE) {
    check(%INPUT_DOCUMENT.%RESOURCE_TYPE.resourceProperties)
        <<
        [CT.ELASTICFILESYSYSTEM.PR.2]: Require an Amazon EFS volume to have an automated backup plan
        [FIX]: Enable automatic backups by setting 'BackupPolicy.Status' to 'ENABLED'.
        >>
}

#
# Parameterized Rules
#
rule check(efs_file_systems) {
    %efs_file_systems {
        # Scenario 2
        BackupPolicy exists
        BackupPolicy is_struct
        BackupPolicy {
            # Scenario 3 and 4
            Status exists
            Status == "ENABLED"
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
```

## \[CT\.ELASTICFILESYSYSTEM\.PR\.3\] Require Amazon EFS access points to have a root directory<a name="ct-elasticfilesysystem-pr-3-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered ](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether your Amazon Elastic File System \(Amazon EFS\) access points are configured to enforce a root directory\.
+ **Control objective: **Enforce least privilege
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EFS::AccessPoint`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICFILESYSYSTEM\.PR\.3 rule specification](#ct-elasticfilesysystem-pr-3-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICFILESYSYSTEM\.PR\.3 rule specification](#ct-elasticfilesysystem-pr-3-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.ELASTICFILESYSYSTEM.PR.3) 

**Explanation**

When you enforce a root directory, the NFS client at the access point uses the root directory configured on the access point, instead of the file system's root directory\. Enforcing a root directory for an access point helps restrict data access by ensuring that users of the access point can reach only the files of the specified subdirectory\.

### Remediation for rule failure<a name="ct-elasticfilesysystem-pr-3-remediation"></a>

Provide a `RootDirectory.Path` configuration with a value for `Path` that does not equal `/`\.

The examples that follow show how to implement this remediation\.

#### Amazon EFS Access Point \- Example<a name="ct-elasticfilesysystem-pr-3-remediation-1"></a>

Amazon EFS access point configured with a root directory set to a specific subdirectory\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "EFSAccessPoint": {
        "Type": "AWS::EFS::AccessPoint",
        "Properties": {
            "FileSystemId": {
                "Ref": "EFSFileSystem"
            },
            "RootDirectory": {
                "Path": "/dir1/child1"
            }
        }
    }
}
```

**YAML example**

```
EFSAccessPoint:
  Type: AWS::EFS::AccessPoint
  Properties:
    FileSystemId: !Ref 'EFSFileSystem'
    RootDirectory:
      Path: /dir1/child1
```

### CT\.ELASTICFILESYSYSTEM\.PR\.3 rule specification<a name="ct-elasticfilesysystem-pr-3-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# 
# Rule Identifier:
#   efs_access_point_enforce_root_directory_check
# 
# Description:
#   This control checks whether your Amazon Elastic File System (Amazon EFS) access points are configured to enforce a root directory.
# 
# Reports on:
#   AWS::EFS::AccessPoint
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
#       And: The input document does not contain any Amazon EFS access point resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EFS access point resource
#       And: 'RootDirectory' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EFS access point resource
#       And: 'RootDirectory' has been provided
#       And: 'Path' within 'RootDirectory' has not been provided or has been provided with an empty string value
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EFS access point resource
#       And: 'RootDirectory' has been provided
#       And: 'Path' within 'RootDirectory' been provided with a value of '/'
#      Then: FAIL
#   Scenario: 5
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EFS access point resource
#       And: 'RootDirectory' has been provided
#       And: 'Path' within 'RootDirectory' been provided with a non-empty string value not equal to '/'
#      Then: PASS

#
# Constants
#
let EFS_ACCESS_POINT_TYPE = "AWS::EFS::AccessPoint"
let INPUT_DOCUMENT = this

#
# Assignments
#
let efs_access_points = Resources.*[ Type == %EFS_ACCESS_POINT_TYPE ]

#
# Primary Rules
#
rule efs_access_point_enforce_root_directory_check when is_cfn_template(%INPUT_DOCUMENT)
                                                        %efs_access_points not empty {
    check(%efs_access_points.Properties)
        <<
        [CT.ELASTICFILESYSYSTEM.PR.3]: Require Amazon EFS access points to have a root directory
        [FIX]: Provide a 'RootDirectory.Path' configuration with a value for 'Path' that does not equal '/'.
        >>
}

rule efs_access_point_enforce_root_directory_check when is_cfn_hook(%INPUT_DOCUMENT, %EFS_ACCESS_POINT_TYPE) {
    check(%INPUT_DOCUMENT.%EFS_ACCESS_POINT_TYPE.resourceProperties)
        <<
        [CT.ELASTICFILESYSYSTEM.PR.3]: Require Amazon EFS access points to have a root directory
        [FIX]: Provide a 'RootDirectory.Path' configuration with a value for 'Path' that does not equal '/'.
        >>
}

#
# Parameterized Rules
#
rule check(efs_access_points) {
    %efs_access_points {
        # Scenario 2
        RootDirectory exists
        RootDirectory {
            # Scenario 3,4 and 5
            Path exists
            check_is_string_and_not_empty(Path)
            Path != "/"
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
```

## \[CT\.ELASTICFILESYSYSTEM\.PR\.4\] Require Amazon EFS access points to enforce a user identity<a name="ct-elasticfilesysystem-pr-4-description"></a>


|  | 
| --- |
| Comprehensive controls management is available as a preview in all [AWS Regions where AWS Control Tower is offered](https://docs.aws.amazon.com/controltower/latest/userguide/region-how.html)\. These enhanced control capabilities reduce the time required to define and manage the controls you need, to help you meet common control objectives and industry regulations\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing/)\. | 

This control checks whether your Amazon Elastic File System \(Amazon EFS\) access points are configured to enforce a user identity\.
+ **Control objective: **Enforce least privilege
+ **Implementation: **AWS CloudFormation Guard Rule
+ **Control behavior: **Proactive
+ **Resource types: **`AWS::EFS::AccessPoint`
+ **AWS CloudFormation guard rule: ** [CT\.ELASTICFILESYSYSTEM\.PR\.4 rule specification](#ct-elasticfilesysystem-pr-4-rule) 

**Details and examples**
+ For details about the PASS, FAIL, and SKIP behaviors associated with this control, see the: [CT\.ELASTICFILESYSYSTEM\.PR\.4 rule specification](#ct-elasticfilesysystem-pr-4-rule) 
+ For examples of PASS and FAIL CloudFormation Templates related to this control, see: [GitHub](https://docs.aws.amazon.com/https://github.com/aws-samples/aws-control-tower-samples/tree/main/samples/CT.ELASTICFILESYSYSTEM.PR.4) 

**Explanation**

Amazon EFS access points are application\-specific entry points into an Amazon EFS file system that make it easier to manage application access to shared datasets\. Access points can enforce a user identity, including the user's POSIX groups, for all file system requests that are made through the access point\. Access points also can enforce a different root directory for the file system, so that clients gain access only to data in the specified directory or its subdirectories\.

### Remediation for rule failure<a name="ct-elasticfilesysystem-pr-4-remediation"></a>

Provide a `PosixUser` configuration with a POSIX user ID \(`Uid`\) and POSIX group ID \(`Gid`\)\.

The examples that follow show how to implement this remediation\.

#### Amazon EFS Access Point \- Example<a name="ct-elasticfilesysystem-pr-4-remediation-1"></a>

Amazon EFS access point configured to enforce a user identity for all file system requests made through the access point\. The example is shown in JSON and in YAML\.

**JSON example**

```
{
    "EFSAccessPoint": {
        "Type": "AWS::EFS::AccessPoint",
        "Properties": {
            "FileSystemId": {
                "Ref": "EFSFileSystem"
            },
            "PosixUser": {
                "Uid": "111",
                "Gid": "222"
            }
        }
    }
}
```

**YAML example**

```
EFSAccessPoint:
  Type: AWS::EFS::AccessPoint
  Properties:
    FileSystemId: !Ref 'EFSFileSystem'
    PosixUser:
      Uid: '111'
      Gid: '222'
```

### CT\.ELASTICFILESYSYSTEM\.PR\.4 rule specification<a name="ct-elasticfilesysystem-pr-4-rule"></a>

```
# ###################################
##       Rule Specification        ##
#####################################
# Rule Identifier:
#   efs_access_point_enforce_user_identity_check
# 
# Description:
#   This control checks whether your Amazon Elastic File System (Amazon EFS) access points are configured to enforce a user identity.
# 
# Reports on:
#   AWS::EFS::AccessPoint
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
#       And: The input document does not contain any Amazon EFS access point resources
#      Then: SKIP
#   Scenario: 2
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EFS access point resource
#       And: 'PosixUser' has not been provided
#      Then: FAIL
#   Scenario: 3
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EFS access point resource
#       And: 'PosixUser' has been provided
#       And: 'Uid' within 'PosixUser' has not been provided or has been provided with an empty string value
#       And: 'Gid' within 'PosixUser' has not been provided or has been provided with an empty string value
#      Then: FAIL
#   Scenario: 4
#     Given: The input document is an AWS CloudFormation or AWS CloudFormation hook document
#       And: The input document contains an Amazon EFS access point resource
#       And: 'PosixUser' has been provided
#       And: 'Uid' within 'PosixUser' has been provided with a non-empty string value
#       And: 'Gid' within 'PosixUser' has been provided with a non-empty string value
#      Then: PASS

#
# Constants
#
let EFS_ACCESS_POINT_TYPE = "AWS::EFS::AccessPoint"
let INPUT_DOCUMENT = this

#
# Assignments
#
let efs_access_points = Resources.*[ Type == %EFS_ACCESS_POINT_TYPE ]

#
# Primary Rules
#
rule efs_access_point_enforce_user_identity_check when is_cfn_template(this)
                                                       %efs_access_points not empty {
    check(%efs_access_points.Properties)
        <<
        [CT.ELASTICFILESYSYSTEM.PR.4]: Require Amazon EFS access points to enforce a user identity
        [FIX]: Provide a 'PosixUser' configuration with a POSIX user ID ('Uid') and POSIX group ID ('Gid').
        >>
}

rule efs_access_point_enforce_user_identity_check when is_cfn_hook(%INPUT_DOCUMENT, %EFS_ACCESS_POINT_TYPE) {
    check(%INPUT_DOCUMENT.%EFS_ACCESS_POINT_TYPE.resourceProperties)
        <<
        [CT.ELASTICFILESYSYSTEM.PR.4]: Require Amazon EFS access points to enforce a user identity
        [FIX]: Provide a 'PosixUser' configuration with a POSIX user ID ('Uid') and POSIX group ID ('Gid').
        >>
}

#
# Parameterized Rules
#
rule check(efs_access_points) {
    %efs_access_points {
        # Scenario 2
        PosixUser exists
        PosixUser {
            # Scenario 3 and 4
            Uid exists
            check_is_string_and_not_empty(Uid)
            Gid exists
            check_is_string_and_not_empty(Gid)
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
```