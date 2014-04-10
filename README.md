Rigger CoreOS Source
====================
CoreOS image source for [Rigger](rigger). Builds new style images with
read-only /usr and btrfs. Minimum allowed version is 197.0.0. Special version
strings are alpha and master. See the [amd64-usr index](coreos-images) for
available image versions. [Packer](packer) v0.5.2 is used to build the images.

Platforms
---------
Currently two platforms are available: amazon-instance and virtualbox.
Configuration may be provided through the sources.cfg file located at Rigger's
root directory.

### amazon-instance
Instance AMI's are created with two ephemeral disks. The first disk (xvda) is
mounted as ROOT (/). The second disk (xvdb) is mounted as VAR (/var). Script
and unit file modifications are made to accomplish this.

Ths following configuration options are necessary:
- __AWS_ACCOUNT_ID__ - The account ID used to create the AMI.
- __AWS_ACCESS_KEY__ - An access key for the above account.
- __AWS_SECRET_KEY__ - The secret key associated with the access key.
- __AWS_BUCKET__ - The bucket to store the AMI image in.

### virtualbox
VirtualBox instances are created with a single 40GB disk. The disk may be
extended from VirtualBox prior to booting. This will cause CoreOS to expend to
the available space. The public key in files/virtualbox/id_rsa.pub is added to
the authrized_keys file for the core user. Use the associated id_rsa file as
the private key when connecting.

License
-------
This software project is licensed under the BSD-derived license and is
copyright (c) 2014 Ryan Bourgeois. A copy of the license is included in the
LICENSE file. If it is missing then a copy may be found on the project page.

[rigger]: https://github.com/BlueDragonX/rigger "Rigger"
[packer]: http://packer.io "Packer"
[coreos-images]: http://storage.core-os.net/coreos/amd64-usr "CoreOS Images"
