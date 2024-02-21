SHELL:=/usr/bin/env bash

.PHONY: help
# Run "make" or "make help" to get a list of user targets
# Adapted from https://marmelab.com/blog/2016/02/29/auto-documented-makefile.html
help: ## Show this help message
	@grep -E '^[a-zA-Z_-]+:.*?##.*$$' $(MAKEFILE_LIST) | awk 'BEGIN { \
	 FS = ":.*?## "; \
	 printf "\033[1m%-30s\033[0m %s\n", "TARGET", "DESCRIPTION" \
	} \
	{ printf "\033[32m%-30s\033[0m %s\n", $$1, $$2 }'

.PHONY: all
all: install-tool validate-all convert-all ## Validate ../src files and convert them in ../release

#
# Dependencies
#
OSCAL_CLI_VERSION:=1.0.3
OSCAL_CLI_BIN:=oscal-cli
OSCAL_CLI_INSTALL_URL:=https://repo1.maven.org/maven2/gov/nist/secauto/oscal/tools/oscal-cli/cli-core/$(OSCAL_CLI_VERSION)/cli-core-$(OSCAL_CLI_VERSION)-oscal-cli.zip
OSCAL_CLI_INSTALL_PATH:=./oscal-cli
$(OSCAL_CLI_INSTALL_PATH):
	@echo Downloading OSCAL CLI Tool...
	@mkdir -p $(OSCAL_CLI_INSTALL_PATH)
	@curl $(CURL_INSTALL_OPTS) -o $(OSCAL_CLI_INSTALL_PATH)/oscal-cli.zip $(OSCAL_CLI_INSTALL_URL)
	@unzip -o $(OSCAL_CLI_INSTALL_PATH)/oscal-cli.zip -d $(OSCAL_CLI_INSTALL_PATH)
	@chmod +x $(OSCAL_CLI_INSTALL_PATH)/bin/$(OSCAL_CLI_BIN)

CURL_INSTALL_OPTS:=--silent --location

SRC_DIR:=../src
RELEASE_DIR:=../release

#
# OSCAL CLI (oscal-cli) tool
#
.PHONY: install-tool
install-tool: $(OSCAL_CLI_INSTALL_PATH) ## Install oscal-cli tool from Maven

#
# Validate OSCAL ../src files with oscal-cli
#
.PHONY: validate-all
validate-all: $(OSCAL_CLI_INSTALL_PATH) validate-example validate-eucs ## Validate all files in ../src using OSCAL CLI Tool

.PHONY: validate-examples
validate-examples: $(OSCAL_CLI_INSTALL_PATH) 
	@echo "Validating XML files in the ../src/examples directory using OSCAL CLI Tool"
	@$(OSCAL_CLI_INSTALL_PATH)/bin/$(OSCAL_CLI_BIN) --version
	@echo "latest oscal-cli version is $(OSCAL_CLI_VERSION)"
	@find $(SRC_DIR)/examples -mindepth 1 -maxdepth 1 -type d | while read example_dir; do \
		example_type=$$(basename "$$example_dir"); \
		echo "Processing example type: $$example_type"; \
		find "$$example_dir" -name '*.xml' | while read example_file; do \
			echo "Validating $$example_file with OSCAL CLI as $$example_type"; \
			$(OSCAL_CLI_INSTALL_PATH)/bin/oscal-cli "$$example_type" validate "$$example_file"; \
		done \
	done

.PHONY: validate-eucs
validate-eucs: $(OSCAL_CLI_INSTALL_PATH) 
	@echo "Validating XML files in the ../src directory using OSCAL CLI Tool"
	@$(OSCAL_CLI_INSTALL_PATH)/bin/$(OSCAL_CLI_BIN) --version
	@echo "latest oscal-cli version is $(OSCAL_CLI_VERSION)"
	@find $(SRC_DIR)/eucs/$*.xml -mindepth 1 -maxdepth 1 -type d | while read eucs_dir; do \
		eucs_type=$$(basename "$$eucs_dir"); \
		echo "Processing example type: $$eucs_type"; \
		find "$$eucs_dir" -name '*.xml' | while read eucs_file; do \
			echo "Validating $$eucs_file with OSCAL CLI as $$eucs_type"; \
			$(OSCAL_CLI_INSTALL_PATH)/bin/oscal-cli "$$eucs_type" validate "$$eucs_file"; \
		done \
	done

