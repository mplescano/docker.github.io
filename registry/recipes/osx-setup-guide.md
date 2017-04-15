---
description: Explains how to run a registry on macOS
keywords: registry, on-prem, images, tags, repository, distribution, macOS, recipe, advanced
title: macOS Setup Guide
---

## Use-case

This is useful if you intend to run a registry server natively on macOS.

### Alternatives

You can start a VM on macOS, and deploy your registry normally as a container using Docker inside that VM.

The simplest road to get there is traditionally to use the [docker Toolbox](https://www.docker.com/toolbox), or [docker-machine](/machine/index.md), which usually relies on the [boot2docker](http://boot2docker.io/) iso inside a VirtualBox VM.

### Solution

Using the method described here, you install and compile your own from the git repository and run it as an macOS agent.

### Gotchas

Production services operation on macOS is out of scope of this document. Be sure you understand well these aspects before considering going to production with this.

## Setup golang on your machine

If you know, safely skip to the next section.

If you don't, the TLDR is:

    bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
    source ~/.gvm/scripts/gvm
    gvm install go1.4.2
    gvm use go1.4.2

If you want to understand, you should read [How to Write Go Code](https://golang.org/doc/code.html).

## Checkout the Docker Distribution source tree
    mkdir -p $GOPATH/src/github.com/Azure/azure-sdk-for-go &&
    git clone https://github.com/Azure/azure-sdk-for-go.git $GOPATH/src/github.com/Azure/azure-sdk-for-go &&

    mkdir -p $GOPATH/src/github.com/Sirupsen/logrus &&
    git clone https://github.com/sirupsen/logrus.git $GOPATH/src/github.com/Sirupsen/logrus &&

    mkdir -p $GOPATH/src/github.com/aws/aws-sdk-go &&
    git clone https://github.com/aws/aws-sdk-go.git $GOPATH/src/github.com/aws/aws-sdk-go &&

    mkdir -p $GOPATH/src/github.com/docker/goamz &&
    git clone https://github.com/docker/goamz.git $GOPATH/src/github.com/docker/goamz &&

    mkdir -p $GOPATH/src/github.com/docker/libtrust &&
    git clone https://github.com/docker/libtrust.git $GOPATH/src/github.com/docker/libtrust &&

    mkdir -p $GOPATH/src/github.com/garyburd/redigo &&
    git clone https://github.com/garyburd/redigo.git $GOPATH/src/github.com/garyburd/redigo &&

    mkdir -p $GOPATH/src/github.com/mitchellh/mapstructure &&
    git clone https://github.com/mitchellh/mapstructure.git $GOPATH/src/github.com/mitchellh/mapstructure &&

    mkdir -p $GOPATH/src/github.com/ncw/swift &&
    git clone https://github.com/ncw/swift.git $GOPATH/src/github.com/ncw/swift &&

    mkdir -p $GOPATH/src/github.com/spf13/cobra &&
    git clone https://github.com/spf13/cobra.git $GOPATH/src/github.com/spf13/cobra &&

    mkdir -p $GOPATH/src/github.com/stevvooe/resumable &&
    git clone https://github.com/stevvooe/resumable.git $GOPATH/src/github.com/stevvooe/resumable &&

    mkdir -p $GOPATH/src/github.com/yvasiyarov/gorelic &&
    git clone https://github.com/yvasiyarov/gorelic.git $GOPATH/src/github.com/yvasiyarov/gorelic &&

    mkdir -p $GOPATH/src/github.com/golang/crypto &&
    git clone https://github.com/golang/crypto.git $GOPATH/src/github.com/golang/crypto
    
    mkdir -p $GOPATH/src/github.com/docker
    git clone https://github.com/docker/distribution.git $GOPATH/src/github.com/docker/distribution
    cd $GOPATH/src/github.com/docker/distribution

## Build the binary

    GOPATH=$(PWD)/Godeps/_workspace:$GOPATH make binaries
    sudo cp bin/registry /usr/local/libexec/registry

## Setup

Copy the registry configuration file in place:

    mkdir /Users/Shared/Registry
    cp docs/osx/config.yml /Users/Shared/Registry/config.yml

## Running the Docker Registry under launchd

Copy the Docker registry plist into place:

    plutil -lint docs/osx/com.docker.registry.plist
    cp docs/osx/com.docker.registry.plist ~/Library/LaunchAgents/
    chmod 644 ~/Library/LaunchAgents/com.docker.registry.plist

Start the Docker registry:

    launchctl load ~/Library/LaunchAgents/com.docker.registry.plist

### Restarting the docker registry service

    launchctl stop com.docker.registry
    launchctl start com.docker.registry

### Unloading the docker registry service

    launchctl unload ~/Library/LaunchAgents/com.docker.registry.plist
