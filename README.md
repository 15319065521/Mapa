#  Asynchronous Federated Learning with Differential Privacy for Edge Intelligence
Multi-stage adjustable private algorithm(MAPA) by computational differential privacy (DP) to protect the training process,focusing on the impacts of noise on the machine learning in AFL and a better trade-off between the model accuracy and privacy guarantee to improve the trade-off by dynamically adjusting the variance of noise

In order to compare the algorithm, we set up three comparison methods, soa、 ms and mapa. And the algorithm is encapsulated in the docker container and defined by the makefile.
## 1.Environment Deployment

|       OS       | Ubuntu 18.04 |
| :------------: | :----------: |
|     Docker     |   18.09.7    |
| Docker-compose |    1.24.1    |
|    OpenSSH     |     7.6      |
## 2. Project structure

* Cloud

```
├──cloud
|    ├── cloud-soa.py             Stochastic optimization algorithm 
|    ├── cloud-mapa.py            Multi stage adjustable private algorithm
|    ├── cloud-ms.py              Centralized gradient descent algorithm
|    ├── Dockerfile               Docker image build file
|    ├── docker-compose.yml       Defining YAML files for services, networks, and volumes
|    ├── params.py
|    └── sources.list
|
├──edge
|    ├── Dockerfile    
|    ├── docker-compose.yml   
|    ├── sources.list
|    ├── edge-soa.py
|    ├── edge-mapa.py
|    ├── edge-ms.py
|    ├── data                               Datasets
|    ├   └──MNIST
|    ├      └──  …
|    └──  result                              Output
|        └── [EDGE_NUM:3][METHOD:soa]        Naming rules: number of edge nodes, number of containers
├──result
├──Makefile                          Setting parameters    
├──README.md
└──ssh_config
 
```

## 3.Network Configuration
Before the application runs, first perform the network configuration of the user operating device and the working node, and write the network topology to the `ssh_config` file, such as：

```
Host cloud
    HostName xxx.xxx.xxx.xxx
    Port 22
    User node2user

Host edge1                                     #Node name
    HostName xxx.xxx.xxx.xxx                   #ip
    Port 22                                    #port
    User node1user                             #ssh connection username

```

## 4.Parameter settings
Algorithm-related parameters are defined in the Makefile，such as：

```
# In cloud 
CLOUD := cloud
MQTT_IP ?= 192.168.0.101
MQTT_PORT ?= 1884

# In edge
EDGES := edge1
EDGES_NUM ?=1
CONTAINER_NUM ?=3            
METHOD ?= soa  #soa, ms, mapa,audp
BATCH_SIZE ?= 24
EPOCH ?= 1
TEST_NUM ?= 100
DATA_ROOT ?= './data/'
RESULT_ROOT ?= './result/'

```

| **cloud**  |                                  |
| :--------- | :------------------------------: |
| MQTT_IP    |  cloud device MQTT broker ip  |
| MQTT_PORT  | cloud device MQTT broker port |

| **edge**  |                                  |
| :--------- | :------------------------------: |
|EDGES        | edge device name   |
|EDGES_NUM    | edge device numbers             |
|CONTAINER_NUM| Number of containers per edge device  |
|METHOD       | (SOA, MS, MAPA ,AUDP)        |
|BATCH_SIZE   | Batch size                 |
|EPOCH        | Epoch                          |
|TEST_NUM     | Test every TEST_NUM iterations   |
|DATA_ROOT    |Dataset directory           |
|RESULT_ROOT  |result directory                | 


## 5.Run
Under the project folder, execute the shell command to apply the project:
* Perform network configuration between the user-operated device and the working node, including the configuration of ssh-free operation. After completing this step, the user operates the device to perform algorithm-related steps on other working nodes directly through ssh.
```
make net_config
```
* The program and algorithm data packets are transmitted from the user operating device to the corresponding working node and mirrored.
```
make build
```
* Start the container, transfer the parameters to each working node, assign tasks to the working nodes, and start the algorithm work.
```
make run
```
* Look at the work log on the master node, and the log content is dynamically updated in real time following the current program execution.
```
make logs
```
* Output the experimental results to the host.

```
make result
```
* Clean up all containers and free up resources.

```
make clean
```


