.PHONY: help, ci-black, ci-flake8, isort, black

PROJECT=CSCI-4308-TerumoBCT
CONTAINER_NAME="CSCI-4308-TerumoBCT_bash_${USER}"  ## Ensure this is the same name as in docker-compose.yml file
VERSION_FILE:=VERSION
TAG:=$(shell cat ${VERSION_FILE})

help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

git-tag:  ## Tag in git, then push tag up to origin
	git tag $(TAG)
	git push origin $(TAG)

ci-black: ## Test lint compliance using black. Config in pyproject.toml file
	docker exec $(CONTAINER_NAME) black --check /mnt/CSCI_4308_TerumoBCT

ci-flake8: ## Test lint compliance using flake8. Config in tox.ini file
	docker exec $(CONTAINER_NAME) flake8 /mnt/CSCI_4308_TerumoBCT

ci: ci-black ci-flake8 ## Check black and flake8
	@echo "CI sucessful"

isort: ## Runs isort to sorts imports
	docker exec $(CONTAINER_NAME) isort -rc /mnt/CSCI_4308_TerumoBCT

black: ## Runs black auto-linter
	docker exec $(CONTAINER_NAME) black /mnt/CSCI_4308_TerumoBCT

lint: isort black ## Lints repo; runs black and isort on all files
	@echo "Linting complete"

dev-start: ## Primary make command for devs, spins up containers
	@echo "Building new images from compose"
	docker-compose -f docker/docker-compose.yml --project-name $(PROJECT) up -d --build

dev-stop: ## Spin down active containers
	docker-compose -f docker/docker-compose.yml --project-name $(PROJECT) down
