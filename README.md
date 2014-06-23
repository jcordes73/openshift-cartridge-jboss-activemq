# openshift-cartridge-jboss-activemq

## Description

An OpenShift cartridge for provisioning ActiveMQ RAR on the JBoss EAP 6 cartridge.

## Adding ActiveMQ RAR

First you have to download JBoss A-MQ from https://access.redhat.com/jbossnetwork/restricted/listSoftware.html?downloadType=distributions&product=jboss.amq&productChanged=yes and unzip it.

Then you have to extract the ActiveMQ RAR like this:

```bash
unzip extras/apache-activemq-${activemq.version}-bin.zip apache-activemq-${activemq.version}/lib/optional/activemq-rar-${activemq.version}.rar
```

and copy the ActiveMQ RAR into the project

```bash
cp activemq-rar-${activemq.version}.rar src/main/resources/cartridge/configuration
```

## Build

For building the project you need Maven installed. I have used Maven 3.0.5.

Just execute (after setting mvn on the PATH):

```bash
mvn clean install
```

Below you can find information on what properties are used and can be overriden by passing system properties (i.e. using -D{name})

|Property|Default|
|--------|-------|
|activemq.version|5.9.0.redhat-610379|
|activemq.host|localhost|
|activemq.port|61616|
|activemq.username|admin|
|activemq.password|admin|

## Install

After the build you need to copy the resulting RPM to the target OpenShift node (for convenience I have used the /tmp directory):

```bash
scp target/rpm/openshift-cartridge-jboss-activemq/RPMS/noarch/openshift-cartridge-jboss-activemq-${project.version}-1.noarch.rpm root@${openshift.node.ip}:/tmp
```

then install it using

```bash
rpm -ivh openshift-cartridge-jboss-activemq-${project.version}.rpm
```

After the RPM has been installed successfully you need to install the cartridge like this:

```bash
oo-admin-cartridge -a install -s /usr/libexec/openshift/cartridges/jboss-activemq
```

The last installation step is to clear the caches on the OpenShift Broker:

```bash
oo-admin-broker-cache --clear --console
```

## Remove

To remove the cartridge you first need to remove the package installed via RPM:

```bash
rpm -e openshift-cartridge-jboss-activemq-${project.version}-1.noarch
```

The next command will list all available OpenShift cartridges along with it's versions and cartridge-versions:

```bash
oo-admin-cartridge -l
```

Finally you delete the cartridge using this command

```bash
oo-admin-cartridge -a erase --name jboss-activemq --version ${activemq.version} --cartridge_version ${project.version}
```
