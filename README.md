# explore-docker

[_Up_](https://github.com/irnc/software-developer).

## Docker Engine on Windows

* http://blog.docker.com/2015/08/tp-docker-engine-windows-server-2016/

## Practical use cases

- creating isolated build environment

### Creating isolated build environment

- create [Dockerfile](https://docs.docker.com/engine/reference/builder/)
- connect sources as [data volume](https://docs.docker.com/engine/userguide/containers/dockervolumes/)

Example for Ubuntu 14.04 + Ansible 1.9.4, `Dockerfile`:

```dockerfile
FROM ubuntu

RUN apt-get update

# curl to dowload ansible 1.9.4 tarball (PPA registry contains latest absible
# 2.0 which does not work with our projects)
RUN apt-get --yes install curl

RUN mkdir /opt/ansible
RUN curl http://releases.ansible.com/ansible/ansible-1.9.4.tar.gz | tar xz -C /opt/ansible

# python to run ansible
# python-setuptools to setup  ansible via `python setup.py install`
# build-essential because pycrypto otherwise errors `no acceptable C compiler found in $PATH`
RUN apt-get --yes install python python-dev python-setuptools build-essential

WORKDIR /opt/ansible/ansible-1.9.4
RUN python setup.py install

ENTRYPOINT /bin/bash
```

To build it run `docker build -t ubuntu-ansible-1.9.4 .` and then run ``docker run -v `pwd`:/opt/sources -it ubuntu-ansible-1.9.4``