#
# Convert to OSCAL the ../src files using oscal-cli
#
.PHONY: convert-all 
convert-all: copy-xml-content convert-examples convert-eucs ## Convert all ../src files to JSON and YAML in ../release

.PHONY: copy-xml-content
copy-xml-examples:  
	@find $(SRC_DIR)/examples -mindepth 1 -maxdepth 1 -type d | while read example_dir; do \
		example_type=$$(basename "$$example_dir"); \
		echo "Converting $$example_type examples"; \
		find "$$example_dir" -name '*.*' | while read example_file; do \
			echo "Copy the $$example_type: $$example_file.xml to ../$(RELEASE_DIR)/xml/. "; \
			cp "$$example_file".xml ../$(RELEASE_DIR)/xml/.; \
		done \
	done

.PHONY: convert-examples
convert-examples: $(OSCAL_CLI_INSTALL_PATH) copy-xml-examples ## Convert and release src/examples using OSCAL CLI Tool
	@find $(SRC_DIR)/examples -mindepth 1 -maxdepth 1 -type d | while read example_dir; do \
		example_type=$$(basename "$$example_dir"); \
		echo "Converting $$example_type examples"; \
		find "$$example_dir" -name '*.xml' | while read example_file; do \
			echo "Converting to JSON the $$example_type: $$example_file.xml using OSCAL CLI "; \
			$(OSCAL_CLI_INSTALL_PATH)/bin/oscal-cli "$$example_type" convert "$$example_file".xml -to=JSON ../$(RELEASE_DIR)/json/"$$example_file".json; \
			echo "Converting to YAML the $$example_type: $$example_file.xml using OSCAL CLI "; \
			$(OSCAL_CLI_INSTALL_PATH)/bin/oscal-cli "$$example_type" convert "$$example_file".xml -to=JSON ../$(RELEASE_DIR)/yaml/"$$example_file".yaml; \
		done \
	done

.PHONY: convert-eucs
convert-eucs: $(OSCAL_CLI_INSTALL_PATH) copy-xml-eucs ## Convert and release src/eucs using OSCAL CLI Tool
	@find $(SRC_DIR)/eucs -mindepth 1 -maxdepth 1 -type d | while read eucs_dir; do \
		example_type=$$(basename "$$example_dir"); \
		echo "Converting $$example_type examples"; \
		find "$$example_dir" -name '*.xml' | while read example_file; do \
			echo "Converting to JSON the $$example_type: $$example_file.xml using OSCAL CLI "; \
			$(OSCAL_CLI_INSTALL_PATH)/bin/oscal-cli "$$example_type" convert "$$example_file".xml -to=JSON ../$(RELEASE_DIR)/json/"$$example_file".json; \
			echo "Converting to YAML the $$example_type: $$example_file.xml using OSCAL CLI "; \
			$(OSCAL_CLI_INSTALL_PATH)/bin/oscal-cli "$$example_type" convert "$$example_file".xml -to=JSON ../$(RELEASE_DIR)/yaml/"$$example_file".yaml; \
		done \
	done

#
#  Clean tool
#
.PHONY: clean-tool
clean-tool:  ## Remove oscal-cli tool 
	@echo Cleaning oscal-cli tool installation ...
	rm -frv $(OSCAL_CLI_INSTALL_PATH)

#
# Clean release
#
.PHONY: clean-xml-release
clean-xml-release: 
	@echo Cleaning XML content in ../release
	rm -f ../release/xml

.PHONY: clean-json-release
clean-json-release: 
	@echo Cleaning JSON content in ../release
	rm -f ../release/json

.PHONY: clean-yaml-release
clean-yaml-release: 
	@echo Cleaning YAML content in ../release
	rm -f ../release/yaml

.PHONY: clean-release
clean-release: clean-xml-release clean-json-release clean-yaml-release ## Clean all generated content in ../release

#
# Clean examples
#
PHONY: clean-xml-examples
clean-xml-examples: 
	@echo Cleaning XML content in ../examples
	rm -f ../examples/xml

.PHONY: clean-json-examples
clean-json-examples: 
	@echo Cleaning JSON content in ../release
	rm -f ../examples/json

.PHONY: clean-yaml-examples
clean-yaml-examples: 
	@echo Cleaning YAML content in ../release
	rm -f ../examples/yaml

.PHONY: clean-examples
clean-examples: clean-xml-examples clean-json-examples clean-yaml-examples ## Clean all generated content in ../examples

.PHONY: clean-all
clean-all: clean-examples clean-release clean-tool ## Clean tool and generated files.	
