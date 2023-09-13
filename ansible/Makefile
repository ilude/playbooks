ifneq (,$(wildcard /app/.devcontainer/.env))
    include /app/.devcontainer/.env
    export
endif

ifeq ($(SITE),)
    $(error Need a value for SITE, e.g., export SITE=value)
endif

export ANSIBLE_HOST_KEY_CHECKING=false

PASSED_COMMAND := $(firstword $(MAKECMDGOALS))

.PHONY: prereq inventory %.yml 

prereq: 
	ansible-galaxy install -r requirements.yml
	pip install passlib

inventory:
	ansible-playbook -i inventory.$(SITE) update-known-hosts.yml

echo:
	env|sort

sudo: prereq
	ansible-playbook -i inventory.$(SITE) --ask-become-pass setup-sudoers.yml

%.yml: prereq FORCE
	ansible-playbook -i inventory.$(SITE) $@ 

FORCE: ;