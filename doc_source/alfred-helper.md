# The ‘alfred’ helper and the AWS CloudFormation parameter files<a name="alfred-helper"></a>

CfCT provides a mechanism to get the value for an [SSM Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) key defined in the AWS CloudFormation parameter JSON file \(\.json\)\. This mechanism allows you to use values stored in the parameter store without updating the AWS CloudFormation template\.

For example, suppose that AWS CloudFormation **stack 1** creates an Amazon VPC and subnets in an availability zone\. The `VPC ID` and `subnet ID` must be passed into AWS CloudFormation **stack 2** as parameter values\. 

You can store the `VPC ID` and the `subnet ID` into the SSM parameter store, using the [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ssm-parameter.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ssm-parameter.html) resource in **stack 1**\. As shown in the example that follows, the *alfred* helper can get the values from the parameter store and pass them into the StackSet state machine, as input\.

AWS CloudFormation **stack 1** snippet:

```
VpcIdParameter:
    Type: AWS::SSM::Parameter
    Properties:
      Name: '/stack_1/vpc/id'
      Description: Contains the VPC id
      Type: String
      Value: !Ref MyVpc

SubnetIdParameter:
    Type: AWS::SSM::Parameter
    Properties:
      Name: '/stack_1/subnet/id'
      Description: Contains the subnet id
      Type: String
      Value: !Ref MySubnet
```

The following example shows the parameters for AWS CloudFormation **stack 2** specified in the `manifest.yaml` file\.

```
parameters:
      - parameter_key: VpcId
        parameter_value: $[alfred_ssm_/stack_1/vpc/id']
      - parameter_key: SubnetId
        parameter_value: $[ alfred_ssm_/stack_1/subnet/id’]
```

Starting with version 2\.1, you can use a list of `alfred_ssm` properties under `parameter_value` to support the **CommaDelimitedList** type of AWS CloudFormation parameter\. For example:

```
parameters:
      - parameter_key: VpcId # Type: String
        parameter_value: $[alfred_ssm_/stack_1/vpc/id']
      - parameter_key: SubnetId # Type: String
        parameter_value: $[ alfred_ssm_/stack_1/subnet/id']
      - parameter_key: AvailablityZones # Type: CommaDelimitedList
        parameter_value:   - "$[alfred_ssm_/availability_zone_1]"  - "$[alfred_ssm_/availability_zone_2]"
```