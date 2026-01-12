# Delete EC2 Instance Using Terraform

During the migration process, several resources were created under the AWS account. Some of these test resources are no longer needed at the moment, so we need to clean them up temporarily. One such instance is currently unused and should be deleted.

1) Delete the ec2 instance named `datacenter-ec2` present in `us-east-1` region using terraform. Make sure to keep the provisioning code, as we might need to provision this instance again later.

2) Before submitting your task, make sure instance is in terminated state.

The Terraform working directory is `/home/bob/terraform`. Create the `main.tf` file (do not create a different `.tf` file) to accomplish this task.

**`/home/bob/terraform/main.tf`** 

```HCL
# Provision EC2 instance
resource "aws_instance" "ec2" {
  ami           = "ami-0c101f26f147fa7fd"
  instance_type = "t2.micro"
  vpc_security_group_ids = [
    "sg-aaeca30a146fb5a9d"
  ]

  tags = {
    Name = "datacenter-ec2"
  }
}
```

**Remove the instance from Terraform state:**

```sh
terraform state rm aws_instance.ec2
```

**Terminate the EC2 instance manually via AWS CLI:**

```sh
aws ec2 terminate-instances \
    --instance-ids $(aws ec2 describe-instances \
      --filters "Name=tag:Name,Values=datacenter-ec2" \
      --query "Reservations[*].Instances[*].InstanceId" --output text) \
    --region us-east-1
```

**Check the instance is terminated:**

```sh
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=datacenter-ec2" \
    --query "Reservations[*].Instances[*].State.Name" --output text \
    --region us-east-1
```

**Expected:** `terminated`
