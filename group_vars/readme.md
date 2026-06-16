Good. This is your **Ansible variables file**.

File:

```yaml
# group_vars/all.yaml

smtp_server: "smtp.gmail.com"
smtp_port: 587
email_user: "--"
email_pass: "--"
alert_recipient: "--"
```

This file is used to store **common variables available to all hosts and playbooks**.

---

# What is `group_vars/all.yaml`?

In Ansible:

```text
group_vars/
   └── all.yaml
```

means:

> Variables inside `all.yaml` are automatically loaded for **all inventory hosts**.

So instead of hardcoding SMTP details in playbooks, you centralized them.

---

# Line by line

### SMTP Server

```yaml
smtp_server: "smtp.gmail.com"
```

SMTP = Simple Mail Transfer Protocol.

Used for:

```text
Ansible
   ↓
Connect SMTP server
   ↓
Send alert email
```

Here:

```text
smtp.gmail.com
```

is Gmail SMTP endpoint.

---

### SMTP Port

```yaml
smtp_port: 587
```

Port used for:

```text
TLS encrypted SMTP
```

Common ports:

| Port | Purpose    |
| ---- | ---------- |
| 25   | SMTP       |
| 465  | SMTPS      |
| 587  | SMTP + TLS |

You used:

```text
587
```

because secure email transmission.

---

### Email Username

```yaml
email_user: "--"
```

Sender email account.

Example:

```text
monitoring@gmail.com
```

Used for authentication.

---

### Email Password

```yaml
email_pass: "--"
```

Password or App Password.

Used when Ansible authenticates.

Usually consumed like:

```yaml
mail:
  host: "{{ smtp_server }}"
  port: "{{ smtp_port }}"
  username: "{{ email_user }}"
  password: "{{ email_pass }}"
```

---

### Recipient

```yaml
alert_recipient: "--"
```

Who receives monitoring alerts.

Example:

```text
devops-team@company.com
```

---

# Why use `group_vars` instead of hardcoding?

Without variables:

```yaml
mail:
 host: smtp.gmail.com
 port: 587
 user: admin@gmail.com
```

Bad:

* duplicate values
* difficult maintenance
* secrets exposure

With `group_vars`:

```yaml
mail:
 host: "{{ smtp_server }}"
```

Reusable.

---

# Real project flow

```text
Playbook
   ↓
group_vars/all.yaml
   ↓
Read SMTP values
   ↓
Generate email
   ↓
Send alert
```

---

# Interview explanation (say this)

> I used `group_vars/all.yaml` to centralize SMTP configuration for alert notifications. Instead of hardcoding email credentials inside playbooks, variables were externalized to improve maintainability and reusability. These values were consumed by Ansible tasks to send health monitoring alerts.

---

# Possible interviewer follow-up

### Q: Why not store password in YAML?

Expected answer:

> For production, secrets should be stored using Ansible Vault or a secret management solution instead of plaintext variables.

Example:

```bash
ansible-vault encrypt group_vars/all.yaml
```

This answer shows production awareness.

Send the next file.
