# Ansible Basics and Configuration

## What This Covers
Installation, configuring SSH access, configuring VIM, project structure files

## Files in This Folder

| File | Description |
|---|---|
| `ansible.cfg` | **"How"**   The settings manual. It tells Ansible which <br>        user to log in as and where the inventory is  |
| 'inventory'   | **"Who"**   The address book. Lists the IPs of your managed nodes
| 'site.yml'    | **"What"**  The to-do list. This is the playbook.
## Key Concepts Learned

- Concept 1 — Power Heirarchy: Ansible doesnt merge .cfg files, it uses the current directorys file, then looks to the <br> Home directory, and lastly the Global directory.  Variable Hierarchy goes from Grandest to Smallest (Host takes precedence over Group <br> over Inventory/Global Variables)
- Concept 2 — Ad-Hoc Commands: On-demand, pre-packaged commands that can be run without a playbook
- Concept 3 — Idempotency: Commands that have been previously executed are skipped by Ansible

## Lab Environment
- **Control node:** admin.ytt.lab (192.168.1.227/24)
- **Managed node(s):** web1.ytt.lab (192.168.1.245/24)
- **OS:** RHEL 9 / CentOS Stream

## Commands / Syntax Reference
Ad-Hoc commands: [ansible -m] (-m invokes a specific module)
                               Other flags calibrate the actions of the module
# Most useful command or syntax from this topic
# Add anything you want to remember quickly
"The control node SSHs into the managed node as a user, and pushes the desired config changes. the ansible.cfg
 sets the How, the inventory file identifies the Who, and the .yml file (Playbook) sets the what. Commands are declarative
 (you tell Ansible the desired outcome as opposed to how to do it), idempotent (previously executed commands are skipped), 
 and agentless (Ansible doesnt need to be installed on the managed node).
 Ad-Hoc commands are existing scripts, instructed to invoke existing binaries on target machines without the need of 
 a playbook"
## What I'd Do Differently in Production
One or two sentences on how this would look in a real enterprise environment
vs. a lab. Shows hiring managers you're thinking beyond the exercise.

## Connected To
- Links to other folders in this repo that relate to this topic
- Example: "Builds on 02-inventory-management — uses the same hosts file"
