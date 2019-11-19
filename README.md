bootstrap
=========

<img src="https://docs.ansible.com/ansible-tower/3.2.4/html_ja/installandreference/_static/images/logo_invert.png" width="10%" height="10%" alt="Ansible logo" align="right"/>

<a href="https://travis-ci.org/tehtbl/ansible-role-bootstrap">
  <img src="https://travis-ci.org/tehtbl/ansible-role-bootstrap.svg?branch=master" alt="Build status"/>
</a>

<!-- <img src="https://img.shields.io/ansible/role/d/21642"/>
<img src="https://img.shields.io/ansible/quality/21642"/> -->

Prepare your Alpine/Ubuntu/Debian System for Ansible.

Example Playbook
----------------

This example is taken from `molecule/resources/playbook.yml`:
```yaml
---
# ------------------------------------------------------------------------
# bootstrap your hosts
# ------------------------------------------------------------------------
- name: Converge
  hosts: all
  become: yes
  gather_facts: no

  roles:
    - tehtbl.boostrap
```

Role Variables
--------------

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for bootstrap

# The user to use to connect to machines.
bootstrap_user: root

# Installed software to support modules flagged as "preview" (i.e. mysql_db).
# "yes", "no" or unset are valid.
bootstrap_preview: no

# Do you want to wait for the host to be available?
bootstrap_wait_for_host: no

# The number of seconds you want to wait during connection test before failing.
bootstrap_timeout: 3

# The number of retries during installation
bootstrap_retries: 3
```

Requirements
------------

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next
  release of Ansible.)

<!-- The following roles can be installed to ensure all requirements are met, using
`ansible-galaxy install -r requirements.yml`:

```yaml
- none
``` -->

Context
-------

This role is a part of many compatible roles. Have a look at
[my GH repos](https://github.com/tehtbl?utf8=%E2%9C%93&tab=repositories&q=ansible-role&type=&language=)
for further information.

<!-- Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/bootstrap.png "Dependency") -->

Compatibility
-------------

This role has been tested on these [container images](https://hub.docker.com/):

|container|tag|allow_failures|
|---------|---|--------------|
|debian|stable|no|
|debian|testing|no|
|debian|unstable|yes|
|ubuntu|xenial|no|
|ubuntu|bionic|no|
|ubuntu|devel|yes|

This role has been tested on these Ansible versions:

- ansible>=2.8, <2.9
- ansible>=2.9
- git+https://github.com/ansible/ansible.git@devel

Testing Using Tox
-----------------

[Unit tests](https://travis-ci.org/tehtbl/ansible-role-bootstrap) are done on
every commit, pull request, release and periodically.

If you find issues, please register them in
[GitHub](https://github.com/tehtbl/ansible-role-bootstrap/issues)

Testing is done using [Tox](https://tox.readthedocs.io/en/latest/) and
[Molecule](https://github.com/ansible/molecule):

[Tox](https://tox.readthedocs.io/en/latest/) tests multiple Ansible versions.
[Molecule](https://github.com/ansible/molecule) tests multiple distributions.

To test using the defaults (any installed Ansible version, namespace: `tehtbl`,
  image: `ubuntu`, tag: `latest`):

```
molecule test

# Or select a specific image:
IMAGE="ubuntu" molecule test

# Or select a specific image and a specific tag:
IMAGE="debian" TAG="stable" tox
```

Or you can test multiple versions of Ansible, and select images:
Tox allows multiple versions of Ansible to be tested. To run the default
(namespace: `tehtbl`, image: `ubuntu`, tag: `latest`) tests:

```
tox

# To run Ubuntu (namespace: `tehtbl`, tag: `latest`)
IMAGE="ubuntu" tox

# Or customize more:
IMAGE="debian" TAG="stable" tox
```

Testing Using Vagrant
---------------------

Install `vagrant` plugins via:
```
vagrant plugin install vagrant-reload
```

Start Tests via *VirtualBox* Provider:
```
vagrant up
```

License
-------

GNU General Public License v3.0

Author Information
------------------

[@tehtbl](https://github.com/tehtbl)

Sources
-------

This work is based on the great work of many people, e.g.
[robertdebock](https://github.com/robertdebock) and
[geerlingguy](https://github.com/geerlingguy)
