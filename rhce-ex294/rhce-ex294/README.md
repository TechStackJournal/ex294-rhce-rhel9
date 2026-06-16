# RHCE EX294 Practice Lab

A structured study repository for the Red Hat Certified Engineer exam (EX294) — Ansible automation on RHEL 9. The repo mirrors the exact directory layout expected on the exam control node, and each question has its own doc covering the question text, approach, solution, how to run it, verification steps, and common traps.

---

## Lab environment

| Host | Role | IP |
|---|---|---|
| `control.lab.example.com` | Ansible control node | 172.25.250.254 |
| `node1.lab.example.com` | Managed node — group: `dev` | 172.25.250.9 |
| `node2.lab.example.com` | Managed node — group: `test` | 172.25.250.10 |
| `node3.lab.example.com` | Managed node — groups: `prod`, `webservers` | 172.25.250.11 |
| `node4.lab.example.com` | Managed node — groups: `prod`, `webservers` | 172.25.250.12 |
| `node5.lab.example.com` | Managed node — group: `balancers` | 172.25.250.13 |

SSH user: `student`

---

## Repository structure

```
rhce-ex294/
├── ansible.cfg                  # Control node config (inventory, roles, collections, vault)
├── inventory/
│   └── hosts                    # Static inventory with all groups
├── playbooks/                   # One playbook per exam question
│   ├── yum_repo.yml             # Q02
│   ├── packages.yml             # Q03
│   ├── selinux.yml              # Q04
│   ├── apache.yml               # Q07
│   ├── roles.yml                # Q08
│   ├── lv.yml                   # Q09
│   ├── hosts.yml                # Q10 (do not modify)
│   ├── issue.yml                # Q11
│   ├── webcontent.yml           # Q12
│   ├── hwreport.yml             # Q13
│   ├── users.yml                # Q15
│   └── cron.yml                 # Q17
├── roles/
│   ├── requirements.yml         # Q06 — Galaxy roles (balancer, phpinfo)
│   └── apache/                  # Q07 — Custom apache role
│       ├── tasks/main.yml
│       └── templates/index.html.j2
├── collections/
│   └── requirements.yml         # Q05 — Collection tarballs
├── templates/
│   └── hosts.j2                 # Q10 — Jinja2 hosts template
└── docs/
    ├── q01.md  Q01 Install and configure Ansible
    ├── q02.md  Q02 Create yum repositories
    ├── q03.md  Q03 Install packages
    ├── q04.md  Q04 Use the SELinux role
    ├── q05.md  Q05 Install a collection
    ├── q06.md  Q06 Install roles using Ansible Galaxy
    ├── q07.md  Q07 Create and use the apache role
    ├── q08.md  Q08 Use roles from Ansible Galaxy
    ├── q09.md  Q09 Create and use a logical volume
    ├── q10.md  Q10 Generate a hosts file
    ├── q11.md  Q11 Modify file content
    ├── q12.md  Q12 Create a web content directory
    ├── q13.md  Q13 Generate a hardware report
    ├── q14.md  Q14 Create a password vault
    ├── q15.md  Q15 Create user accounts
    ├── q16.md  Q16 Rekey an Ansible vault
    └── q17.md  Q17 Configure a cron job
```

---

## How to use this repo

### Recommended study flow

1. **Read the question** — open `docs/qXX.md` and read the Question section only
2. **Attempt it yourself** — write the playbook or config from memory
3. **Check the approach** — read the Approach section for strategy hints
4. **Compare your solution** — read the Solution section
5. **Run it** — use the How to run command
6. **Verify** — run the verification commands
7. **Read the traps** — even if you got it right, review the Common exam traps

### Initial setup on the control node

```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/rhce-ex294.git /home/student/ansible
cd /home/student/ansible

# Install packages (Q01)
sudo dnf -y install ansible-automation-platform-common.noarch ansible-navigator

# Install rhel-system-roles RPM (Q04)
sudo dnf -y install rhel-system-roles

# Install Galaxy roles (Q06)
ansible-galaxy install -r roles/requirements.yml

# Install collections (Q05)
ansible-galaxy collection install -r collections/requirements.yml -p ./collections/

# Create the vault password file (Q14)
echo "whenyouwishuponastar" > /home/student/ansible/secret.txt
chmod 600 secret.txt

# Smoke test
ansible all -m ping
```

---

## Running individual questions

| Question | Command |
|---|---|
| Q02 yum repos | `ansible-navigator run playbooks/yum_repo.yml -m stdout` |
| Q03 packages | `ansible-navigator run playbooks/packages.yml -m stdout` |
| Q04 selinux | `ansible-playbook playbooks/selinux.yml` ⚠️ use ansible-playbook |
| Q07 apache | `ansible-navigator run playbooks/apache.yml -m stdout` |
| Q08 roles | `ansible-navigator run playbooks/roles.yml -m stdout` |
| Q09 lv | `ansible-navigator run playbooks/lv.yml -m stdout` |
| Q10 hosts | `ansible-navigator run playbooks/hosts.yml -m stdout` |
| Q11 issue | `ansible-navigator run playbooks/issue.yml -m stdout` |
| Q12 webcontent | `ansible-navigator run playbooks/webcontent.yml -m stdout` |
| Q13 hwreport | `ansible-navigator run playbooks/hwreport.yml -m stdout` |
| Q15 users | `ansible-navigator run playbooks/users.yml -m stdout` |
| Q17 cron | `ansible-navigator run playbooks/cron.yml -m stdout` |

> Q04 uses `ansible-playbook` because `rhel-system-roles` is installed as an RPM, not a collection.

---

## Key exam rules to remember

- **`ansible-navigator`** for collection-installed roles and most playbooks
- **`ansible-playbook`** for RPM-installed system roles (Q04 selinux)
- **`vault_password_file`** is set in `ansible.cfg` — no need for `--vault-password-file` flag
- **Never mount the LV** in Q09 — the question explicitly forbids it
- **Never modify `hosts.yml`** in Q10 — only edit `templates/hosts.j2`
- **Passwords must use `password_hash('sha512')`** in Q15

---

## Files created during exam (not in repo)

These files are created during the exam session and should not be committed:

```
secret.txt          # vault password (Q14)
locker.yml          # encrypted vault (Q14)
salaries.yml        # rekeyed vault (Q16)
user_list.yml       # downloaded user list (Q15)
```
