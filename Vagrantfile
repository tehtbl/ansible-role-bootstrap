# -*- mode: ruby -*-
# vi: set ft=ruby :

# based on https://raw.githubusercontent.com/borgbackup/borg/master/Vagrantfile

#
# variables
#
$cpus = Integer(ENV.fetch('VMCPUS', '2'))  # create VMs with that many cpus
$mem = Integer(ENV.fetch('VMMEM', '8192'))  # create VMs with that many cpus
$rolename = "ansible-role-bootstrap"

#
# provision user
#
def fs_init(user)
  return <<-EOF
    find /vagrant/#{$rolename} -name '__pycache__' -exec rm -rf {} \\; 2> /dev/null
    chown -R #{user} /vagrant/#{$rolename}
    touch ~#{user}/.bash_profile ; chown #{user} ~#{user}/.bash_profile
    echo 'export LANG=en_US.UTF-8' >> ~#{user}/.bash_profile
    echo 'export LC_CTYPE=en_US.UTF-8' >> ~#{user}/.bash_profile
    echo 'export LC_ALL=en_US.UTF-8' >> ~#{user}/.bash_profile

    export PATH="#{user}/.pyenv/bin:$PATH"
  EOF
end

#
# install packages
#
def packages_debianoid(user)
  return <<-EOF

    apt update

    # install all the (security and other) updates
    apt dist-upgrade -y

    # for building borgbackup and dependencies:
    apt install -y libssl-dev libacl1-dev liblz4-dev libzstd-dev libfuse-dev fuse pkg-config

    usermod -a -G fuse #{user}
    chgrp fuse /dev/fuse
    chmod 666 /dev/fuse

    apt install -y fakeroot build-essential git curl
    apt install -y python3-dev python3-setuptools python-virtualenv python3-virtualenv python3-pip python3-venv

    # for building python:
    apt install -y zlib1g-dev libbz2-dev libncurses5-dev libreadline-dev liblzma-dev libsqlite3-dev libffi-dev

  EOF
end

#
# install pyenv
#
def install_pyenv(boxname)
  return <<-EOF
    curl https://pyenv.run | bash

    echo 'export PATH="$HOME/.pyenv/bin:$PATH"' >> ~/.bash_profile
    echo 'eval "$(pyenv init -)"' >> ~/.bash_profile
    echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bash_profile

    echo 'export PYTHON_CONFIGURE_OPTS="--enable-shared"' >> ~/.bash_profile
  EOF
end

#
# install pyenvs
#
def install_pythons(boxname)
  return <<-EOF
    . ~/.bash_profile

    # pyenv install 3.5.9
    # pyenv install 3.6.9
    pyenv install 3.7.5rc1
    # pyenv install 3.8.0

    pyenv rehash
  EOF
end

#
# run tests
#
def run_tests(boxname)
  return <<-EOF

    . ~/.bash_profile
    cd /vagrant/#{$rolename}

    # pyenv global 3.5.9
    # pip3 install tox
    # fakeroot -u tox -e "py35-ansible-{previous,current,next}"

    # pyenv global 3.6.9
    # pip3 install tox
    # fakeroot -u tox -e "py36-ansible-{previous,current,next}"

    pyenv global 3.7.5rc1
    pip3 install tox
    fakeroot -u tox -e "py37-ansible-{previous,current,next}"

    # pyenv global 3.8.0
    # pip3 install tox
    # fakeroot -u tox -e "py38-ansible-{previous,current,next}"

    # pyenv virtualenv 3.6.8 borg-env
    # ln -s ~/.pyenv/versions/borg-env .

  EOF
end

#
# MAiN
#
Vagrant.configure(2) do |config|
  # use rsync to copy content to the folder
  config.vm.synced_folder ".", "/vagrant/#{$rolename}", :type => "rsync", :rsync__args => ["--verbose", "--archive", "--delete", "-z"], :rsync__chown => false

  # do not let the VM access . on the host machine via the default shared folder!
  config.vm.synced_folder ".", "/vagrant", disabled: true

  # access a port on your host machine (via localhost) and have all data forwarded to a port on the guest machine.
  config.vm.network "forwarded_port", guest: 8080, host: 8080

  # Create a private network, which allows host-only access to the machine using a specific IP.
  config.vm.network "private_network", ip: "192.168.5.11"

  # set number of CPUs
  config.vm.provider :virtualbox do |v|
    v.cpus = $cpus
    # vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    # vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
  end

  # ubuntu vm
  config.vm.define "bionic64" do |b|
    b.vm.box = "ubuntu/bionic64"
    b.vm.provider :virtualbox do |v|
      v.memory = $mem
    end
    b.vm.provision "fs init", :type => :shell, :inline => fs_init("vagrant")
    b.vm.provision "packages debianoid", :type => :shell, :inline => packages_debianoid("vagrant")
    b.vm.provision :reload

    b.vm.provision "install pyenv", :type => :shell, :privileged => false, :inline => install_pyenv("bionic64")
    b.vm.provision "install pythons", :type => :shell, :privileged => false, :inline => install_pythons("bionic64")

    b.vm.provision :docker
    b.vm.provision "run tests", :type => :shell, :privileged => false, :inline => run_tests("bionic64")
  end

  # config.vm.define "stretch64" do |b|
  #   b.vm.box = "debian/stretch64"
  #   b.vm.provider :virtualbox do |v|
  #     v.memory = 1024 + $wmem
  #   end
  #   b.vm.provision "fs init", :type => :shell, :inline => fs_init("vagrant")
  #   b.vm.provision "packages debianoid", :type => :shell, :inline => packages_debianoid("vagrant")
  #   b.vm.provision "install pyenv", :type => :shell, :privileged => false, :inline => install_pyenv("stretch64")
  #   b.vm.provision "install pythons", :type => :shell, :privileged => false, :inline => install_pythons("stretch64")
  #   b.vm.provision "build env", :type => :shell, :privileged => false, :inline => build_pyenv_venv("stretch64")
  #   b.vm.provision "install borg", :type => :shell, :privileged => false, :inline => install_borg(true)
  #   b.vm.provision "install pyinstaller", :type => :shell, :privileged => false, :inline => install_pyinstaller()
  #   b.vm.provision "build binary with pyinstaller", :type => :shell, :privileged => false, :inline => build_binary_with_pyinstaller("stretch64")
  #   b.vm.provision "run tests", :type => :shell, :privileged => false, :inline => run_tests("stretch64")
  # end

end
