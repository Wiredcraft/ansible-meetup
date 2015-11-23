# Green blue deploy with ansible

## Pre-requisite

- 3 hosts: 
  - web1 -- update IP address in `host_vars`
  - web2 -- update IP address in `host_vars`
  - ha -- update IP address in `host_vars`

## Setup

### Fetch ansible roles

```
# Install galaxy roles
ansible-galaxy -r requirements.yml -p roles
```

### Complete the setup

```
# Install nginx / haproxy
ansible-playbook -i inventory setup.yml
```

## 
