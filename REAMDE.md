## Phising infra

Set up a phining tool with postfix, gophish in Debian-like systems.

#### Requrements

None

#### Variables
* `create_certbot_certificate` [default: `true`] Creates tls certificates with [letsencrypt](https://letsencrypt.org/)
* `gophish_version`: [default: `https://github.com/gophish/gophish/releases/download/v0.8.0/gophish-v0.8.0-linux-64bit.zip`] Gophish package url
* `gophish_admin_server_listen_url`: [default: `0.0.0.0:3333`] Gophish Admin interface
* `gophish_admin_server_use_tls`: [default: `false`] Enable TLS encryption on Gophish Admin interace
* `gophish_admin_server_cert_path`: [default: `letsencrypt_cert`] Path to cert.pem
* `gophish_admin_server_key_path`: [default: `letsencrypt_key`] Path to key.pem
* `phish_server_listen_url`: [default: `"{{ '0.0.0.0:443' | default('0.0.0.0:80', phish_server_use_tls)}}"`]
* `phish_server_use_tls`: [default: `false`]
* `phish_server_cert_path`: [default: `letsencrypt_cert`] Path to cert.pem
* `phish_server_key_path`: [default: `letsencrypt_key`] Path to key.pem

For postfix variables please see [postfix - ansible role](https://github.com/Oefenweb/ansible-postfix)

## Dependencies
- [gophish phishing toolkit](https://github.com/gophish/gophish)
- [postfix - ansible role](https://github.com/Oefenweb/ansible-postfix)

#### Example(s)

A simple example that use SASL authentication and TLS encryption on the phishing page:

```  
  hosts: all
  become: true
  become_method: sudo
  connection: ssh
  roles:
    - certs
    - gophish
    - postfix
  vars:
    phish_domain: gooogle.com
    postfix_hostname: gooogle.com
    postfix_mailname: "{{ phish_domain }}"
    postfix_aliases:
      - user: root
        alias: "no-replay@{{ phish_domain }}"
    postfix_sasl_password: test_passw0rd!
    postfix_inet_protocols: ipv4
    postfix_smtpd_relay_restrictions: ["permit_mynetworks", "permit_sasl_authenticated", "reject_unauth_destination"]
    gophish_admin_server_use_tls: true
    phish_server_use_tls: true
```