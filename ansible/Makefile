ifneq (,$(wildcard /app/.devcontainer/.env))
    include /app/.devcontainer/.env
    export
endif

PASSED_COMMAND := $(firstword $(MAKECMDGOALS))

requirements:
	ansible-galaxy install -r requirements.yml

echo:
	env|sort

%: requirements
	ansible-playbook -i inventory $@.yml