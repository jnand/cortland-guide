
Sandboxing
===========

> Sandboxing protects the system by limiting the kinds of operations an application
can perform, such as opening documents or accessing the network. Sandboxing
makes it more difficult for a security threat to take advantage of an issue in a
specific application to affect the greater system.

There are few ways to accomplish this


Native
-------

Clone the s7ephen's [profile suite](https://github.com/s7ephen/OSX-Sandbox--Seatbelt--Profiles) for examples.

To run an application within a sandbox,

`❯ sandbox-exec -f profile.sb /Path/To/The/Application/`

---

Containers
-----------

The current popular trend is to use docker.

> Namespaces provide the first and most straightforward form of isolation: processes running within a container cannot see, and even less affect, processes running in another container, or in the host system.

Install the service, `❯ brew install caskroom/cask/docker` and see the [official guide](https://docs.docker.com/docker-for-mac/) for more details.

?> <i class="fas fa-lock"></i>**+ Security tip:** [Docker Bench Security](https://github.com/docker/docker-bench-security) is very useful tool for evaluating container best practices.

![Docker Bench Report](https://raw.githubusercontent.com/docker/docker-bench-security/master/benchmark_log.png)

---

Virtualization
---------------

Instead of executing against the host system's kernel via a container, we can create a fully encapsulated environment using a VM. The major offerings for macOS are [VirtualBox](https://www.virtualbox.org)(open source), [VMWare Fusion](https://www.vmware.com/products/fusion.html), and [Parallels](https://www.parallels.com/).

Of the three, VMWare Fusion has the best enterprise features for developers.

In all cases well be using Hashicorp's [Vagrant](https://www.vagrantup.com/) and [Packer](https://www.packer.io/) command-line tools to manage virtual "boxes".

**Packer** can build VMs/images for multiple platforms given one config.  
**Vagrant** is used to run/manage them locally, some remotes too.

Install the tools, 

`❯ brew install packer`  
`❯ brew install caskroom/cask/vagrant`  

?> Packer & Vagrant can be used to create sandbox VMs for many platforms; VirtualBox, VMware, EC2, CloudStack, DigitalOcean, Docker, Google Compute Engine, Microsoft Azure, QEMU...


### Resources ###

These resources are a good starting point for creating new sanboxing environments.

Useful templates for packer: <https://github.com/chef/bento>  
Public available vagrant boxes: <https://app.vagrantup.com/boxes/search>



