# Mock examples for Software Collections development

This repo contains examples for SCL development on your local machine in mock.

## Mock introduction

Mock is a tool for building packages. It can build packages for different architectures and different CentOS, Fedora or RHEL versions than the build host has. Mock creates chroots and builds packages in them. Its only task is to reliably populate a chroot and attempt to build a package in that chroot. More about mock is available at [https://fedoraproject.org/wiki/Mock](https://fedoraproject.org/wiki/Mock).

## Example task -- packaging rh-mariadb101 SCL

This tutorial expects you are already able to prepare source RPM with SCL macros. For testing, you can get such SRPMs from [cbs.centos.org](http://cbs.centos.org), for example these:

* http://cbs.centos.org/kojifiles/packages/rh-mariadb101/2.2/2.el7/src/rh-mariadb101-2.2-2.el7.src.rpm
* http://cbs.centos.org/kojifiles/packages/rh-mariadb101-Judy/1.0.5/12.el7/src/rh-mariadb101-Judy-1.0.5-12.el7.src.rpm
* http://cbs.centos.org/kojifiles/packages/rh-mariadb101-mariadb/10.1.11/8.el7/src/rh-mariadb101-mariadb-10.1.11-8.el7.src.rpm

What we'll need to do is to build packages in this order while there are dependencies between them, so we must make available the previous builds for the later builds.

## Congiguring the mock environment

We will use `/home/hhorak/rh-mariadb101-rpms` directory to store built RPMs, so let's create this directory and initialize it as repository:

```
mkdir /home/hhorak/rh-mariadb101-rpms
createrepo /home/hhorak/rh-mariadb101-rpms
```

We can now add the profile for mock available in this repo and add the user to mock group so the user has permissions to install packages in the chroot:
```
sudo cp sclo7-rh-mariadb101.cfg /etc/mock/sclo7-rh-mariadb101.cfg
sudo usermod -a -G mock <username>
```

## Building SCL packages in mock locally

Now we're ready to build the meta package:
```
mock -r sclo7-rh-mariadb101 rh-mariadb101-2.2-2.el7.src.rpm
```

The results, when build succeeded, is stored in the mock results dir, so we can grab resulting RPMs to the local repo dir and refresh repodata:
```
cp /var/lib/mock/fedora-rawhide-x86_64/result/*.rpm /home/hhorak/rh-mariadb101-rpms
createrepo /home/hhorak/rh-mariadb101-rpms
```

Now we can continue with another package and build package by package.
