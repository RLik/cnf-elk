# Configuring Elasticsearch (Elasticsearch, Logstash, Kibana and Fleet server) from box by GitLab pipeline

Ansile playbookas for configuring Elasticsearch components out of box by GitLab pipeline. Playbook was tested on followins OS: Rocky Linux 9.3 (Blue Onyx).

## Environment description

This pipeline using GitLab CE server with local shell agent and Ansible installed. GitLab runner run under gitlab-runner user account.

## Step to prepare

- [ ] Switch to gitlab-runner user context:
```
 sudo su - gitlab-runner
```
- [ ] Generate SSH keys if they wasn't generated early:
```
ssh-keygen
```
- [ ] Correct inventory file (inventory/inv.ini) and add appropriate hosts.
- [ ] Copy SSH public key to all servers form inventory file:
```
ssh-copy-id elkuser@192.168.56.122
ssh-copy-id elkuser@192.168.56.120
```
- [ ] Add sudo password for all hosts.
```
sudo nano /etc/ansible/vault/elasticsearch.yml
sudo nano /etc/ansible/vault/kibana.yml
```
Format is:
```
ansible_sudo_pass: P@$$word
```
- [ ] Encrypt file with sudo password by Ansible Vault:
```
sudo ansible-vault encrypt /etc/ansible/vault/elasticsearch.yml
sudo ansible-vault encrypt /etc/ansible/vault/kibana.yml
```
- [ ] Give rights to read sudo password file only to root and gitlab-runner users:
```
sudo chown root:gitlab-runner /etc/ansible/vault/elasticsearch.yml
sudo chown root:gitlab-runner /etc/ansible/vault/kibana.yml
sudo chmod 640 /etc/ansible/vault/elasticsearch.yml
sudo chmod 640 /etc/ansible/vault/kibana.yml
sudo chmod 650 /etc/ansible/vault/
```

- [ ] In GitLab Project settings add variale named $ANSIBLE_VAULT_PASSWORD and in value part specify password that you've use to encrypt elasticsearch.yml and kibana.yml files by Ansible Vault.
- [ ] In GitLab Project settings add variale named $ELASTIC_USER_PWD and in value part specify password that willbe used for elastic builtin user.
- [ ] In GitLab Project settings add variale named $ES_ENCR_KEY and in value part specify encryption key that will be using for Enterprisesearch server.
- [ ] In GitLab Project settings add variale named $METRICBEAT_USER_PWD and in value part specify from which Metricbeat will be connecting to Enterprisesearch server.
- [ ] Create directory for storing SSL cert and keys files. Add contents of SSL crt and key files (or just copy this files to server):
```
sudo mkdir -p /etc/ansible/config/ssl
sudo nano /etc/ansible/config/ssl/itproblog.key
sudo nano /etc/ansible/config/ssl/itproblog.crt
```
- [ ] Chnage the owner of certificate files
```
sudo chown root:gitlab-runner /etc/ansible/config/ssl/itproblog.key
sudo chown root:gitlab-runner /etc/ansible/config/ssl/itproblog.crt
sudo chmod 640 /etc/ansible/config/ssl/itproblog.key
sudo chmod 640 /etc/ansible/config/ssl/itproblog.crt
sudo chown root:gitlab-runner /etc/ansible/config/ssl
sudo chmod 650 /etc/ansible/config/ssl
```