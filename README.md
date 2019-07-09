# nephelaiio.playbook-awx

[![Build Status](https://travis-ci.org/nephelaiio/ansible-playbook-awx.svg?branch=master)](https://travis-ci.org/nephelaiio/ansible-playbook-awx)

A set of [ansible playbooks](https://galaxy.ansible.com/nephelaiio/ansible-playbook-awx-local) to install and configure [Ansible AWX](https://github.com/ansible/awx).

## Playbook Variables

The following parameters are available/required for playbook invocation

### [local.yml](local.yml):
| required | variable          | description                                  | default |
| ---      | ---               | ---                                          | ---     |
| no       | awx_release       | the fqdn to generate an acme certificate for | '4.0.0' |
| no       | awx_pg_user       | the postgresql connection user               | 'awx'   |
| *yes*    | awx_pg_pass       | the postgresql connection password           | n/a     |
| no       | awx_admin_user    | the awx administrator user                   | 'admin' |
| *yes*    | awx_admin_pass    | the awx administrator password               | n/a     |
| no       | awx_rabbitmq_user | the awx administrator user                   | 'awx    |
| *yes*    | awx_rabbitmq_pass | the awx administrator password               | n/a     |

### [nginx.yml](nginx.yml):
| required | variable | description | default |
| --- | --- | --- | --- |
| *yes* | awx_url | the awx url | ansible_fqdn |
| *yes* | acme_certificate_aws_accesskey_id | an ec2 key id with route53 management rights | lookup('env', 'AWS_ACCESS_KEY_ID') |
| *yes* | acme_certificate_aws_accesskey_secret  | an ec2 key secret | lookup('env', 'AWS_SECRET_ACCESS_KEY') |

## Dependencies

This playbook has the following git submodule dependencies:

* [submodules/awx](https://github.com/ansible/awx)

And the following role dependencies by play (no dependencies if play is not listed):

### [local.yml](local.yml):
* [nephelaiio.plugins](https://galaxy.ansible.com/nephelaiio/plugins)
* [nephelaiio.docker](https://galaxy.ansible.com/nephelaiio/docker)
* [nephelaiio.pip](https://galaxy.ansible.com/nephelaiio/pip)

### [nginx.yml](nginx.yml):
* [nginxinc.nginx](https://galaxy.ansible.com/nginxinc/nginx)
* [nephelaiio.acme_certificate_route53](https://galaxy.ansible.com/nephelaiio/acme_certificate_route53)

See the [requirements](https://raw.githubusercontent.com/nephelaiio/ansible-role-requirements/master/requirements.txt) and [meta](meta.yml) files for more details

## Example Invocation

```
git checkout https://galaxy.ansible.com/nephelaiio/ansible-playbook-awx awx
ansible-playbook -i inventory/ awx/local.yml
```

## Testing

Please make sure your environment has [docker](https://www.docker.com) installed in order to run role validation tests. Additional python dependencies are listed in the [requirements](https://raw.githubusercontent.com/nephelaiio/ansible-role-requirements/master/requirements.txt)

This role is tested automatically against the following distributions (docker images):

  * Ubuntu Bionic
  * Ubuntu Xenial

You can test the role directly from sources using command ` molecule test `

## TODO
Add support for AWX settings; i.e:

AUTH_LDAP_SERVER_URI: "ldap://ldap01.{{ awx_ldap_domain_0 }}:389 ldap://ldap02.{{ awx_ldap_domain_0 }}:389"
AUTH_LDAP_USER_ATTR_MAP: "{'first_name': 'givenName', 'last_name': 'sn', 'email': 'mail'}"
AUTH_LDAP_GROUP_TYPE: "ActiveDirectoryGroupType"
AUTH_LDAP_GROUP_TYPE_PARAMS: "{}"
AUTH_LDAP_GROUP_SEARCH: "['ou=Security Groups,{{ awx_ldap_domain_dn_0 }}', 'SCOPE_SUBTREE', '(objectClass=group)']"
AUTH_LDAP_USER_SEARCH: "['ou=Accounts,{{ awx_ldap_domain_dn_0 }}', 'SCOPE_SUBTREE', '(sAMAccountName=%(user)s)']"
AUTH_LDAP_USER_FLAGS_BY_GROUP: "{'is_superuser': 'cn=AWX Admins,ou=Security Groups,{{ awx_ldap_domain_dn_0 }}'}"
AUTH_LDAP_REQUIRE_GROUP: "cn=AWX Users,ou=Security Groups,{{ awx_ldap_domain_dn_0 }}"
AUTH_LDAP_BIND_DN: "{{ awx_ad_bind_user_0 }}"
AUTH_LDAP_BIND_PASSWORD: "{{ awx_ad_bind_pass_0 }}"
AUTH_LDAP_TEAM_MAP: "{'AWX Local Users': {'organization': '{{ awx_ldap_domain_0 }}', 'users': 'cn=AWX Users,ou=Security Groups,{{ awx_ldap_domain_dn_0 }}', 'remove': true }}"

REMOTE_HOST_HEADERS: "[u'REMOTE_ADDR', u'REMOTE_HOST', u'HTTP_X_FORWARDED_FOR']"
PROXY_IP_WHITELIST: "{{ bigip_query.self_ips | json_query(selfip_query) | map('split_with', '%') | map('first') | list | string }}"
TOWER_URL_BASE: "{{ awx_url_local }}"

## License

This project is licensed under the terms of the [MIT License](/LICENSE)
