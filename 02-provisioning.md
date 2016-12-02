# Provisioning

Provisioning is creating the machines with OS and software ready for the deployment of our application.

Deployment is installing and configuring your software product on the provisioned machine.

## Vagrant

Vagrant quickly perform provision machines on your own computer for development purpose. Is a wrapper around VMWare, Virtualbox etc etc.

HINT: If you are looking for production machines have a look to **OpenStack**.

Vagrant can create and configure virtual development environments:

* Lightweight
* Reproducible
* Portable

Is also used to create identical development environments for Operations and to avoid comments like *"It works on my machine!"*.

## Getting Started

First of all we need to install Vagrant and we need also Virtualbox installed on our machine. To create and start a new machine (for example a simple Ubuntu) we need to use two different commands:

    vagrant init ubuntu/trusty64
    vagrant up --provider virtualbox

Now we can use ssh to get access to the machine:

    vagrant ssh

To save the current states:

    vagrant suspend

To stop the machine:

    vagrant halt

To destroy the machine:

    vagrant destroy


## VagrantFile

A VagrantFile is a text file used to create the configuration for the machine we want to create:

* It marks the root directory of your project
* It describes the kind of machine and resources needed for the project to run and what software to installed
* The VagrantFile is meant to be included in version control system

To create a new VagrantFile we can use the **vagrant init** command:

    mkdir my_vagrant_project
    cd my_vagrant_project
    vagrant init

You can use a **box** like we did before with the Ubuntu image, just write in you VagrantFile:

    Vagrant.configure("2") do|config|
      config.vm.box = "ubuntu/trusty64"
    end

Vagrant.configure("2") refers to Vagrant version 2 syntax

Once we have created the machine we can use ssh to get log in. After the login we'll find all the home file inside a directory on the path **/vagrant**. Vagrant will automatically sync our files to and from the guest machine.

Vagrant has also support for automated provisioning:

    Vagrant.configure("2") do|config|
      config.vm.box = "ubuntu/trusty64"
      config.vm.provision :shell, path: "bootstrap.sh"
    end

Vagrant will automatically run **bootstrap.sh** when the guest machine is started, which contains commands to install and configure software.

### VagrantFile - Port Forwarding

If we want to run services we can add port forwarding in the VagrantFile:

    Vagrant.configure("2") do|config|
      config.vm.box = "ubuntu/trusty64"
      config.vm.provision :shell, path: "bootstrap.sh"
      config.vm.network :forwarded_port, host: 8080, guest: 80
    end

This will open the port 8080 on the host machine and redirect all the traffic on port 80 on the guest machine. After adding port forwarding we need to run a **vagrant reload** to reload the VagrantFile.
