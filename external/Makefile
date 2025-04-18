SHELL := bash
ROOT := $(shell git rev-parse --show-toplevel)

COMMON := yaml-common
EATME := eatme
REF_PARSER := yaml-reference-parser
NPM_YAML := npm-yaml

COMMON_REPO := https://github.com/yaml/yaml-common
EATME_REPO ?= https://github.com/eatme-dev/eatme
REF_PARSER_REPO ?= https://github.com/yaml/yaml-reference-parser
NPM_YAML_REPO := https://github.com/eemeli/yaml

ALL := \
    $(COMMON) \
    $(EATME) \
    $(REF_PARSER) \
    $(NPM_YAML) \

TMPEXT := /tmp/yaml-play-ext

.DELETE_ON_ERROR:
default:

build: $(ALL)

clean:
	rm -fr $(ALL)

$(COMMON):
ifeq (,$(COMMON_REPO:/%=))
	ln -s $(COMMON_REPO) $@
else ifneq (,$(wildcard $(ROOT)/../$(COMMON)))
	ln -s $(shell echo $$(cd $(ROOT)/../$(COMMON) && pwd)) $@
else ifneq (,$(wildcard $(TMPEXT)/$(COMMON)))
	ln -s $(TMPEXT)/$(COMMON) $@
else
	git clone $(COMMON_REPO) $@
	mkdir -p $(TMPEXT)
	mv $@ $(TMPEXT)/
	ln -s $(TMPEXT)/$@ $@
endif

$(EATME):
ifeq (,$(EATME_REPO:/%=))
	ln -s $(EATME_REPO) $@
else ifneq (,$(wildcard $(ROOT)/../$(EATME)))
	ln -s $(shell echo $$(cd $(ROOT)/../$(EATME) && pwd)) $@
else ifneq (,$(wildcard $(TMPEXT)/$(EATME)))
	ln -s $(TMPEXT)/$(EATME) $@
else
	git clone $(EATME_REPO) --branch=devel $@
	mkdir -p $(TMPEXT)
	mv $@ $(TMPEXT)/
	ln -s $(TMPEXT)/$@ $@
endif

$(REF_PARSER):
ifeq (,$(REF_PARSER_REPO:/%=))
	ln -s $(REF_PARSER_REPO) $@
else ifneq (,$(wildcard $(ROOT)/../$(REF_PARSER)))
	ln -s $(shell echo $$(cd $(ROOT)/../$(REF_PARSER) && pwd)) $@
else ifneq (,$(wildcard $(TMPEXT)/$(REF_PARSER)))
	ln -s $(TMPEXT)/$(REF_PARSER) $@
else
	git clone $(REF_PARSER_REPO) $@
	mkdir -p $(TMPEXT)
	mv $@ $(TMPEXT)/
	ln -s $(TMPEXT)/$@ $@
endif

$(NPM_YAML):
ifdef FAST_TEST
	mkdir -p $(TMPEXT)/$(NPM_YAML)/browser/dist
	mkdir -p $(TMPEXT)/$(NPM_YAML)/v1/browser/dist
	mkdir -p $(TMPEXT)/$(NPM_YAML)/v2/browser/dist
else ifneq (,$(wildcard $(TMPEXT)/$(NPM_YAML)))
	# This line intentionally left blank
else ifneq ($(shell command -v npm),)
	git clone -q $(NPM_YAML_REPO) $@
	patch $@/config/rollup.browser-config.js < npm-yaml-rollup-master.patch
	(cd $@ && npm install . && npm run build)
	[[ -d $@/browser/dist ]]
	#
	git -C $(NPM_YAML) worktree add -f v2 v2.0.0-6
	patch $@/v2/config/rollup.browser-config.js < npm-yaml-rollup-v2.patch
	(cd $@/v2 && npm install . && npm run build)
	[[ -d $@/v2/browser/dist ]]
	#
	git -C $(NPM_YAML) worktree add -f v1 v1.10.2
	patch $@/v1/rollup.browser-config.js < npm-yaml-rollup-v1.patch
	(cd $@/v1 && npm install . && npm run build)
	[[ -d $@/v1/browser/dist ]]
	#
	mkdir -p $(TMPEXT)
	mv $@ $(TMPEXT)/
else
	@echo "*** NOTICE: 'npm' not available."
	@echo "*** Can't build NodeJS variants for 'yaml' package."
	exit 1
endif
	ln -s $(TMPEXT)/$@ $@
