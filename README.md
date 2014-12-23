Rigger CoreOS Source
====================
CoreOS image source for [Rigger][rigger]. Builds new style images with
read-only /usr and btrfs. See the [amd64-usr index][coreos-images] for
available image versions. [Packer][packer] v0.6.1 is used to build the images.

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
- __AWS_BUCKET__ - The bucket to store the AMI image in. Must not contain dots.

### virtualbox
VirtualBox instances are created with a single 40GB disk. The disk may be
extended from VirtualBox prior to booting. This will cause CoreOS to expend to
the available space. The public key in files/virtualbox/id_rsa.pub is added to
the authrized_keys file for the core user. Use the associated id_rsa file as
the private key when connecting.

Example
-------

### Amazon: Requirements

If you haven't already, generate signing keys and upload them to AWS.

```bash
openssl genrsa 2048 > aws_key.pem
openssl req -new -subj "/C=DE/ST=Niedersachsen/O=Fake Firm/CN=rigger-signing-keys" -key aws_key.pem -out aws_cert.csr
openssl x509 -req -days 365 -in aws_cert.csr -signkey aws_key.pem -out aws_cert.pem
```

It is an excellent idea to create an IAM user dedicated to building images.
Remember to write down **AWS_ACCESS_KEY** and **AWS_SECRET_KEY** right away.
Add that user to a new group.
Let ```my-rigged-amis``` be the destination bucket, the group's policy could look like this:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt12345678900",
      "Effect": "Allow",
      "Action": [
        "s3:*",
      ],
      "Resource": [
        "arn:aws:s3:::my-rigged-amis/*",
        "arn:aws:s3:::my-rigged-amis",
      ]
    },
    {
      "Sid": "Stmt12345678901",
      "Effect": "Allow",
      "Action": [
        "ec2:*"
      ],
      "Resource": [
        "*"
      ]
    }
  ]
}
```

### Example Run

Channel ```stable``` is the default, but for the sake of a complete example we will use ```alpha```.
Remember to adjust the location of **rigger-coreos**!

```bash
env AWS_ACCOUNT_ID=210123456789 \
    AWS_ACCESS_KEY=AKIAIXXXXXXXXXXXXX \
    AWS_SECRET_KEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXX \
    AWS_BUCKET=my-rigged-amis \
    VERSION=536.0.0 \
    ./rigger \
    -s https://github.com/BlueDragonX/rigger-coreos.git \
    -v 536.0.0 \
    -p amazon-instance \
    -u channel=alpha
```

License
-------
This software project is licensed under the BSD-derived license and is
copyright (c) 2014 Ryan Bourgeois. A copy of the license is included in the
LICENSE file. If it is missing then a copy may be found on the project page.

[rigger]: https://github.com/BlueDragonX/rigger "Rigger"
[packer]: http://packer.io "Packer"
[coreos-images]: http://beta.release.core-os.net/amd64-usr "CoreOS Images"
