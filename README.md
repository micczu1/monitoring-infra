**WIP (as curent workstate save)**

Heavily inspired (mostly forked from) by:
https://github.com/cloudalchemy/demo-site


# Monitoring infra

## Important notice

This repository consists of two playbooks:

  - [site.yml](site.yml) - which deploys basic prometheus/grafana stack without additional http proxies and with software listening on default ports
  - [extras.yml](extras.yml) - adds influxdb as a long-term storage and deploys caddy http proxy. This will allow HTTPS connections to services like prometheus

## Run yourself

You can easily run such setup yourself without much knowledge how any part of this works. You just need to do two things:

#### Change ansible inventory

First of all you need to configure your inventory, ours is located in [`hosts`](hosts) file. Here you set up your target hosts by changing value of `ansible_host` variable. Also here you can exclude parts of this demo site, so if you don't need our website, you just remove this part:

```
[web]
demo
```

Accordingly you can exclude grafana, prometheus, or influxdb.

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
git clone https://github.com/micczu1/process-exporter.git
git clone https://github.com/micczu1/bash-exporter.git
git clone https://github.com/micczu1/sql_exporter1.git
cd ..

# Run playbook
ansible-playbook site.yml
# or when using vault encrypted variables
ansible-playbook --vault-id @prompt site.yml
```
