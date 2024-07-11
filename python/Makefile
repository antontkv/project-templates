.ONESHELL:

.PHONY: help
help: ## Show help
	@egrep '^[\.a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-20s\033[0m %s\n", $$1, $$2}'

.PHONY: setup
setup: ## Setup environment
	@if ! python3 --version | grep -c "3\.12" > /dev/null; then \
		echo "$$(tput setaf 1)Use Python 3.12$$(tput sgr0)"; \
		exit; fi
	@if ! [ -d ".venv" ]; then python3 -m venv .venv; fi
	@touch requirements.txt
	@touch requirements.in
	@. .venv/bin/activate && \
	pip install --upgrade pip && \
	pip install pip-tools && \
	pip install --upgrade pip-tools && \
	if ! [ -f requirements-dev.txt ]; then pip-compile --resolver=backtracking requirements-dev.in; fi && \
	pip-sync requirements-dev.txt && \
	pre-commit install && \
	pre-commit run --all-files

.PHONY: requirements
requirements: ## Resolve dependencies from .in files and update environment
	@.venv/bin/pip-compile --resolver=backtracking --strip-extras
	@.venv/bin/pip-compile --resolver=backtracking --strip-extras requirements-dev.in
	@.venv/bin/pip-sync requirements.txt requirements-dev.txt

.PHONY: lint
lint: ## Launch ruff
	@echo "---Run Ruff Format---"
	-@.venv/bin/ruff format .
	@echo "---Run Ruff---"
	-@.venv/bin/ruff check --fix --show-fixes --exit-non-zero-on-fix . && echo "Ruff OK"
