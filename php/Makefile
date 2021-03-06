.PHONY: help pull build test tag images push

TARGETS:=$(MAKEFILE_LIST)
ITSELF:=$(lastword $(MAKEFILE_LIST))
MAKEFILE_PATH:=$(abspath $(lastword $(TARGETS)))
.DEFAULT_GOAL:=help

include .env
.env:

help: ## This help
	@grep -E '^[a-z A-Z_-]+:.*?## .*$$' $(TARGETS) | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-20s\033[0m %s\n", $$1, $$2}'

FQIN=karibbu/${IMAGE_NAME}:${TAG}
LATEST=karibbu/${IMAGE_NAME}:latest

pull: ## Pull docker image
	@docker pull ${FQIN} > /dev/null 2>&1 || (echo "${FQIN} does not exist yet. Will build it." && echo "IMAGE_BUILT=true" >> .env)

build: ## Build docker image. Use f=true to force
	@if [ "$(shell docker image ls -q ${FQIN})" = "" ] || [ "${f}" = "true" ]; then \
		docker build --build-arg FROM_FQIN=${FROM_FQIN} \
					 --build-arg COMPOSER_VERSION=${COMPOSER_VERSION} \
					 --build-arg PRESTISSIMO_VERSION=${PRESTISSIMO_VERSION} \
					 --build-arg PHPCSFIXER_VERSION=${PHPCSFIXER_VERSION} \
					 --build-arg PHPSTAN_VERSION=${PHPSTAN_VERSION} \
					 --build-arg APCUBC_VERSION=${APCUBC_VERSION} \
					 --build-arg RAPHF_VERSION=${RAPHF_VERSION} \
					 --build-arg DS_VERSION=${DS_VERSION} \
					 --build-arg AMQP_VERSION=${AMQP_VERSION} \
					-t ${FQIN} .; \
	fi

test: ## Test built docker image
	docker run --rm ${FQIN} php --version;
	docker run --rm ${FQIN} php -m;

tag: ## Make a tag alias latest to the built image
	@docker tag ${FQIN} ${LATEST}

images: ## List built images
	docker image ls karibbu/${IMAGE_NAME}

push: ## Push built images
	@if [ "$(IMAGE_BUILT)" = "true" ]; then \
		docker login --username="$(DOCKER_HUB_USERNAME)" --password="$(DOCKER_HUB_PASSWORD)"; \
		docker push ${FQIN}; \
		docker push ${LATEST}; \
	fi