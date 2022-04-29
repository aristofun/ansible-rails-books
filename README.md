# Simple VPS provisioning for simple programmers

*In honor of all those brave programmers who have disappeared into devops swamps* 😀 just kidding
 
```
1. Edit hosts.yml, edit options in up group_vars/*.yaml
2. ansible-playbook  .ansible/books/go.yml
3. Your servers are ready deployment and work
4. PROFIT
```

No more time wasted f\*cking with configs, learning docker (chef, puppet-shmuppet, you name it...), 
analyzing logs, googling linux recipes, ssh-ing back and forth.   

Tested on clean Ubuntu 16.04 64bit so far. Might as well work with some updates on Ubuntu 14.04, 18.04. 
You're welcome to contribute, fix and improve!

# How to

You need [ansible 2.6 installed](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) on your provisioning machine (your computer).

### 1. Copy 

Copy `.ansible/`, `ansible.cfg` to your rails app folder or separate folder. Rails `database.yml`, `secrets.yml` are expected to be in their default locations (configurable). 

### 2. Configuration

Editing config files before running the scripts is all you have to do, but you have to do it.  

`ansible.cfg` — ansible environment, edit paths if neccessary  
`.ansible/hosts.yml` — the list of your servers to provision
`.ansible/group_vars/*.yml` — **main configs** defining what will be actually going on

*group_vars/*.yml* filename == hosts group in *hosts.yml*

Variables declared in `group_vars/all.yml` are applied to all groups (all your hosts).

**IMPORTANT**:  you need to have SSH root access to all provisioned hosts 

**Idea**  
Each YAML in `.ansible/books/` is a set of options (rules, commands) to be applied one by one to the hosts being provisioned.

Most options are checked before being applied. Let's say if your node already has postgres-9.6 installed it would not be installed again. Most ansible tasks are [*idempotent*](https://docs.ansible.com/ansible/latest/reference_appendices/glossary.html). 

These books are tested against clean fresh ubuntu nodes. It could work with already running and configured nodes, or it could break them.

Each book is intended to run on specific group. Please take a brief look inside the books.

### 3. Run

All books bundled in a single script:

```
ansible-playbook  .ansible/books/go.yml --ask-become-pass
```

Must type in your *deploy* user SUDO pass (the one you've chosen on a configuration step).

Now you're ready to take it from there and deploy your app as usual (capistrano etc.)

---

You may run books individually for specific goals (just set up redis nodes etc.)

```
ansible-playbook  .ansible/books/<book_file>.yml --ask-become-pass
```
 
### 4. Troubleshooting

It's usually not a problem to run same book again and again until error would be fixed. 
But be carefull with your important servers — nobody's responsible for your data but you!

* narrow down the problem to specific playbook + host + group_vars file
* fix configs accordingly
* read links provided in config file carefully
* if you want more, you would need to learn ansible more
* pull-request your findings to this repo!

### 5. Improve & contribute

Got a bug, have an idea, have written additional book? Please share and pull-request! 

Applications come and go, karma is forever.


# Important stuff

All the code is provided as is. Use it with care at your own risk. Authors are not responsible for anything. 

Ansible Galaxy roles used (bundled):
 
- anxs.postgresql 
- geerlingguy.certbot 
- DavidWittman.redis 
- nickhammond.logrotate
- Oefenweb.swapfile
- zzet.rbenv

Respect and thanks to the authors!  

# Feature list

What these playbooks can do: feature / hosts group name.

* Basic system configuration / all host groups
  - timezone
  - locale
  - disable password login
  - Apt upgrades
* Deploy user provisioning / all host groups
  - ssh authorized keys automation
  - sudo passwords
* Passenger-Nginx setup / `passenger-nginx-app*` group
  - configs managemant
  - virtual hosts provisioning
  - multi rails apps mode
* Rails application / `passenger-nginx-app*` group
  - logs and logrotate management
  - folders set up
  - capistrano deployment support
  - HTTPS nginx auto-setup with LetsEncrypt Certbot (**New**).
* Postgres / `postgres` group
  - user setup from `database.yml`
  - database setup from `database.yml`
  - Full app Postgres DB backup/restore (**TBD!**) 
* Postfix standalone send-only mode / `postfix-dkim` group 
* DKIM / `postfix-dkim` group
  - postfix integration
  - multidomain keys management 
* Redis / `redis` group
* rbenv + Ruby / `passenger-nginx-app*` group
  - multiple hosts multiple apps support 
* Ajenti panel / `ajenti` group
* Misc 
  - dropbox uploader (**TBD!**)
  - crontab options / all hosts groups 
  - swapfile management / all hosts groups
  - kernel SHMALL SHMMAX management / `postgres` group
