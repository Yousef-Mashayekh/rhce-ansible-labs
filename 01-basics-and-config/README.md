# Ansible Basics and Configuration

## What This Covers
Installation, configuring SSH access, configuring VIM, project structure files

## Files in This Folder

| File | Description |
|---|---|
| `ansible.cfg` | **"How"**   The settings manual. It tells Ansible which <br>               user to log in as and where the inventory is  |
| 'inventory'   | **"Who"**   The address book. Lists the IPs of your managed nodes
| 'site.yml'    | **"What"**  The to-do list. This is the playbook.
## Key Concepts Learned

- **Concept 1** — Power Heirarchy: Ansible doesnt merge .cfg files, it uses the current directorys file, then looks to <br> the Home directory, and lastly the Global directory.  Variable Hierarchy goes from "Specific to General" (CLI beats Playbook beats Host beats Group beats Inventory/Global Variables)
- **Concept 2** — Ad-Hoc Commands: On-demand, pre-packaged commands that can be run without a playbook.
- **Concept 3** — Idempotency: Commands that have been previously executed are skipped by Ansible.
- **Concept 4** - Agentless: Ansible pushes changes via SSH access and doesnt require anything be installed on the target machine.
- **Concept 4** - SSH: The identity is tied to the user account, not just the hardware. The "lock" (public key) is stored in the authorized_keys dir on managed node, and the "key" (private key) is stored in in the ~/.ssh/ dir of the user that generated the keys. 
## Lab Environment
- **Control node:** admin.ytt.lab   (192.168.1.227/24)
- **Managed node(s):** web1.ytt.lab (192.168.1.245/24)
                       web2.ytt.lab (192.168.1.243/24)
                       db1.ytt.lab  (192.168.1.242/24)
- **OS:** RHEL 9 / CentOS Stream

## Commands / Syntax Reference
Ad-Hoc commands: [ansible -m] (-m invokes a specific module)
                               Other flags calibrate the actions of the module

# Add anything you want to remember quickly
"The control node SSHs into the managed node as a user, and pushes the desired config changes. the ansible.cfg
 sets the How, the inventory file identifies the Who, and the .yml file (Playbook) sets the what. Commands are declarative
 (you tell Ansible the desired outcome as opposed to how to do it), idempotent (previously executed commands are skipped), 
 and agentless (Ansible doesnt need to be installed on the managed node).
 Ad-Hoc commands are existing scripts, instructed to invoke existing binaries on target machines without the need of 
 a playbook"

