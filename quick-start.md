# Asperathos's Quick Start

The purpose of this guide is to demonstrate how to setup all Asperathos components.

### How to deploy an Asperathos instance?

#### I. Configuring Manager component

1. Using a exclusive terminal, clone Asperathos-Manager repository:

```bash
$ git clone https://github.com/ufcg-lsd/asperathos-manager 
$ cd asperathos-manager/
```

2. Run the ‘pre-install.sh’ script that will install the requirements:

```bash
$ ./pre-install.sh
```

#### II. Configuring Monitor component

1. Using a exclusive terminal, clone Asperathos-Monitor repository:

```bash
$ git clone https://github.com/ufcg-lsd/asperathos-monitor 
$ cd asperathos-monitor/
```

2. Run the ‘pre-install.sh’ script that will install the requirements:

```bash
$ ./pre-install.sh
```

#### III. Configuring Controller component

1. Using a exclusive terminal, clone Asperathos-Controller repository:

```bash
$ git clone https://github.com/ufcg-lsd/asperathos-controller 
$ cd asperathos-controller/
```

2. Run the ‘pre-install.sh’ script that will install the requirements:

```bash
$ ./pre-install.sh
```

#### IV. Configuring Visualizer component

1. Using a exclusive terminal, clone Asperathos-Visualizer repository:

```bash
$ git clone https://github.com/ufcg-lsd/asperathos-visualizer 
$ cd asperathos-visualizer/
```

2. Run the ‘pre-install.sh’ script that will install the requirements:

```bash
$ ./pre-install.sh
```

### How to configure Asperathos components to execute the KubeJobs plugin?

#### I. Configure the Manager component to run KubeJobs plugin

1. Access the Asperathos-Manager folder:

```bash
$ cd path/to/asperathos-manager
```

2. Modify the file ‘broker.cfg’ with the required informations to run the KubeJobs plugin. You can check below an template of how the ‘broker.cfg’ file should look like:

```
[general]
host = 0.0.0.0
port = 1500
plugins = kubejobs

[services]
controller_url = http://0.0.0.0:5000
monitor_url = http://0.0.0.0:5001
visualizer_url = http://0.0.0.0:5002
optimizer_url = http://0.0.0.0:5003
authorization_url = http://0.0.0.0:5004

[kubejobs]
k8s_conf_path = /home/user/.kube/config # Optional value with ./data/conf
redis_ip = 10.0.0.1 # Optional value, gets the Ip of any node in the cluster if not specified
```

Description of the required variables:

* **k8s_conf_path**: The path for the configuration file of the Kubernetes cluster that will be used.
* **redis_ip**: ip of the one of the nodes contained in the Kubernetes clusters that will be used.

3. Run the Asperathos-Manager service:

```bash
path/to/asperathos-manager$ ./run.sh
```

#### II. Configure the Monitor component to run KubeJobs plugin

1. Access the Asperathos-Monitor folder:

```bash
$ cd path/to/asperathos-monitor
```

2. Modify the file ‘monitor.cfg’ with the required informations to run the KubeJobs plugin. You can check below an template of how the ‘monitor.cfg’ file should look like:

```
[general]
host = 0.0.0.0
port = 5001
plugins = kubejobs
debug = True
retries = 5

[kubejobs]
k8s_manifest = /home/user/.kube/config # Optional value with ./data/conf
```

3. Run the Asperathos-Monitor service:

```bash
path/to/asperathos-monitor$ ./run.sh
```

#### III. Configure the Controller component to run KubeJobs plugin

1. Access the Asperathos-Controller folder:

```bash
$ cd path/to/asperathos-controller
```

2. Modify the file ‘controller.cfg’ with the required informations to run the KubeJobs plugin. The KubeJobs plugin requires the k8s-replicas actuator to be activated as well as a specific metric_source_plugin. In this example we will use ‘monasca’ as metric_source_plugin. You can check below an template of how the ‘controller.cfg’ file should look like:

```
[general]
host = 0.0.0.0
port = 5000
actuator_plugins = k8s_replicas
metric_source_plugins = monasca

[monasca]
monasca_endpoint = https://monasca-url:port/v2.0
username = user
password = psswrd
project_name = admin
auth_url = https://monasca-url:port/v3/
api_version = 2_0

[k8s_replicas]
k8s_manifest = /home/user/.kube/config # Optional value with ./data/conf
```

Description of the required variable:

* **k8s_manifest**: The path for the configuration file of the Kubernetes cluster that will be used.

3. Run the Asperathos-Controller service:

