# Asperathos

<img src="https://pbe.lsd.ufcg.edu.br/wp-content/uploads/sites/5/2018/10/logoasperathos.png" alt="drawing" width="55%"/>



[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

Asperathos is part of Privacy by Evidence (PbE) which is an initiative to improve confidence in applications that handle sensitive data, you can read more about this initiative in: [PbE](https://pbe.lsd.ufcg.edu.br/) 



## Overview
Asperathos is a platform to facilitate the deployment and control of applications running in cloud environments. For example, it can provide Quality of Service (QoS) by controlling resources allocated during runtime. Nevertheless, in contrast to other orchestration tools, such as Kubernetes itself or OpenStack Heat, it can be configured to consider application specific metrics and to actuate in a customized fashion.

The architecture from Asperathos is depicted in the following figure. It is composed of three main modules: _(i)_ the **Manager** is the entry point for the user and is responsible for receiving an application submission, triggering all other steps; _(ii)_ the **Monitor** gather, transforme and publish metrics collected from applications (e.g., the application progress) or environment resources (e.g., CPU usage); _(iii)_ the **Controller** is the component that adjusts the amount of allocated resources dedicated to an application.

<div style="width:100px;height:100px;"><img src ="http://picresize.com/images/rsz_11architecture.png" class="center"/></div>

Each module above can be customized by plugins. For example, a plugin to deploy the application can be specified in the Manager, another to define the application specific metric can be specified in the Monitor, another to control the actuation logic could be configured in the Controller, and so on.

**Asperathos** was developed by the [**LSD-UFCG**](https://www.lsd.ufcg.edu.br/#/) *(Distributed Systems Laboratory at Federal University of Campina Grande)* as part of the toolset of the **EUBra-BIGSEA** ecosystem.

**EUBra-BIGSEA** is committed to making a significant contribution to the **cooperation between Europe and Brazil** in the *area of advanced cloud services for Big Data applications*. See more about in [EUBra-BIGSEA website](http://www.eubra-bigsea.eu/).


## How does it works?
The Asperathos modules are implemented following a **plugin architecture**, providing flexibility to customize your deployment using only the plugins you need, avoiding to include unnecessary dependencies (from others plugins) to your deploy environment. All the integrations with different infrastructures and components are made by specific plugins. 

This independent architecture allot the combination of different approaches in the same context. For example: running a containerized distributed application using an specific Manager plugin, it is possible to write a Monitor plugin to gather metrics about application progress and another to gather metrics from resources usage at the same time; it is also possible to Write two different Controller plugins where one can watch the resources usage measurements (published by one of the monitor plugins) from VMs to add/kill instances in the cluster, and another plugin to watch the application progress and add more container replicas to speed up the processing of tasks.

It is also possible to easily add external or new components, like: authenticators, authorizers, validators, and so on.

# Case studies


<img src="https://www.lncc.br/images/logo_lncc.png" alt="drawing" width="45%"/>
<img src="https://www.lsd.ufcg.edu.br/wp-content/uploads/2019/01/securecloud-logo.png" alt="drawing" width="45%"/>
<img src="https://www.atmosphere-eubrazil.eu/sites/all/themes/theme1/logo.png" alt="drawing" width="45%"/>
<img src="https://www.eubrasilcloudforum.eu/sites/default/files/EUBra-BIGSEA_logo_piccolo.jpg" alt="drawing" width="45%"/>


Asperathos is being used and expanded in our partners: LNCC, SecureCloud and Atmosphere.



## Requirements
* Python 2.7
* Linux packages: python-dev and python-pip
* Python packages: setuptools, tox and flake8

