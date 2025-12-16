<strong>This project reflects the small environment with Flask Python webapp integrated with MySQL database. Its installation is automated using Ansible.</strong>

## How to prepare the environment?

Please make sure that ssh keypair exists in Vagrantfile directory. If not, follow [this procedure](https://devops.stackexchange.com/a/1247).

## How to run the playbook?

Go to `playbooks` folder and run the playbook:

```sh
cd playbooks
ansible-playbook -i inventory main.yml
```

## How to run molecule tests?

Go to /vagrant/playbooks/roles/<role_name> directory and run:

```sh
molecule test
```

If you would like to create the docker container for molecule testing, run:
```sh
molecule create
```

If you would like to run tests without destroying the container afterwards, run:

```sh
molecule converge
```

If you would like to enter the running container, run:

```sh
molecule login
```

If you want to destroy the existing testing environment, run:

```sh
molecule destroy
```