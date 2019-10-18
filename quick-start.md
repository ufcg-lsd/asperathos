# Asperathos's Quick Start

The purpose of this guide is to demonstrate how to setup all Asperathos components.
### How to create a Kubernetes cluster.

1. The following [guide](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/) 
   retrieve from official Kubernetes documentation can be used to 
   support the deployment of the cluster. The config file generate by this tutorial will be used
   in the following steps.

### How to create a docker image to consume a KubeJobs workload?

#### I. Creating a Python application that knows how to consume the redis workload:

1. The application must know how to use the redis API.
   As an example, take the following simple worker. It consume an item from redis, which is a URL. The worker then downloads the content of the URL and prints to stdout.
   In Python, the application would look like this:
```
import redis
import requests
import os

# Asperathos will give the redis host as enviroment variable named REDIS_HOST.
# The defalt port of redis is 6379.
r = redis.StrictRedis(host=os.environ['REDIS_HOST'],
                      port=6379,
                      db=0)

# r.llen("job") returns the length of the queue "job" in the redis.
while r.llen("job") > 0:

    # `rpoplpush` moves one item from our work queue
    # to an auxiliary queue, for items being processed,
    # returning its value
    item_url = r.rpoplpush('job', 'job:processing')

    # as it is a link, download the content
    content = requests.get(item_url).text

    # print to stdout
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

#### I. Install Docker and Docker Compose
```bash
curl https://get.docker.com/ | bash
apt-get install docker-compose
```
#### II. Clone Asperathos Compose repository
```bash
git clone https://github.com/ufcg-lsd/asperathos-compose
cd asperathos-compose
```
#### III. Set your KUBECONFIG env var
```bash
export KUBECONFIG=/path/to/your/kube/config
```
#### IV. Build the images
```bash
./build.sh
```
#### V. Run compose
```bash
docker-compose up
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

* The **workload-url** is a URL to a text file containing all the work items, one per line. Asperathos pushes these values to a work queue which the workers will later consume. For our simple worker that just downloads the contents of a URL and prints them to stdout, consider the `workload-url` example: [https://gist.githubusercontent.com/clenimar/9ecb14e2346af72763303a9957c94ea3/raw/1c014690f10c79bb9fe2165360ee92c106485ee7/workload-url.txt](https://gist.githubusercontent.com/clenimar/9ecb14e2346af72763303a9957c94ea3/raw/1c014690f10c79bb9fe2165360ee92c106485ee7/workload-url.txt). It contains URLs to text files containing phrases that are going to be printed.

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
