# Introduction to Storage

With the introduction of experimental, work-in-progress [storage](https://jujucharms.com/docs/1.24/wip-storage) in Juju 1.24, this charm has been created in order to explore this new functionality as well as it's relation to benchmarking.

The goal is to provide a set of working examples for current and future charm authors to add storage to their work.

# Usage

    # Clone this to your charms/trusty directory, i.e., ~/charms
    git clone https://github.com/juju-solutions/storage-demo.git

    juju deploy --repository=~/charms local:trusty/storage-demo

By default, this will create and mount a 2G ext4 file system. You can change these settings as needed.

# Actions

The *format* action below allows you to reformat the underlying storage used by the charm. This is particularly useful when used with the *dd* action, allowing you to do simple IO benchmarks against various storage configuration options.

## format
Reformat a block device. Useful for changing fs type or mount options.

    juju action do storage-demo/0 format fstype=ext3
    juju action do storage-demo/0 format mount-options="-o noatime"

## dd
Run `dd` to test the raw write speed of a device.

    juju action do storage-demo/0 dd
    juju action do storage-demo/0 dd bs=512M count=3


# Caveats

There's no way to do the equivalent of `juju storage list` from a hook/action context, i.e., `storage-list` so extra steps have been taken to track what block device and mount point are associated with this charm. This also makes working with more than one storage device at a time more problematic. We'll revisit this, as needed, in the future.

Different cloud providers have different block storage options. Please refer to the [work-in-progress storage documentation](https://jujucharms.com/docs/1.24/wip-storage) for more information.

# Provider-specific instructions
## Local Provider/LXC

In order to test using LXC containers, you'll need to add `allow-lxc-loop-mounts: true` to your `~/.juju/environments.yaml` file.

    local:
      admin-secret: K2kiBpqK4uqJZr
      default-series: trusty
      storage-port: 62840
      type: local
      allow-lxc-loop-mounts: true

## Amazon

By default, Amazon will provision magnetic storage for new block devices. You can change this by telling the charm which type of volume to use, such as ssd or provisioned iops:

    juju deploy local:trusty/storage-demo --storage data=ebs-ssd,1G

# TODO
- Write a config-changed hook to make adjustments to storage
- Sanity check parameters all parameters for unexpected input
- Document usage against various clouds and cloud-specific blockdevice parameters.
- Integrate with [benchmark-gui](https://code.launchpad.net/~marcoceppi/charms/trusty/benchmark-gui/trunk) so that we can better compare file system types and mount options.

# Contributions

Patches and bug reports welcome! Please visit the [github repo](https://github.com/juju-solutions/storage-demo) for the latest code, pull requests, and bug reports. Feel free to visit #juju on Freenode to be a part of the conversation.
