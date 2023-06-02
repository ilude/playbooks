ifneq (,$(wildcard /app/.devcontainer/.env))
    include /app/.devcontainer/.env
    export
endif

PASSED_COMMAND := $(firstword $(MAKECMDGOALS))

requirements:
	ansible-galaxy install -r requirements.yml
	pip install passlib

.PHONY: inventory
inventory:
	ansible-playbook -i inventory update-known-hosts.yml

echo:
	env|sort

sudo: requirements inventory
	ansible-playbook -i inventory --ask-become-pass setup-sudoers.yml

%.yml: requirements inventory
	ansible-playbook -i inventory $@