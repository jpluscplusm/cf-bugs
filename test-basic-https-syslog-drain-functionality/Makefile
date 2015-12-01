.DEFAULT: default
SHELL=bash
STABLE_RANDOM := $(shell echo $$RANDOM)

default:
	@echo 'First run: "make setup test".'
	@echo 'Repeat the test: "make test".'
	@echo 'Afterwards: "make clean".'

setup: announce-starting-setup create-sink
clean: announce-starting-clean-up delete-sink delete-generator delete-service delete-routes
test: \
  announce-cleaning-up-previous-tests delete-generator delete-service \
  announce-instantiating-test-entities create-service create-generator \
  announce-generating-log-traffic generate-traffic \
  announce-checking-for-unique-syslog-traffic check-logs announce-logs-found announce-tests-completed-successfully

create-sink:
	cf push -f manifest.yml.log-sink
create-service:
	cf cups test-log-service \
	  -l https://$$(cf app test-log-sink | awk '/^urls:/{print $$2}')/TEST-LOG-PATH/$(STABLE_RANDOM)
create-generator:
	cf push -f manifest.yml.log-generator
delete-sink:
	cf delete -f test-log-sink
delete-generator:
	cf delete -f test-log-generator
delete-service:
	cf delete-service -f test-log-service
delete-routes:
	cf delete-orphaned-routes # deliberately require operator input here ...

generate-traffic: export GENERATOR_FQDN=$(shell cf app test-log-generator | awk '/^urls:/{print $$2}')
generate-traffic:
	@for I in 1 2 3 4 5; do \
	  curl --write-out "%{url_effective} responded with HTTP %{http_code}\n" \
	  http://$${GENERATOR_FQDN}; \
	done

check-logs:
	sleep 5 # Waiting for logs to move around behind the scenes ...
	@cf logs test-log-sink --recent \
	  | grep -q TEST-LOG-PATH/$(STABLE_RANDOM) || { \
	  echo 'The string "TEST-LOG-PATH/$(STABLE_RANDOM)" was not found in the recent'; \
	  echo 'logs for the "test-log-sink" app. Tests failed.'; \
	  exit 1; \
	}

announce-%:
	@echo -e "\n***************************************"
	@echo -n "*** "
	@echo $@ | tr 'a-z-' 'A-Z ' | cut -d' ' -f2-
	@echo "***************************************"
