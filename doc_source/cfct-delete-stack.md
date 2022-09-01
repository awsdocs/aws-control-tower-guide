# Delete a stack set<a name="cfct-delete-stack"></a>

You can delete a stack set if you've enabled stack set deletion in the manifest file\. By default, the `enable_stack_set_deletion` parameter is set to `false`\. In this configuration, no action is taken to delete the associated stack set when a resource is removed from the CfCT manifest file\.

If you change the value of `enable_stack_set_deletion` to `true` in the manifest file, CfCT deletes the stack set and all of its resources when you remove an associated resource from the manifest file\.

This capability is supported in **v2** of the manifest file\.

**Important**  
When you initially set the value of `enable_stack_set_deletion` to `true`, the next time you invoke CfCT, **ALL** resources that begin with the prefix `CustomControlTower-`, which have the associated key tag `Key:AWS_Solutions, Value: CustomControlTowerStackSet`, and which are not declared in the manifest file, are staged for deletion\.

Here's an example of how to set this parameter in a `manifest.yaml` file:

```
version: 2021-03-15
region: us-east-1
enable_stack_set_deletion: true   #New opt-in functionality


resources: 
  - name: demo_resource_1
    resource_file: s3://demo_bucket/resource.template
    deployment_targets:
      accounts:
        - 012345678912
    deploy_method: stack_set
    ...
    regions:
    - us-east-1
    - us-west-2

  - name: demo_resource_2
    resource_file: s3://demo_bucket/resource.template
    deployment_targets:
      accounts:
        - 012345678912
    deploy_method: stack_set
    ...
    regions: 
    - us-east-1
    - eu-north-1
```