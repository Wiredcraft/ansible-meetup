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
ansible-galaxy install -r requirements.yml -p roles
```

### Complete the setup

```
# Install nginx / haproxy - base code
ansible-playbook -i inventory 00_setup.yml
```

## Run and deploy

Test in your browser:

- http://www.ansible-meetup.wiredcraft.net: shows `This is orig web1`
- http://new.ansible-meetup.wiredcraft.net: shows 503
- http://orig.ansible-meetup.wiredcraft.net: shows 503

### Deploy new code

```
# Deploy new code on web2
ansible-playbook -i inventory 01_deploy.yml
```

Test in your browser:

- http://www.ansible-meetup.wiredcraft.net: shows `This is orig web1`
- http://new.ansible-meetup.wiredcraft.net: shows `This is new web2`
- http://orig.ansible-meetup.wiredcraft.net: shows `This is orig web1`

### Migrate platform (DNS)

```
# Cut and move to green platform
ansible-playbook -i inventory 02_cut.yml
```

Test in your browser:

- http://www.ansible-meetup.wiredcraft.net: shows `This is new web2`
- http://new.ansible-meetup.wiredcraft.net: shows `This is new web2`
- http://orig.ansible-meetup.wiredcraft.net: shows `This is orig web1`

### Revert to old

```
# Rollback and move back to blue platform
ansible-playbook -i inventory 03_revert.yml
```

Test in your browser:

- http://www.ansible-meetup.wiredcraft.net: shows `This is orig web1`
- http://new.ansible-meetup.wiredcraft.net: shows `This is new web2`
- http://orig.ansible-meetup.wiredcraft.net: shows `This is orig web1`

## No error on cut / revert

```
while [ 1 ]; do 
  # On connect error, curl will throw an error
  curl --connect-timeout 0.3 http://www.ansible-meetup.wiredcraft.net
done
```
