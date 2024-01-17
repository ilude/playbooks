ifneq (,$(wildcard /app/.devcontainer/.env))
    include /app/.devcontainer/.env
    export
endif

export ANSIBLE_HOST_KEY_CHECKING=false

PASSED_COMMAND := $(firstword $(MAKECMDGOALS))

# set ANSIBLE_OVERRIDE_HOSTS to 2nd parameter passed to make
export  
ifneq (,$(strip $(word 2,$(MAKECMDGOALS))))
	ANSIBLE_OVERRIDE_HOSTS := --extra-vars "ansible_override_hosts=$(strip $(word 2,$(MAKECMDGOALS)))"
endif

ASSET_DIRS = $(shell find ansible-prereqs/ -type d)
ASSET_FILES = $(wildcard ansible-prereqs/*)

# use the rest as arguments as empty targets aka: MAGIC
EMPTY_TARGETS := $(wordlist 2,$(words $(MAKECMDGOALS)),$(MAKECMDGOALS))
$(eval $(EMPTY_TARGETS):;@:)

.PHONY: inventory $(PASSED_COMMAND)
.prereqs: .ansible_vault_password $(ASSET_DIRS) $(ASSET_FILES)
	ansible-galaxy install -r ansible-prereqs/requirements.yml
	pip install -r ansible-prereqs/requirements.txt
	touch $@

.ansible_vault_password:
	touch $@

inventory: .ansible_vault_password 
	ansible-playbook -i ansible-sites/$(SITE)/inventory update-known-hosts.yml --vault-password-file .ansible_vault_password

echo:
	env|sort

sudo: prereq
	ansible-playbook -i ansible-sites/$(SITE)/inventory --ask-become-pass setup-sudoers.yml --vault-password-file .ansible_vault_password

encrypt:
	ansible-vault encrypt --vault-password-file .ansible_vault_password vaults/$(SITE).yml

edit-vars:
	EDITOR='nano' ansible-vault edit --vault-password-file .ansible_vault_password vaults/$(SITE).yml

$(PASSED_COMMAND): .prereqs 
	ansible-playbook -i ansible-sites/$(SITE)/inventory ./books/$@.yml $(ANSIBLE_OVERRIDE_HOSTS) --vault-password-file .ansible_vault_password

FORCE: ;