# dEF-Pi: Distributed Energy Flexibility Platform & Interface

## Introduction

The Distributed Energy Flexibility Platform & Interface \(dEF-Pi\) aims to create an interoperable platform that is able to connect to a variety of appliances and support a host of Demand Side Management \(DSM\) approaches. This way the energy management system \(EMS\) hardware does not need to be changed when a consumers switches from one service to another. At the same time the Energy Flexibility Platform & Interface makes it easier for service providers to introduce new services, since they do not have to provide the EMS hardware to their consumers to go with it.

## Goal of dEF-Pi

The goal of dEF-Pi is to provide a runtime environment that makes it possible to quickly design and implement services dealing with energy management. An important factor is the ability to support more than one node that is hosting instances of services. dEF-Pi abstracts the difficulties that arise when deploying services on separated nodes. It is designed with scalability in mind, meaning that the platform itself is lightweight enough to handle large amounts of nodes and subsequently a large amount of service instances. Because the changes of failures increase exponentially when the amount of nodes or services increase, the robustness of the platform is very important. Making sure that nodes that become unresponsive are handled correctly, preventing that errors are contained and do not cause errors elsewhere in the platform. Because of the distributed nature of dEF-Pi, security is also an important fact in the platform.

Build tools assist developers in creating services for the platform. By describe interfaces in a declarative way, the build tools are able to generate the skeleton of a service. This way the developer can focus solely on the implementation of the logical part of the service.

## About the FAN

The Flexiblepower Alliance Network \(FAN\) is a network of companies and institutions that jointly develop and manage the international FAN standard. FAN is a new open standard that connects \(household\) devices with energy management services. FAN gives users control over the energy consumption in their house or building.

FAN can be built into devices and energy management services of diverse manufacturers. Thanks to FAN, they can work together as a team – to decide whether to use, store or sell locally produced energy, for instance, or to determine which device should be switched on first. Controlling the energy services on  a smart phone or tablet, users can set the energy preferences, ensuring that devices are turned on, off or down whenever they want. FAN makes it easy to use renewable energy and will mean significant savings on your energy bill.  
[www.flexiblepower.org](http://www.flexiblepower.org)

## GitBook

There is a gitbook version of this documentation available [here](https://fan-ci.sensorlab.tno.nl/builds/defpi-documentation/master/html/).