```bash
path/to/asperathos-controller$ ./run.sh
```

#### IV. Configure the Visualizer component to run KubeJobs plugin

1. Access the Asperathos-Visualizer folder:

```bash
$ cd path/to/asperathos-visualizer
```

2. Modify the file ‘visualizer.cfg’ with the required informations to run the K8s-Grafana plugin. You can check below an template of how the ‘visualizer.cfg’ file should look like:

```
[general]
host = 0.0.0.0
port = 5002
plugins = k8s-grafana
datasources = monasca,influxdb
debug = true
retries = 5

[k8s-grafana]
k8s_conf_path = /home/user/.kube/config # Optional value with ./data/conf
visualizer_type = k8s-grafana
visualizer_ip = 10.11.5.62 # Optional value, gets the Ip of any node in the cluster if not specified

[monasca]
name = monasca
type = monasca-datasource
url = https://monasca-url:port
access = proxy
basic_auth = no
auth_type = token
token = 

[influxdb]
name = InfluxDB
type = influxdb
url = https://influxdb-url
access = proxy
```

Description of the required variables:

* **k8s_conf_path**: The path for the configuration file of the Kubernetes cluster that will be used. 
* **visualizer_type**: The visualizer type that will be used
* **visualizer_ip**: The IP where the visualizer will be accessible from. Needs to be the IP of one of the nodes contained in the Kubernetes cluster.

If you want to use Monasca as Datasource, the following variables are required:

* **name**: Name of the Datasource (usually gets the value of ‘monasca’)
* **type**: Type of the Datasource (usually gets the value of ‘monasca-datasource’)
* **url**: Address where Monasca service are answering from.
* **access**: Access type (usually gets the value of ‘proxy’).
* **basic_auth**: Enables the basic authentication (usually gets the value of ‘no’)
* **auth_type**: The authentication type (can get the value of ‘token’)
* **token**: The token of access to the service. Needs to be provided if the ‘auth_type’ variable get the value of ‘token’.

If you want to use InfluxDB as Datasource, the following variables are required:

* **name**: Name of the Datasource (usually gets the value of ‘InfluxDB’)
* **type**: Type of the Datasource (usually gets the value of ‘influxdb’)
* **url**: Address where InfluxDB service will be answering from (usually gets the value of the IP of one of the nodes contained in the Kubernetes cluster).
* **access**: Access type (usually gets the value of ‘proxy’).

3. Run the Asperathos-Visualizer service :

```bash
path/to/asperathos-visualizer$ ./run.sh
```

### How to submit a KubeJobs job?

1. First, create a file named ‘kubejobs-job.json’. The content of this file will have all necessary commands to activate each plugin of each Asperathos component. Also, this JSON file also contains information about the user credentials, workload that will be running, image that will be used and so on. You can check below an template of how the ‘kubejobs-job.json’ file should look like:

```json
{  
   "plugin":"kubejobs",
   "plugin_info":{  
      "username": "user",
      "password": "psswrd",
      "cmd":[  
         ...
      ],
      "img": "img-url:port",
      "init_size": 1,      
      "redis_workload": "workload-url",
      "config_id": "id",
      "control_plugin": "kubejobs",
      "control_parameters": {  
         "max_size": 10,
         "actuator": "k8s_replicas",
         "check_interval": 5,
         "trigger_down": 0,
         "trigger_up": 0,
         "min_rep": 1,
         "max_rep": 10,
         "actuation_size": 1,
         "metric_source": "redis"
      },
      "monitor_plugin": "kubejobs",
      "monitor_info":{  
         "expected_time": 40
      },
      "enable_visualizer":true,
      "visualizer_plugin":"k8s-grafana",
      "visualizer_info":{  
         "datasource_type":"datasource-type"
      },
      "env_vars":{  
         [...]
      }
   }
}
```

2.  Then, in a different terminal, run the following command to submit the job to the Manager component.

```bash
$ cd path/to/kubejobs.json
$ curl -H "Content-Type: application/json" --data @kubejobs-job.json http://0.0.0.0:1500/submissions
```

### How collect the visualization URL of a specific job?

1. After a job be launched with the Visualization flag setted to ‘enable’, it is possible to retrieve the visualization URL to keep track of the job progress at execution time. The following GET request to the Visualizer api can be used to retrieve this information. 

```bash
$ curl visualizer-ip:port/visualizing/id
```

Where:

* **visualizer-ip**: The IP where the visualizer service are running.
* **port**: The port where the visualizer service are answering from.
* **id**: The ID of the job launched.
