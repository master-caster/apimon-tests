# apimon-tests
Tests for the APIMonitoring

Initially developed as part of the api-monitoring project, but separated for the clarity of things


#Steps to execute scenarios manually

1. Prepare a virtual environment for the python (optional, depending on how you would like to set up environment)

```
  python3 -m venv venv
```

2. Source into the virtual environment (required only if step 1 was performed)

```
  source venv/bin/activate
```

3. Install Ansible (RPM version may be used instead, but must be of minimum version 2.9)

```
  pip install ansible
```

4. Install additional python packages (it is important that those packages are installed in the python environment, which is used by python to execute modules on the target system)

```
  pip install openstacksdk
```

5. Install additional ansible requirements used in the some scenarios from the git checkout location (this step will look different with Ansible 2.10)

```
  ansible-galaxy collection install -r requirements.yml
  ansible-galaxy role install -r requirements.yml
```

6. Prepare clouds.yaml configuration. It must be located in either ~/.config/openstack/clouds.yaml or /etc/openstack/cluods.yaml, otherwise ansible will not be able to locate it:

```
clouds:
  otc:
    auth:
      auth_url: https://XXXXXXXXXXX
      project_name: XXXXXXXXXX
      user_domain_name: XXXXXXXXXXXXXXX
      username: XXXXXXXX
      password: XXXXXXXX
```

7. Execute the chosen scenario (adapt OS_CLOUD and ansible_python_interpreter as you need)

```
  OS_CLOUD=otc ansible-playbook -i inventory/testing playbooks/scenario1_token.yaml -e 'ansible_python_interpreter=~/venv/bin/python'
```

**Note**: Ansible is designed to accept description in form: what and where. Ansible itself is triggered from the "controller" host, connects to target host (whether through SSH, FTP, HTTP, Telnet, or even SH if target=localhost). On the target environment Ansible searches for the fitting interprether (https://docs.ansible.com/ansible/latest/reference_appendices/interpreter_discovery.html) and uses it for the execution of each concrete step. This leaves an important apsect of running tasks on the localhost (especially valid for managing IaaS, since there is actually no host existing, we try to create one), that dependencies required by the ansible module need to be satisfied by the interpreter that Ansible uses on the target (python used to start `ansible-playbook` command is most likely not the same ansible will use to execute module). Option `-e 'ansible_python_interpreter=``which python``'` is forcing Ansible to use the same interprether as the one used to invoke ansible itself. This can be avoided if the required dependencies are installed in the system globally (not in the virtual environment). Therefore, if `pip install otcextensions openstacksdk` was executed not in the virtual environment, but in the system scope - setting this option is not required.

Every scenario is designed to deploy some resources, do some additional checks/operations on them and destroy them. If it is desired to leave provisioned resources scenario should be modified (TODO: add support for tag as a cleanup). Please note in this case, that scenario might leave many resources, which need to be deleted manually.

Under CentOS7 ansible is of a very old version with some other problems. One example is that some scenarios are trying to write file on the FS with SElinux enabled, but this requires selinux python package to be installed in the execution environment (`pip install selinux` works, but playbook fail with python internal exceptions). So for usage under CentOS7 it is recommended to disable SElinux or invest time in ensuring selinux python library work properly (not this project's fault).

It might be also desired to enable task tracing (timing). This can be achieved by following https://docs.ansible.com/ansible/latest/plugins/callback/profile_tasks.html and enabling profile_tasks plugin in the ansible.cfg file in this repository.

dummy change
dummy change2
dummy change3
dummy change4
