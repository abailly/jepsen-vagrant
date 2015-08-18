# Requirements

* Vagrant version >= 1.6.3

# Running Jepsen

This directory contains a bunch of files to ease the setup of boxes for running [jepsen](http://github.com/aphyr/jepsen) tests. 
It might be useful to people that do not run
natively on OSes providing lxc/containers implementation nor have access to dedicated testing infrastructure, yet want to study
jepsen, run the test suite it provides and test implementation for other databases/systems.

* The `Vagrantfile`is a [vagrant](http://vagrantup.com) file (!) that creates a VM box containing all the needed tools to run
jepsen. For detailed usage please refer to Vagrant's documentation. Normally the following should give you, after a couple of
coffees, a working VM containing five configured LXC boxes configured for running jepsen:

         vagrant up
         ..... [long]
         vagrant ssh
         cd /jepsen
         lein with-profile elasticsearch test :only jepsen.system.elasticsearch-test

* Configuration of the vagrant's VM is provided as a bunch of scripts (yes, this should be
  puppet/chef/salt/pick-your-own-scm-tool) which may be used independently from Vagrant itself:

   * `setup.sh` install dev packages: git, java and lein
   * `net.sh` setup the virtual network that connects all LXC boxes 
   * `lxc.sh` creates the five LXC boxes and configures them
   *  `functions.sh` contains auxiliary functions useful to other scripts
   * Note that scripts should be idempotent
      
* **Caveat**: Tests might need to be modified as the authentication configuration of LXC boxes is a bit rough. Modify the tests
  accordingly by adding `:ssh` keys to the `core/run!`function's parameters. Normally, logging in as `root` with key
  `~/.ssh/id_rsa` should work fine.

* **Troubleshooting**:  If during provisioning, your terminal turn to gibberish, follow these steps to manually complete
  provisioning:

  First, fix your terminal:

        reset

  Next log in, repair the problem which I'm relatively sure will be wrong with your apt, and run the provisioning again manually:

        vagrant ssh
        cd /vagrant
        sudo bash setup.sh # once this is complete, this vm will always be setup for jepsen
        sudo bash net.sh # this needs to be run on each boot to setup the network for lxcs
        sudo bash lxc.sh # this creates and/or starts the lxc containers, run once per boot

  At this point, in 'ps auxf' output, you should see a bunch of lxc containers running things like ElasticSearch and RabbitMQ.  If not, restart the machine and re-run net.sh and lxc.sh .

  Now you can run a sample command such as in Jepsen docs:
  
        lein with-profile +rabbitmq test jepsen.system.rabbitmq-test

* As mentioned in Jepsen's own README, if you get errors about HostKey failure from jsch, you probably need to auth all of the vms:

        for i in 1 2 3 4 5; do ssh-keyscan -t rsa n${i}; done >> ~/.ssh/known_hosts

# TODO

* Ensure all tests for all systems run correctly
    * elasticsearch works fine
    * etcd installation works fine but runner stays lock in the synchronization phase
* Number and name of boxes should be parameterized
* Provide the ability to build boxes using docker

         
