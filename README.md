packer-template-openstack-nodepool-nodes
========================================

Packer template for creating your own test nodes that behave like [Nodepool](http://ci.openstack.org/nodepool.html)'s test nodes.

[OpenStack](http://www.openstack.org/)'s automated testing runs on a
large pool of single use test nodes.  Nodepool generates a snapshot of
each test node type once a day, and maintains a set of each in a ready
state for any tests that need to run.  A node is used for one test and
then deleted to ensure that there are no side effects between test
runs.

This project uses [Packer](http://packer.io) to build machines just like
Nodepool would, so you can have test machines of your own that should
match what OpenStack uses without requiring a full copy of their CI
infrastructure.

Since this project wants to match Nodepool's build behavior as closely
as possible, it uses the build scripts that Nodepool uses.  That means
you need to check out those scripts:

    pushd ..
    git clone https://git.openstack.org/openstack-infra/config openstack-infra-config
    popd

Nodepool can build a few different types of machines.  You can see the
supported options:

    ls ../openstack-infra-config/modules/openstack_project/files/nodepool/scripts/prepare_node_*

At the time I'm writing this doc, the variations include:

- bare
- devstack
- devstack_new_kernel
- py3k
- tripleo

To build the machine, run the following command:

    packer build -only=qemu -var "node-type=devstack" -var "publickey=`cat ~/.ssh/id_rsa.pub | sed -e 's/ssh-rsa //' -e 's/ .*//'`" machine.json

Three things to note:

1. You can provide any of the node variations in the list above to the
   node-type variable.
2. The command above will load your public key onto the machine as
   long as it's in the default location.  Change that if you'd like.
3. I've only tested the qemu builder so far.  More to come, pull
   requests or test failure logs are welcome.
