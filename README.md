## Ansible Rails Toolkit

Ansible roles and playbooks for deploying a Rails application to EC2. **This was a learning experiment** so keep it in mind if you intend to use it.

The system will configure:

- A Postgres instance
- An instance with [Nginx](https://www.nginx.com) acting as a load balancer for the web servers
- A number of web servers with puma
- A [Jenkins](https://jenkins.io) instance

It also includes a playbook for deploying to different environments using Ansible (without using Capistrano or any other third-party system).

It includes 2 environments: 

- `production`: With an EC2-based inventory.
- `production-vagrant`: Which runs on virtual machines orchestrated by [Vagrant](https://www.vagrantup.com). It is meant for experimenting with the production environment locally, which is much more suitable for development time.

It would be pretty trivial to add other environments such as staging. Just copy an existing environment and tweak it.

Notice there are some files that are soft links to avoid duplication. For example: `environments/production/group_vars/all/00-common.yml` → `environments/common.yml`

### Usage

#### Configure app variables

You can configure basic properties such as your app name and git repo in `environments/common.yml`.

#### Edit and encrypt sensitive data (vault files)

You should use [Ansible Vault](http://docs.ansible.com/ansible/2.5/user_guide/vault.html) to encrypt the sensitive data such as passwords and keys in:

- `environments/common-vault.yml`
- `db/vault.yml`

The system assumes that you have a file in `~/ vault_password.txt` containing your password. That way, it won't ask you for it in every operation.

#### Configure your EC2 servers

Edit `environments/production-vagrant/group_vars/all/vars.yml` to configure your EC2 nodes. Example: the type and number of instances you want to use.

#### Provision instances

To provision EC2 instances:

```
ansible-playbook -i environments/production provision.yml
```

Provisioning instances will create the instances in EC2 and configure them with all the artifacts they need.

#### Configure instances

If you only want to launch the configurations of instances:

```
 ansible-playbook -i environments/production configure.yml
```

#### Deploy

To deploy to a given environment:

```
ansible-playbook -i environments/production deploy.yml
```

#### Working with Vagrant

Vagrant up will create the virtual machines and configure them:

```
cd environments/production-vagrant
vagrant up
```

You can launch the configuration only with:

```
ansible-playbook -i environments/production-vagrant configure.yml
```

Or, alternatively, with `vagrant provision`

Then you could deploy normally with:

```
ansible-playbook -i environments/production-vagrant deploy.yml
```

#### Provision Jenkins instance

```
ansible-playbook -i environments/production services.yml
```

