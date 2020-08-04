# Introduction
The utopian goal of telemetry is to allow reproduction of the state of a system/network for analysis. Ideally the solution provides a complete snapshot of the state and configuration of every component of the system (every node of the network) for any given moment in history.

    $ get-net-db db.conf "2020-07-13T00:24:27Z" network.xml > 1.xml
    $ get-net-db db.conf "2020-07-13T00:24:51Z" network.xml > 2.xml

What just happened is we retrieved 2 snapshots of the operational datastores of the devices of interest as specified in the [network.xml](network.xml). The data included in the files [1.xml](1.xml) and [2.xml](2.xml) contains the complete operational datastores (configuration and state) of the devices according to the network.xml and includes the yang-library specification of each of the devices.

# What is YANG?

The content of [1.xml](1.xml) and [2.xml](2.xml) files is readable without any use of YANG but if one needs to find out the formal specification of the data nodes e.g. /ietf-interfaces:interfaces-state/interface[name="eth0"]/in-octets one needs a formal model [ietf-interface@2018-02-20.yang](https://github.com/YangModels/yang/blob/master/standard/ietf/RFC/ietf-interfaces%402018-02-20.yang). Simple tree diagram of the module [rfc8343#section-3](https://tools.ietf.org/html/rfc8343#section-3)

There are many standard models for different application - networking, power grid monitoring (not yet), etc. One can extend standard models using the augment statement.

Here is demo module adding "in-testframe-pkts" counter to the network interface statistics container: [demo-testframes.yang](demo-testframes.yang)

Compare and see how data implementing this proprietary model is added [2-w-testframes.xml](2-w-testframes.xml) (diff with 2.xml)

# Notifications

In addition to modeling the device as a datastore of configuration and state YANG models the notifications the device can issue. Independent of the methods used to collect the notifications they can be represented as NETCONF session data instance in XML or with other encoding.

# Timeseries
In reality one can not retrieve complete snapshot of the network state for every point in time if any. However the archived data corresponding to a certain data node in the model can be stored in a timeseries database. Being able to resolve each data column of the timeseries database to instance identifier  e.g. /networks/network[network-id="demo"]/node[node-id="foo"]/data/interfaces-state/interface[name="eth5"]/statistics/in-testframe-pkts [rfc7950#section-9.13](https://tools.ietf.org/html/rfc7950#section-9.13) with information resolving the model context (yang-library) one can avoid the jungle of obscure non-homogeneous data sources by referring to a common model.

