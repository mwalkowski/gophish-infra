# Phishing Toolbox with Gophish, Certbot, and Postfix

## Overview
gophish-infra is an automation toolset prepared to simplify the deployment of [Gophish](https://github.com/gophish/gophish), an open-source phishing toolkit. This project aims to eliminate the repetitive setup phase associated with configuring Gophish and its dependencies, enabling users to quickly proceed to conducting realistic phishing tests.

Gophish is a powerful tool for security professionals to create and conduct phishing simulations. It allows for the testing of employees' security awareness and preparedness against phishing attacks, which are among the most common cybersecurity threats today.

## Key Features
* **Rapid Deployment:** Streamlines the setup process, significantly reducing the time to launch phishing simulations.
* **Automatic TLS Certificate Creation:** Utilizes Let's Encrypt to automatically generate TLS certificates for the phishing site, improving its credibility and security.
* **SMTP Server Configuration:** Allows for the configuration of an SMTP server to receive emails from targeted users, adding realism to the simulations.
* **Security First:** Ensures that all operations and configurations adhere to the latest security standards.

## Getting Started
#### Requrements
- Phising domain (nip.io can be also used)
- Server with public IP and Debian-like systems
- Ansible

### Installation
1. Clone the repository:
```bash
git clone --recurse-submodules https://github.com/yourusername/gophish-infra.git
cd gophish-infra
```

2. Configure variables:
* Copy the playbook.example.yml file to playbook.yml
* Edit the conf.yml file to match your setup, specifying your domain and email for Let's Encrypt.
* Edit the inventory file

3. Run
```bash
ansible-playbook conf.yml -i inventory  -u usename --ask-pass
```

### Usage
After installation, access the Gophish admin interface at https://yourdomain.com:3333. Use the default credentials provided in the Gophish documentation to log in, and begin configuring your phishing campaign.

## Postconfig
- Configure spf record in domain provider
- Configure mx record in domain provider


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


#### Example(s)

A simple example that use SASL authentication and TLS encryption on the phishing page:

```
# ansible-playbook example.yml -i inventory  -u username --ask-pass
---
- name: Example playbook
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
      - user: no-replay
        alias: "no-replay@{{ phish_domain }}"
    postfix_sasl_password: test_passw0rd!
    postfix_inet_protocols: ipv4
    postfix_smtpd_relay_restrictions: ["permit_mynetworks", "permit_sasl_authenticated", "reject_unauth_destination"]
    gophish_admin_server_use_tls: true
    phish_server_use_tls: true
```

## Dependencies
- [gophish phishing toolkit](https://github.com/gophish/gophish)
- [postfix - ansible role](https://github.com/Oefenweb/ansible-postfix)

### License
Distributed under the MIT License. See `LICENSE` for more information.

### Acknowledgments
[Gophish](https://github.com/gophish/gophish) for providing the foundational phishing toolkit.
[Let's Encrypt](https://letsencrypt.org/) for enabling secure, automated TLS certificates.
