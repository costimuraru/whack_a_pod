# Copyright 2017 Google Inc. All Rights Reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
BASEDIR = $(shell pwd)

INGRESS_URL=“`kubectl get service nginx-ingress-controller -n nginx-ingress -o json | jq --raw-output ".status.loadBalancer.ingress[0].hostname"`”

include ../../Makefile.properties

deploy: env creds
	kubectl apply -f "$(BASEDIR)/ingress.yaml"

deploy.minikube:
	kubectl apply -f "$(BASEDIR)/ingress.minikube.yaml"

deploy.generic: 
	test -n "$(INGRESS_URL)" # No ingress url detected? $$INGRESS_URL
	sed -i '' "s/INGRESS_ENDPOINT_HERE/${INGRESS_URL}/g" "$(BASEDIR)/ingress.generic.yaml"
	kubectl apply -f "$(BASEDIR)/ingress.generic.yaml"

config: env
	@echo Creating INGRESS Yaml files based on samples and setting in your Makefile.properties
	@cp "$(BASEDIR)/ingress.sample.yaml" "$(BASEDIR)/ingress.yaml"
	$(call rewritefile,"$(BASEDIR)/ingress.yaml",%INGRESSNAME%,$(INGRESSNAME))

clean: env creds
	-kubectl delete -f "$(BASEDIR)/ingress.yaml"

clean.minikube:
	-kubectl delete -f "$(BASEDIR)/ingress.minikube.yaml"

clean.generic: 
	test -n "$(INGRESS_URL)" # No ingress url detected? $$INGRESS_URL
	sed -i '' "s/${INGRESS_URL}/INGRESS_ENDPOINT_HERE/g" "$(BASEDIR)/ingress.generic.yaml"
	-kubectl delete -f "$(BASEDIR)/ingress.generic.yaml"

define rewritefile
	@sed s/$(2)/$(3)/g <""$(1)"" >"$(BASEDIR)/.temp"
	@cp "$(BASEDIR)/.temp" $(1)
	@rm "$(BASEDIR)/.temp" 
endef