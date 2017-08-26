.PHONY: bootstrap requirements install test clean clean-pyc

ROOTDIR ?= .
VIRTUALENVPATH = venv/bin
MYPATH = $(ROOTDIR):$(PYTHONPATH)
PIP ?= $(VIRTUALENVPATH)/pip
PIPCOMPILE ?= $(VIRTUALENVPATH)/pip-compile
PIPSYNC ?= $(VIRTUALENVPATH)/pip-sync
PYTEST ?= pytest
PYTEST_OPTIONS ?= -v
MODULES = apps
TOX ?= tox

bootstrap:
	python3 -m venv venv
	source venv/bin/activate
	pip install pip -U
	pip install pip-tools
	$(PIPCOMPILE) requirements.in
	make install

requirements:
	pip install pip-tools
	$(PIPCOMPILE) requirements.in

upgragerequirements:
	$(PIPCOMPILE) --upgrade

install:
	PYTHONPATH=$(MYPATH) $(PIPSYNC)

pytest:
	PYTHONPATH=$(MYPATH) $(PYTEST) $(MODULES) $(PYTEST_OPTIONS) --cov=$(MODULES)
	coverage xml

test: lint pytest

failed:
	PYTHONPATH=$(MYPATH) $(PYTEST) $(MODULES) $(PYTEST_OPTIONS) --failed

tox:
	PYTHONPATH=$(MYPATH) tox

clean: clean-build clean-pyc

clean-build:
	rm -fr build/
	rm -fr dist/
	rm -fr *.egg-info
	rm -fr *.egg
	rm -rf __pycache__

clean-pyc:
	find . -name '*.pyc' -exec rm -f {} +
	find . -name '*.pyo' -exec rm -f {} +
	find . -name '*~' -exec rm -f {} +
	find . -name '__pycache__' -exec rm -fd {} +

lint:
	flake8 $(MODULES) --max-complexity 24 --statistics  --config=./setup.cfg

runprod:
	bin/run-prod.sh

rundev:
	bin/run-dev.sh

branch-cleaner:
	git branch --merged | grep -v "\*" | grep -v master | grep -v dev | xargs -n 1 git branch -d

hooks:
	cp pre-push .git/hooks/pre-push
	chmod a+x .git/hooks/pre-push
