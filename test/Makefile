
BINS = bin/random bin/multihash bin/ipfs bin/pollEndpoint bin/iptb bin/go-sleep
IPFS_ROOT = ../
IPFS_CMD = ../cmd/ipfs
RANDOM_SRC = ../Godeps/_workspace/src/github.com/jbenet/go-random
RANDOM_FILES_SRC = ../Godeps/_workspace/src/github.com/jbenet/go-random-files
POLLENDPOINT_SRC= ../thirdparty/pollEndpoint
GOSLEEP_SRC = ./dependencies/go-sleep

GX_RELATIVE_PATH = gx/ipfs

export PATH := ../bin:${PATH}

# User might want to override those on the command line
GOFLAGS =

all: deps

global-deps:
	make -C .. deps

deps: global-deps bins

clean:
	rm $(BINS)

bins: $(BINS)

find_go_files = $(shell find $(1) -name "*.go")

# Non gx dependencies

bin/random: $(call find_go_files, $(RANDOM_SRC)) IPFS-BUILD-OPTIONS
	@echo "*** installing $@ ***"
	go build $(GOFLAGS) -o bin/random $(RANDOM_SRC)/random

bin/random-files:
	@echo "*** installing $@ ***"
	go build $(GOFLAGS) -o bin/random-files $(RANDOM_FILES_SRC)/random-files

bin/ipfs: $(call find_go_files, $(IPFS_ROOT)) IPFS-BUILD-OPTIONS
	@echo "*** installing $@ ***"
	go build $(GOFLAGS) -o bin/ipfs $(IPFS_CMD)

bin/pollEndpoint: $(call find_go_files, $(POLLENDPOINT_SRC)) IPFS-BUILD-OPTIONS
	@echo "*** installing $@ ***"
	go build $(GOFLAGS) -o bin/pollEndpoint $(POLLENDPOINT_SRC)

bin/go-sleep: $(call find_go_files, $(GOSLEEP_SRC)) IPFS-BUILD-OPTIONS
	@echo "*** installing $@ ***"
	go build $(GOFLAGS) -o bin/go-sleep $(GOSLEEP_SRC)

# gx dependencies

multihash_src:
	$(eval MULTIHASH_HASH := $(shell cd .. && gx deps find go-multihash))
	$(eval MULTIHASH_SRC := $(GX_RELATIVE_PATH)/$(MULTIHASH_HASH)/go-multihash)

bin/multihash: multihash_src $(call find_go_files, $(MULTIHASH_SRC)) IPFS-BUILD-OPTIONS
	@echo "*** installing $@ ***"
	go build $(GOFLAGS) -o bin/multihash $(MULTIHASH_SRC)/multihash

iptb_src:
	$(eval IPTB_HASH := $(shell cd .. && gx deps find iptb))
	$(eval IPTB_SRC := $(GX_RELATIVE_PATH)/$(IPTB_HASH)/iptb)

bin/iptb: iptb_src $(call find_go_files, $(IPTB_SRC)) IPFS-BUILD-OPTIONS
	@echo "*** installing $@ ***"
	go build $(GOFLAGS) -o bin/iptb $(IPTB_SRC)

# Tests

test: test_expensive

test_expensive: verify_gofmt
	cd sharness && make TEST_EXPENSIVE=1
	cd 3nodetest && make
	cd dependencies && make

test_cheap: verify_gofmt
	cd sharness && make
	cd 3nodetest && make

test_race: verify_gofmt
	cd sharness && make GOFLAGS=-race TEST_EXPENSIVE=1
	cd 3nodetest && make GOFLAGS=-race
	cd dependencies && make GOFLAGS=-race

# Misc

coverage: coverage_sharness

coverage_sharness:
	./sharness_test_coverage_helper.sh

IPFS-BUILD-OPTIONS: FORCE
	@bin/checkflags '$@' '$(GOFLAGS)' '*** new Go flags ***'

verify_gofmt:
	bin/verify-go-fmt.sh

.PHONY: all clean FORCE
