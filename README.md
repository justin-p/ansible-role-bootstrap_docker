# ansible-role-bootstrap-docker

[![Ansible Role Name](https://img.shields.io/ansible/role/51176?label=Role%20Name&logo=ansible&style=flat-square)](https://galaxy.ansible.com/justin_p/bootstrap_docker)
[![Ansible Quality Score](https://img.shields.io/ansible/quality/51176?label=Ansible%20Quality%20Score&logo=ansible&style=flat-square)](https://galaxy.ansible.com/justin_p/bootstrap_docker)
[![Ansible Role Downloads](https://img.shields.io/ansible/role/d/51176?label=Ansible%20Role%20Downloads&logo=ansible&style=flat-square)](https://galaxy.ansible.com/justin_p/bootstrap_docker)
[![Github Actions](https://img.shields.io/github/workflow/status/justin-p/ansible-role-bootstrap-docker/CI?label=Github%20Actions&logo=github&style=flat-square)](https://github.com/justin-p/ansible-role-bootstrap-docker/actions)

A Ansible role I build for quickly configuring and hardening docker on a new VM.

If you where to use this role you probably don't want to use the default password/salt values. Overwrite these in each project with unique values and store them securely with Ansible Vault.   
After enabeling `userns-remap` you are no longer able to mount the docker sock in side of a container. To solve this we create a copy of the socket with socat under the following path `/var/run/docker-userns.sock`. This copy gives the root account and dockerremap group read and write on the copied docker socket.  
I'd recommend not using this copy directly in target containers (i.e. traefik), but using it in combination with [Docker Socket Proxy](https://github.com/Tecnativa/docker-socket-proxy). This ensures only the bare minumium of what you need is available on the socket in the target container.

## Role Variables

| Variable                         | Description                                                                                                                       | Default value                                          |
| :------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------- |
| bsd_sshkey_folder                | Local folder where the ssh keypair for bsh_docker_public_key_path is stored.                                                      | ~/.ssh                                                 |
| bsd_docker_gid                   | The group id of the newly created docker user.                                                                                    | 1337                                                   |
| bsd_docker_uid                   | The user id of the newly created docker user.                                                                                     | 1337                                                   |
| bsd_docker_password              | The password of the docker user.                                                                                                  | 123c2b9e024723391cf60279c5eb84e4!                      |
| bsd_docker_password_salt         | The password salt of the docker user.                                                                                             | 3204aa9ef01b8f2c62aaaa2613696b$                        |
| bsd_docker_public_key_path       | The path to the local public key that should be added to the admin user.                                                          | {{ bsd_sshkey_folder }}/id_rsa.pub                     |
| bsd_docker_volume_path           | The path to a newly formated disk to mount `/var/lib/docker` on. Note, if a invalid path is supplied no mount action takes place. | /dev/disk/by-id/scsi-0DO_Volume_volume                 |
| bsd_docker_remap_socket_template | The template file for the docker remap socket.                                                                                    | {{ role_path }}/templates/dockerremapsocket.service.j2 |
| bsd_docker_deamon_template       | The template for the docker daemon.                                                                                               | {{ role_path }}/templates/daemon.json.j2               |
| bsd_docker_edition               | The edition of docker to install.                                                                                                 | ce                                                     |

## Example Playbook

```yaml
---
- hosts: all
  name: Bootstrap docker
  tasks:
      - include_role:
          name: bootstrap-docker
        vars:
          bsd_docker_username: "{{ docker_username }}"
          bsd_docker_password: "{{ docker_password }}"
          bsd_docker_password_salt: "{{ docker_password_salt }}"
          bsd_docker_public_key_path: "{{ docker_public_key_path }}"
          bsd_docker_volume_path: "/dev/disk/by-id/scsi-0DO_Volume_{{ do_docker_volume_name }}"
```

## License

MIT

## Author Information

- Justin Perdok ([@justin-p](https://github.com/justin-p/))

## Contributing

Feel free to open issues, contribute and submit your Pull Requests. You can also ping me on Twitter ([@JustinPerdok](https://twitter.com/JustinPerdok)).
