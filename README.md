# Ansible Docker Jupyter Remote
[![Work with Ansible](https://img.shields.io/badge/Work%20with-Ansible-brightgreen.svg)](https://img.shields.io/badge/Work%20with-Ansible-brightgreen.svg) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) 

An Ansible playbook deploys Jupyter notebook to remote hosts. For local development, please see my another project, [Ansible Docker Jupyter Local](https://github.com/yungshun317/ansible-docker-jupyter-local).

## Requirements

|Package|Version|  
|:-----:|:-----:|  
|python2|2.7.12|  
|ansible|2.7.2|

## Usage
Run `ansible-playbook`.
```sh
$ ansible-playbook jupyter.yml
```

## Overview
The playbook does these locally:
1. Ensure Python 2.7 is installed for running Ansible.
2. Install `pip` and `docker.io` with `apt`.
3. Install Docker Python library with `pip`.
4. Add user to the docker group and reboot.
5. Copy Dockerfile to remote, build image with `docker_image`.
6. Remove container if it already exists.
7. Create a directory for mounting host volume.
8. Get UID and GID passing them into container's `env`: `NB_UID` and `NB_GID`. Run the container.
```yaml
- name: get UID
  shell: 'id -u ccma'
  register: get_uid

- name: get GID
  shell: 'id -g ccma'
  register: get_gid
  
- name: Run a jupyter notebook container
  docker_container:
    image: notebook:jupyter
    name: notebook
    state: started
    ports: "8888:8888"
    volumes:
      - /home/ccma/workspace:/home/jovyan/work
    env:
      NB_UID: "{{ get_uid.stdout }}"
      NB_GID: "{{ get_gid.stdout }}"
```
Note the strange path, `/home/jovyan/work`.
9. Execute the command, `jupyter notebook list`, in Docker container to get necessary token for logging in.

## Tech
This project uses:
* [Ansible](https://www.ansible.com/) - Ansible is open source software that automates software provisioning, configuration management, and application deployment.
* [Jupyter Notebook](https://jupyter.org/) - an open-source web application that allows you to create and share documents that contain live code, equations, visualizations and narrative text.

## Todos
 - Try to write an Ansible Role.

## License
[Ansible Docker Jupyter Remote](https://github.com/yungshun317/ansible-docker-jupyter-remote) is released under the [MIT License](https://opensource.org/licenses/MIT) by [yungshun317](https://github.com/yungshun317).
