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