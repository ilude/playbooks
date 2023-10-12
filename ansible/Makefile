ifneq (,$(wildcard /app/.devcontainer/.env))
    include /app/.devcontainer/.env
    export
endif

export ANSIBLE_HOST_KEY_CHECKING=false

PASSED_COMMAND := $(firstword $(MAKECMDGOALS))
# NOUN := $(lastword $(MAKECMDGOALS))
# SITE_NOUN := $(filter $(NOUN),$(SITES))

# INVENTORY_FILES := $(wildcard inventory.*)
# SITES := $(strip $(subst inventory., ,$(INVENTORY_FILES)))

# set ANSIBLE_OVERRIDE_HOSTS to 2nd parameter passed to make
export  
ifneq (,$(strip $(word 2,$(MAKECMDGOALS))))
	ANSIBLE_OVERRIDE_HOSTS := --extra-vars "ansible_override_hosts=$(strip $(word 2,$(MAKECMDGOALS)))"
endif

# use the rest as arguments as empty targets aka: MAGIC
EMPTY_TARGETS := $(wordlist 2,$(words $(MAKECMDGOALS)),$(MAKECMDGOALS))
$(eval $(EMPTY_TARGETS):;@:)

.PHONY: prereq inventory %.yml 

prereq: .ansible_vault_password
	ansible-galaxy install -r requirements.yml
	pip install passlib

.ansible_vault_password:
	touch $@

# site_check:
# 	ifeq ($(SITE),)
# 	$(error Need a value for SITE, e.g., export SITE=value)
# 	endif	

# site:	
# 	export SITE=$(SITE_NOUN)

inventory:
	ansible-playbook -i inventory.$(SITE) update-known-hosts.yml --vault-password-file .ansible_vault_password

echo:
	env|sort

sudo: prereq
	ansible-playbook -i inventory.$(SITE) --ask-become-pass setup-sudoers.yml --vault-password-file .ansible_vault_password

encrypt:
	ansible-vault encrypt --vault-password-file .ansible_vault_password vaults/$(SITE).yml

edit-vars:
	EDITOR='nano' ansible-vault edit --vault-password-file .ansible_vault_password vaults/$(SITE).yml

%.yml: prereq FORCE
	ansible-playbook -i inventory.$(SITE) $@ $(ANSIBLE_OVERRIDE_HOSTS) --vault-password-file .ansible_vault_password

FORCE: ;