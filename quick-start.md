# Asperathos's Quick Start

The purpose of this guide is to demonstrate how to setup all Asperathos components.
### How to create a Kubernetes cluster.

1. The following [guide](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/) 
   retrieve from official Kubernetes documentation can be used to 
   support the deployment of the cluster. The config file generate by this tutorial will be used
   in the following steps.

### How to create a docker image to consume a kubejobs workload?

#### I. Creating a Python application that knows how to consume the redis workload:

1. The application must know how to use the redis API.
   In Python, the application looks like this:
   (In this example this job will just print the content of the workload in the console)
```
import redis
import requests
import os

# Asperathos will give the redis host as enviroment variable named REDIS_HOST.
# The defalt port of redis is 6379.
r = redis.StrictRedis(host=os.environ['REDIS_HOST'],
                      port=6379,
                      db=0)

# r.len("job") returns the length of the queue "job" in the redis.
while r.llen("job") > 0:

    # r.rpoplpush('job', 'job:processing')
    # do dequeue in 'job' and enqueue in 'job:processing'
    # and return the content that was moved.
    link = r.rpoplpush('job', 'job:processing')

    content = requests.get(link).text

    print(content)

```

#### II. Creating a requirements file.

1. It is required to make a requirements file with all necessary imports from the application.
   In this example the application need the redis and requests modules.
   The file must be like this:

```
redis
requests
```
#### III. Creating a Dockerfile:

1. In the same path that the application is contained create a file named Dockerfile 
   with the following content:

```
FROM python:2.7
COPY requirements.txt .
COPY application.py  .
RUN pip install -r requirements.txt
```

#### IV. Building, tagging and pushing the image.
The image name will be 'quickstart' and the tag will be 'demo'.

1. To build the image run the following command:
   
```
docker build -t quickstart:demo .
```

2. To create a tag run the following command:
   The repository variable represent a public repository 
   as dockerhub or a private repository.
   (If private, it's also required to specify the port)

```
docker tag quickstart:demo <repository>/quickstart:demo
```

3. To push to the repository run the following command:

```
docker push <repository>/quickstart:demo
```

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
k8s_conf_path = /home/user/.kube/config # Optional value with ./data/conf as default
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

[monasca]
monasca_endpoint = https://<monasca_url>:<monaca_port>/<version>
username = user
password = password
project_name = demo
auth_url = https://<monasca_url>:<monaca_port>/<version>/
api_version = version

[kubejobs]
k8s_manifest = /home/user/.kube/config # Optional value with ./data/conf as default
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
k8s_manifest = /home/user/.kube/config # Optional value with ./data/conf as default
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
k8s_conf_path = /home/user/.kube/config # Optional value with ./data/conf as default
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
url = https://influxdb-url # Optional value, gets the Ip of any node in the cluster if not specified
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

* The **cmd** needs to contain the command that will launch the file that will consumes the workload, in this tutorial will be '"python", "application.py"'.

* The **img-url:port** variable will be the image generated in the first tutorial of this guide.


* The **workload-url** is a url containing a text file with a list a urls, each of those containing text charatecters. These are used to fill the work queue provided by Asperathos, the worker will consume this queue as described in the first tutorial For this tutorial you can use this workload : [https://raw.githubusercontent.com/joseims/workload-example/master/workload_url.txt.](https://raw.githubusercontent.com/joseims/workload-example/master/workload_url.txt) This file contains three links, each one is a string with one of the following phrases.
```
Asperathos is amazing!

Kubejobs is the best plugin

The workload-url is a queue of urls
```

* The **datasource-type** will be 'influxdb' in this example.

* The **env_vars** variable needs to contain any environment variables that the file contained in the image generated in the first tutorial of this guide will use.

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
