ifneq (,$(wildcard /app/.devcontainer/.env))
    include /app/.devcontainer/.env
    export
endif

export ANSIBLE_HOST_KEY_CHECKING=false

export SITE := $(or $(SITE), ilude)

PASSED_COMMAND := $(firstword $(MAKECMDGOALS))
  
ifeq (role, $(PASSED_COMMAND))
	ANSIBLE_OVERRIDE_HOSTS := $(strip $(or $(word 3,$(MAKECMDGOALS)), localhost))
	ROLE_NAME := $(strip $(word 2,$(MAKECMDGOALS)))
else ifneq (,$(strip $(word 2,$(MAKECMDGOALS))))
	# set ANSIBLE_OVERRIDE_HOSTS to 2nd parameter passed to make
	ANSIBLE_OVERRIDE_HOSTS := --extra-vars "ansible_override_hosts=$(strip $(word 2,$(MAKECMDGOALS)))"
endif

export

ASSET_DIRS = $(shell find ansible-prereqs/ -type d)
ASSET_FILES = $(wildcard ansible-prereqs/*)

# use the rest as arguments as empty targets aka: MAGIC
EMPTY_TARGETS := $(wordlist 2,$(words $(MAKECMDGOALS)),$(MAKECMDGOALS))
$(eval $(EMPTY_TARGETS):;@:)

.PHONY: inventory $(PASSED_COMMAND)
.prereqs: .ansible_vault_password $(ASSET_DIRS) $(ASSET_FILES)
	ansible-galaxy install -f -r ansible-prereqs/requirements.yml
	pip install -r ansible-prereqs/requirements.txt
	touch $@

.ansible_vault_password:
	touch $@

inventory: .ansible_vault_password 
	ansible-playbook -i ./ansible-sites/$(SITE)/inventory ./books/update-known-hosts.yml --vault-password-file .ansible_vault_password

echo:
	env|sort

sudo: prereq
	ansible-playbook -i ./ansible-sites/$(SITE)/inventory --ask-become-pass ./books/setup-sudoers.yml --vault-password-file .ansible_vault_password

encrypt:
	ansible-vault encrypt --vault-password-file .ansible_vault_password ./ansible-sites/$(SITE)/vault.yml

edit-vars:
	EDITOR='nano' ansible-vault edit --vault-password-file .ansible_vault_password ./ansible-sites/$(SITE)/vault.yml

fix-ssh-perms:
	sudo chown -R $(shell id -u):$(shell id -g) ~/.ssh
	chmod 600 ~/.ssh/*
	chmod 700 ~/.ssh

role: .prereqs
	ansible $(ANSIBLE_OVERRIDE_HOSTS) --inventory-file ./ansible-sites/$(SITE)/inventory  --playbook-dir books/ --module-name include_role --args name=$(ROLE_NAME)

$(PASSED_COMMAND): .prereqs 
	ansible-playbook -i ./ansible-sites/$(SITE)/inventory ./books/$@.yml $(ANSIBLE_OVERRIDE_HOSTS) 
# -vvv  --vault-password-file .ansible_vault_password

FORCE: ;
