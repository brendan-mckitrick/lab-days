# VARIABLES
POSTGRES_PASSWORD ?= POSTGRES_PASSWORD


ARGC := $(shell printf $(words $(MAKECMDGOALS)))
TF_COMPONENT ?= $(wordlist 2,2,$(MAKECMDGOALS))
ARGV_FILTER := $(filter-out $(wordlist 1,1,$(MAKECMDGOALS)),$(MAKECMDGOALS))
ARGV := $(filter-out $(wordlist 1,1,$(TF_COMPONENT)),$(ARGV_FILTER))
ifeq (1, $(ARGC))
COMPOSE_RUN = docker-compose run timescaledb
TF_COMPONENT := "."
else
COMPOSE_RUN = docker-compose run timescaledb
endif


# TARGETS
help:	## display this help
	@awk 'BEGIN {FS = ":.*##"; printf "\nUsage:\n  make \033[36m<target> <component> <arguments>\033[0m\n\nTargets:\n"} /^[a-zA-Z_-]+:.*?##/ { printf "  \033[36m%-8s\033[0m %s\n", $$1, $$2 } END{print ""}' $(MAKEFILE_LIST)


# match the current timescaledb stages
up: .env .validate
	@docker-compose up -d

up-debug: .env .validate
	@docker-compose up

down:
	@docker-compose down

restart: down up
	@echo "Restarted"

logs:
	@docker-compose logs --follow

shell: .env
	docker-compose run --entrypoint=psql db -U postgres

clean:	## remove local state and modules
	@[ ! -f .env ] && touch .env || echo -n
	docker-compose run --entrypoint="rm -rf .gem" timescaledb
	docker-compose down
	[ "$(ARGC)" -eq 1 ] && rm -f .env || true

yace:
	docker run -d --rm -v $PWD/credentials:/exporter/.aws/credentials -v $PWD/config.yml:/tmp/config.yml \
-p 5000:5000 --name yace ghcr.io/nerdswords/yet-another-cloudwatch-exporter:v0.30.1-alpha


.env:
	@env | grep "AWS_" > .env || true
	@env | grep "TF_" >> .env || true

.validate:
	docker-compose run --entrypoint promtool prometheus check config /etc/prometheus/prometheus.yml

%:
	@:

.PHONY: help shell clean .env publish build
.DEFAULT_GOAL:=help
