ifneq (,$(wildcard /app/.devcontainer/.env))
    include /app/.devcontainer/.env
    export
endif

ifeq ($(SITE),)
    $(error Need a value for SITE, e.g., export SITE=value)
endif

export ANSIBLE_HOST_KEY_CHECKING=false

PASSED_COMMAND := $(firstword $(MAKECMDGOALS))

# set ANSIBLE_OVERRIDE_HOSTS to 2nd parameter passed to make
export ANSIBLE_OVERRIDE_HOSTS := $(strip $(word 2,$(MAKECMDGOALS)))

# use the rest as arguments as empty targets aka: MAGIC
EMPTY_TARGETS := $(wordlist 2,$(words $(MAKECMDGOALS)),$(MAKECMDGOALS))
$(eval $(EMPTY_TARGETS):;@:)

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
	ansible-playbook -i inventory.$(SITE) $@ --extra-vars "ansible_override_hosts=$(ANSIBLE_OVERRIDE_HOSTS)"

FORCE: ;