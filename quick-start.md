# Asperathos's Quick Start

The purpose of this guide is to demonstrate how to setup all Asperathos components.

### How to deploy an Asperathos instance?

#### I. Deploying Manager component

1. Using a exclusive terminal, clone Asperathos-Manager repository:

```bash
$ git clone https://github.com/ufcg-lsd/asperathos-manager 
$ cd asperathos-manager/
```

2. Run the ‘pre-install.sh’ script that will install the requirements:

```bash
$ ./pre-install.sh
```

3. Run the Asperathos-Manager service:

```bash
$ ./run.sh
```

#### II. Deploying Monitor component

1. Using a exclusive terminal, clone Asperathos-Monitor repository:

```bash
$ git clone https://github.com/ufcg-lsd/asperathos-monitor 
$ cd asperathos-monitor/
```

2. Run the ‘pre-install.sh’ script that will install the requirements:

```bash
$ ./pre-install.sh
```

3. Run the Asperathos-Monitor service :

```bash
$ ./run.sh
```

#### III. Deploying Controller component

1. Using a exclusive terminal, clone Asperathos-Controller repository:

```bash
$ git clone https://github.com/ufcg-lsd/asperathos-controller 
$ cd asperathos-controller/
```

2. Run the ‘pre-install.sh’ script that will install the requirements:

```bash
$ ./pre-install.sh
```

3. Run the Asperathos-Controller service:

```bash
$ ./run.sh
```

#### IV. Deploying Visualizer component (Optional)

1. Using a exclusive terminal, clone Asperathos-Visualizer repository:

```bash
$ git clone https://github.com/ufcg-lsd/asperathos-visualizer 
$ cd asperathos-visualizer/
```

2. Run the ‘pre-install.sh’ script that will install the requirements:

```bash
$ ./pre-install.sh
```

3. Run the Asperathos-Visualizer service:

```bash
$ ./run.sh
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
