# machine-id-store

An elegant way to collect machine IDs for the inventory group `servers` using `template` module without modules like `lineinfile`, `when` statements, etc.

## Prerequisites
Create and activate a virtual environment and install requirements:
```
python3 -m venv env
source env/bin/activate
pip install --upgrade pip setuptools wheel
pip install -r requirements.txt
```
Provide a valid inventory file with hosts defined in the `servers` group and place it in the project directory. Sample inventory in the repo assumes that Ansible Vault is used for storing credentials and vault file is stored as `group_vars/all/vault.yml`. Feel free to replace this files with your own.<br/>
See more:<br/>
[Ansible Inventory](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html)<br/>
[Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html)<br/>

## Usage
Run the playbook with the following command:
```
ansible-playbook -i hosts machine_id_store.yml
```
The first play gathers machine IDs for the hosts from `servers` inventory group. Gathering restricted to `ansible_machine_id` hostvar only to speed up the play.
The second play saves the list of gathered machine IDs to the files specified in the `vars` section of the top-level playbook. Two lists will be saved in the playbook directory on the Ansible control node:

| Filepath                            | Content                                                         |
| ----------------------------------- | -------------------------------------------------------------------------------------------------- |
| `<playbook directory>/servers.list` | Complete list with all gathered machine IDs and already known IDs if the file was present already. |
| `<playbook directory>/new.list`     | The list of the newly discovered IDs that were not present in servers.list file.                   |

Files will be rendered with Jinja2 using Ansible `template` module which is preferable practice for the case.
