# Monitoring infra

Playbook [site.yml](site.yml) deploys basic prometheus/grafana stack without additional http proxies and with software listening on default ports.

## What is what

| name | source | notes |
| -------------- | ------------- | -----------------------------------|
| `cloudalchemy.node-exporter` | Ansible Galaxy | role to install node_exporter on host; exports OS and hardware metrics |
| `cloudalchemy.blackbox-exporter` | Ansible Galaxy | role to install blackbox-exporter on Prometheus host; it's for checking services remotely |
| `cloudalchemy.snmp-exporter` | Ansible Galaxy | role to install snmp-exporter on Prometheus host; it's for checking devices remotely via SNMP; currently has some problem with installing |
| `cloudalchemy.mysqld_exporter` | Ansible Galaxy | role to install mysqld_exporter on hosts with MySQL databases; it exports databases metrics |
| `process-exporter` | custom Ansible role | role to install process-exporter on host; exports running processes metrics |
| `bash-exporter` | custom Ansible role | role to install bash-exporter on host; it allows exporting contents of text files, which can be written i.e. by scripts; currently unused as node_exporter has textfile exporting capability |
| `cloudalchemy.prometheus` | Ansible Galaxy | role to install Prometheus application on Prometheus host |
| `cloudalchemy.alertmanager` | Ansible Galaxy | role to install Alertmanager application on Prometheus host; alerting app for Prometheus |
| `sql_exporter1` | custom Ansible role | role to install sql_exporter by free on host; exports results of defined custom SQL queries; seems to address our needs better than sql_exporter by justwathcom|
| `cloudalchemy.grafana`| Ansible Galaxy | role to install Grafana application on Grafana host (which could be the same as Prometheus host) |
| `stunnel` | custom Ansible role | role to install stunnel on node; simple TLS proxy used for secure communication between Prometheus and his exporters |

## Run by yourself

You can easily run such setup yourself without much knowledge how any part of this works. You just need to do two things:

#### Change ansible inventory

First of all you need to configure your inventory, ours is located in [`hosts`](hosts) file. Here you set up your target hosts by changing value of `ansible_host` variable. Also, here you can exclude parts of this demo site, so if you don't need our website, you just remove this part:

```
[web]
demo
```

Accordingly, you can exclude grafana, prometheus, or influxdb.

#### Change passwords

For security measures we encrypted some of our passwords, but it is easy to use yours! You can do it by replacing a file located at [`group_vars/all/vault`](group_vars/all/vault) with following content:

```
vault_grafana_password: <<INSERT_YOUR_GRAFANA_PASSWORD>>
vault_influxdb_password: <<INSERT_YOUR_INFLUXDB_PASSWORD>>
```

You need to specify both even if you don't use grafana nor influxdb. You can look over [`group_vars/all/vault`](group_vars/all/vars) to find why.

#### Run as usual Ansible playbook

```bash
# Download roles
ansible-galaxy install -r roles/requirements.yml

cd roles
git clone https://github.com/micczu1/stunnel.git
git clone https://github.com/micczu1/process-exporter.git
git clone https://github.com/micczu1/bash-exporter.git
git clone https://github.com/micczu1/sql_exporter1.git
cd ..

# Run playbook
ansible-playbook site.yml
# or when using vault encrypted variables
ansible-playbook --vault-id @prompt site.yml
```
#### Test with Vagrant

```bash
# To start or reprovision after changes
vagrant up --provision

# To login to test virtual machine
vagrant ssh

# To remove test machine
vagrant destroy
```

## Credits

This project is inspired by:
https://github.com/cloudalchemy/demo-site

## ToDo

- [ ] Put passwords in Ansible Vault
- [ ] Secure exporters (TLS encryption + simple auth)
- [ ] Sort out the _"missing jmespath on deployer"_ problem (it appears in Prometheus and Grafana roles; workaround is to comment out few minor auto-configuration tasks)
- [ ] Better auto-configuration of Prometheus
- [ ] Better auto-configuration of Grafana
