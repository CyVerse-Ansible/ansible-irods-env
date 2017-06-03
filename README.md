irods-env
=========
[![Build Status](https://travis-ci.org/CyVerse-Ansible/ansible-irods-env.svg?branch=master)](https://travis-ci.org/CyVerse-Ansible/ansible-irods-env)

This role creates an irods_environment.json file. It can create both the client for used by iCommands, iRODS FUSE client, etc., as well as the server form used by the iRODS servers.

It was originally created to generate environment files for iRODS 4.1.10, but it will likely work for any iRODS 4.1.x version.


Requirements
------------

None


Role Variables
--------------

Here are the role variables. None of them are required.

Variable                               | Default                                          | Choices                                         | Comments
-------------------------------------- | ------------------------------------------------ | ----------------------------------------------- | --------
`irods_env_for_server`                 | false                                            |                                                 | whether or not the file is for a server                        
`irods_env_file`                       | `ansible_env.HOME`/.irods/irods_environment.json |                                                 | the absolute path to the iRODS environment file to create
`irods_env_sys_user_name`              | `ansible_env.USER`                               |                                                 | the system user that will be using the iRODS environment file
`irods_env_sys_group_name`             | `irods_env_sys_user_name`                        |                                                 | the primary group of the system user that will be using the iRODS environment file
`irods_env_authentication_file`        |                                                  |                                                 | fully qualified path to a file holding the credentials of an authenticated iRODS user
`irods_env_authentication_scheme`      |                                                  | gsi, krb, native, pam                           | user's authentication method
`irods_env_client_server_negotiation`  |                                                  | none, request_server_negotiation                | whether or not advanced negotiation is desired
`irods_env_client_server_policy`       |                                                  | CS_NEG_DONT_CARE, CS_NEG_REFUSE, CS_NEG_REQUIRE | which SSL policy to use
`irods_env_control_plane_port`         |                                                  |                                                 | the port on which the control plane operates
`irods_env_control_plane_key`          |                                                  |                                                 | the encryption key required for communicating with the grid control plane
`irods_env_debug`                      |                                                  |                                                 | desired verbosity of the debug logging level
`irods_env_default_hash_scheme`        |                                                  | MD5, SHA256                                     | checksum scheme
`irods_env_default_resource`           |                                                  |                                                 | the name of the resource used for iRODS operations if one is not specified
`irods_env_encryption_algorithm`       |                                                  |                                                 | EVP-supplied encryption algorithm for parallel transfer
`irods_env_encryption_key_size`        |                                                  |                                                 | key size for parallel transport encryption
`irods_env_encryption_num_hash_rounds` |                                                  |                                                 | number of hash rounds for parallel transfer encryption
`irods_env_encryption_salt_size`       |                                                  |                                                 | salt size for parallel transfer encryption
`irods_env_gsi_server_dn`              |                                                  |                                                 | the distinguished name of the GSI server
`irods_env_host`                       | `inventory_hostname`                             |                                                 | a fully qualified domain name for the iRODS server to connect to
`irods_env_log_level`                  |                                                  |                                                 | desired verbosity of logging
`irods_env_match_host_policy`          |                                                  | compatible, strict                              | whether or not to require given hash scheme
`irods_env_plugins_home`               |                                                  |                                                 | directory to use for client-side plugins
`irods_env_port`                       | 1247                                             |                                                 | the port for the given iRODS zone
`irods_env_relative_cwd`               |                                                  |                                                 | the initial working collection relative to the /`irods_env_zone_name`, don't prefix with '/'
`irods_env_relative_home`              |                                                  |                                                 | the home collection relative to the /`irods_env_zone_name`, don't prefix with '/'
`irods_env_ssl_ca_certificate_file`    |                                                  |                                                 | location of a file of trusted CA certificates in PEM format
`irods_env_ssl_ca_certificate_path`    |                                                  |                                                 | location of a directory containing CA certificates in PEM format
`irods_env_ssl_certificate_chain_file` |                                                  |                                                 | the file containing the server's certificate chain
`irods_env_ssl_certificate_key_file`   |                                                  |                                                 | private key corresponding to the server's certificate in the certificate chain file
`irods_env_ssl_dh_params_file`         |                                                  |                                                 | the Diffie-Hellman parameter file location
`irods_env_ssl_verify_server`          |                                                  | cert, hostname, none                            | level of server certificate based authentication to perform
`irods_env_user_name`                  | anonymous                                        |                                                 | the user name within iRODS for this account
`irods_env_xmsg_host`                  |                                                  |                                                 | the host name of the XMessage server
`irods_env_xmsg_port`                  |                                                  |                                                 | the port of the XMessage server
`irods_env_zone_name`                  | tempZone                                         |                                                 | the name of the iRODS zone

If `irods_env_for_server` is set to `true`, some of the variables have different default values more appropriate for a server configuration.

Variable                               | Server Default
-------------------------------------- | --------------
`irods_env_file`                       | /var/lib/irods/.irods/irods_environment.json
`irods_env_sys_user_name`              | irods
`irods_env_client_server_negotiation`  | request_server_negotiation
`irods_env_client_server_policy`       | CS_NEG_DONT_CARE
`irods_env_control_plane_port`         | 1248
`irods_env_control_plane_key`          | TEMPORARY__32byte_ctrl_plane_key
`irods_env_default_hash_scheme`        | SHA256
`irods_env_default_resource`           | demoResc
`irods_env_encryption_algorithm`       | AES-256-CBC
`irods_env_encryption_key_size`        | 32
`irods_env_encryption_num_hash_rounds` | 16
`irods_env_encryption_salt_size`       | 8
`irods_env_match_host_policy`          | compatible
`irods_env_relative_cwd`               | `irods_env_relative_home`
`irods_env_relative_home`              | home/`irods_env_user_name`
`irods_env_user_name`                  | rods


Dependencies
------------

None


Example Playbook
----------------

Here's an example of creating irods_environment.json for a client.

    - hosts: clients
      roles:
        - role: irods-env
          irods_env_host: "{{ groups['ies'][0] }}"
          irods_env_user_name: tedgin
          irods_env_zone_name: iplant

Here's an example of creating irods_environment.json for an iRODS server.

    - hosts: rs
      roles:
        - role: irods-env
          irods_env_for_server: true
          irods_env_user_name: "{{ irods_adm_name }}"
          irods_env_zone_name: iplant
          irods_env_control_plane_key: "{{ irods_control_plane_key }}"
          irods_env_default_hash_scheme: MD5
          irods_env_default_resource: "{{ irods_default_resource }}"
    

License
-------

See license.md


Author Information
------------------

Tony Edgin

tedgin@cyverse.org

https://cyverse.org
