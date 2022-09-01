# Check the AFT version<a name="check-aft-version"></a>

You can check your deployed AFT version by querying the AWS SSM Parameter Store key:

```
/aft/config/aft/version
```

If you use the registry method, you can pin the version\.

```
module "control_tower_account_factory" {
  source  = "aws-ia/control_tower_account_factory/aws"
  version = "1.3.2"
  # insert the 6 required variables here
}
```

You can view more information about AFT versions in the [AFT repository](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/tree/main)\.