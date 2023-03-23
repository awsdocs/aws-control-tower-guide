# The ‘alfred’ helper and the AWS CloudFormation parameter files<a name="alfred-helper"></a>

 CfCT provides you with a mechanism known as the *alfred* helper to get the value for an [SSM Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) key that's defined in the AWS CloudFormation template\.  Using the *alfred* helper, you can use values that are stored in the SSM Parameter Store and without updating the AWS CloudFormation template\. For more information, see [What is an AWS CloudFormation template?](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/gettingstarted.templatebasics.html#gettingstarted.templatebasics.what) in the *AWS CloudFormation User Guide*\. 

**Important**  
 The *alfred* helper has two limitations\. Parameters are available only in the home region of the AWS Control Tower management account\. As a best practice, consider working with values that don't change from stack instance to stack instance\. When the 'alfred' helper retreives parameters, it chooses a random stack instance from the stack set that exports the variable\. 

## Example<a name="w32aac24c37c15c13b7"></a>

 Suppose that you have two AWS CloudFormation stack sets\. *Stack set 1* has one stack instance and deploys to one account in one Region\. It creates an Amazon VPC and subnets in an availability zone, and the `VPC ID` and `subnet ID` must be passed into *stack set 2* as parameter values\. Before the `VPC ID` and `subnet ID` can be passed to *stack set 2*, the `VPC ID` and `subnet ID` must be stored in *stack set 1* using `AWS:::SSM::Parameter`\. For more information, see [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ssm-parameter.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ssm-parameter.html) in the *AWS CloudFormation User Guide*\. 

**AWS CloudFormation stack set 1:**

 In the following snippet, the *alfred* helper can gets value for the `VPC ID` and `subnet ID` from the parameter store and pass them as input to the StackSet state machine\. 

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

**AWS CloudFormation stack set 2:**

 The snippet shows the parameters that are specified in the AWS CloudFormation stack 2 `manifest.yaml` file\. 

```
parameters:
      - parameter_key: VpcId
        parameter_value: $[alfred_ssm_/stack_1/vpc/id]
      - parameter_key: SubnetId
        parameter_value: $[alfred_ssm_/stack_1/subnet/id]
```

**AWS CloudFormation stack set 2\.1:**

 The snippet shows that you can list `alfred_ssm` properties to support parameters of type *CommaDelimitedList*\. For more information, see [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html#parameters-section-structure-properties-type](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html#parameters-section-structure-properties-type) in the *AWS CloudFormation User Guide*\. 

```
parameters:
      - parameter_key: VpcId # Type: String
        parameter_value: $[alfred_ssm_/stack_1/vpc/id']
      - parameter_key: SubnetId # Type: String
        parameter_value: $[ alfred_ssm_/stack_1/subnet/id']
      - parameter_key: AvailablityZones # Type: CommaDelimitedList
        parameter_value:   - "$[alfred_ssm_/availability_zone_1]"  - "$[alfred_ssm_/availability_zone_2]"
```