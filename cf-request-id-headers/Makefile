default: setup test clean

setup:
	cf push

test: HEADER?=X-Cf-Requestid
test: HEADER?=X-Vcap-Request-Id
test: FQDN=$(shell cf app cf-request-id-headers-test | awk '/^urls:/{print $$2}')
test: 
	for I in 1 2 3 4 5; do \
	  echo "*** Test $$I: (checking for $(HEADER))" ;\
	  curl -v $(FQDN) 2>&1 \
	  | grep $(HEADER) \
	  | sed 's/^N/Observed inside CF  :/' \
	  | sed 's/^</Observed by consumer:/' ;\
	  echo ;\
	done

clean:
	cf delete -f -r cf-request-id-headers-test
