This file is one of the most important parts of your project. This is **Ansible Dynamic Inventory using AWS**.

File:

```yaml
plugin: amazon.aws.aws_ec2

regions:
  - ap-south-1

filters:
  tag:Environment: dev
  instance-state-name: running

compose:
  ansible_host: public_ip_address

keyed_groups:
  - key: tags.Environment
    prefix: env
```

This tells Ansible:

> “Don't use static inventory. Connect to AWS and automatically discover EC2 instances.”

---

# What problem this solves

Normally inventory looks like:

```ini
[web]
10.0.0.10
10.0.0.11
10.0.0.12
```

Problem:

* Every new VM → update inventory manually
* Deleted VM → remove manually

Your solution:

```text
AWS → Dynamic Inventory → Ansible
```

Inventory updates automatically.

---

# Line by line

---

## 1. Plugin

```yaml
plugin: amazon.aws.aws_ec2
```

Meaning:

Use AWS inventory plugin.

Instead of:

```text
inventory.ini
```

Ansible calls AWS APIs.

Internally:

```text
Ansible
 ↓
boto3
 ↓
AWS EC2 API
 ↓
Fetch instances
```

This is why you installed:

```bash
pip install boto3 botocore
```

Interview:

> I used the AWS EC2 dynamic inventory plugin to discover instances automatically.

---

## 2. Region

```yaml
regions:
  - ap-south-1
```

Meaning:

Only scan:

```text
Mumbai region
```

If omitted:

Ansible may search multiple regions.

Interview:

> Inventory discovery was restricted to a specific AWS region for performance and control.

---

## 3. Filters

```yaml
filters:
  tag:Environment: dev
  instance-state-name: running
```

This is very important.

Meaning:

Fetch only:

```text
Environment=dev
AND
running state
```

Example:

EC2 list:

| Instance | Environment | State   |
| -------- | ----------- | ------- |
| VM1      | dev         | running |
| VM2      | prod        | running |
| VM3      | dev         | stopped |

Result:

```text
VM1 only
```

Interview:

> I filtered inventory using AWS tags and instance status to target only active development VMs.

---

## 4. Compose

```yaml
compose:
  ansible_host: public_ip_address
```

Meaning:

Set connection target.

Normally AWS returns:

```text
private_ip
public_ip
dns
instance_id
```

You selected:

```text
public_ip_address
```

Example:

AWS:

```text
public_ip=54.22.33.44
```

Ansible internally becomes:

```text
ansible_host=54.22.33.44
```

SSH:

```bash
ssh ubuntu@54.22.33.44
```

Interview:

> I configured Ansible to connect using EC2 public IP addresses.

---

## 5. Keyed Groups

```yaml
keyed_groups:
  - key: tags.Environment
    prefix: env
```

This auto-creates inventory groups.

Example EC2:

```text
VM1 → Environment=dev
VM2 → Environment=prod
```

Generated inventory:

```ini
env_dev
env_prod
```

Then playbook can run:

```yaml
hosts: env_dev
```

Instead of:

```yaml
hosts: 54.22.11.22
```

Interview:

> Dynamic groups were generated using AWS tags, which allowed environment-based execution.

---

# Command used

You ran:

```bash
ansible-inventory -i inventory/aws_ec2.yaml --graph
```

Example output:

```text
@all
 └── @env_dev
      ├── 13.126.x.x
      ├── 52.66.x.x
```

This validates inventory.

---

# Complete execution flow

```text
Playbook
   ↓
aws_ec2.yaml
   ↓
AWS API
   ↓
Filter dev VMs
   ↓
Create inventory
   ↓
SSH
   ↓
Execute health checks
```

---

# 30-second interview explanation

> I implemented Ansible Dynamic Inventory using the `amazon.aws.aws_ec2` plugin. Instead of maintaining static inventory files, Ansible automatically queried AWS and discovered running EC2 instances in the Mumbai region based on tags. I used filtering to select only development VMs and generated dynamic host groups to simplify environment-based execution.

This file shows interviewers you understand **AWS + Inventory + Ansible internals**, not just playbooks.

Send the next file.
