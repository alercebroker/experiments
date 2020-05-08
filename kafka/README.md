# Kafka
This set of experiments mainly consists of testing, modifying and optimizing KAFKA own configurations to obtain better results both in performance and in the processing times of the different pipeline steps. 
## 1 - File Descriptor
## 2 - Ram Cache
## 3 - increased disks
### Description: 
At the beginning of the tests, the EC2 instance only had one disk (root), to scale, we created 3 disks, which were mounted in 3 corresponding folders. With this we managed to segment the W / R of the topics achieving better R / W speeds.

### Steps for implementation

#### 1- Add 3 new volumes on AWS 

#### 2- Associate them to the kafka instance 

#### 3- The disks must be mounted as follows: 
```
- lsblk - see partitions 
- sudo mkfs -t xfs /dev/xxx - format the partition 
- sudo mkdir /data - create the folder where we will mount the partition
- sudo mount /dev/xxx /data - mount volume
```
#### 4- Change Kafka configuration file 

```
- We must find and edit the kafka configuration file (Server.properties)
- Edit the log-dirs line adding the new paths, separating them by (,)
```
#### 5- Give write and read permissions to the user where Kafka runs. 
```
- sudo chown ec2-user:ec2-user -R /path/data 
```
#### 6- restart kafka service 
```
service kafka restart
```

## 4 - Experiments and settings 
### Experiment 1
### Configuration

#### Hardware:
##### Kafka server:
* Cores: 4
* RAM: 8 GB
##### Postgres:
* Cores: 4
* RAM: 32 GB

#### Kafka settings:
```
num.io.threads=8
num.network.threads=3
num.partitions=32
```

#### Script:
* Processes: 32

### Results

* Rate /30seg: 11.2k

### Observations

*-----

### Experiment 2
### Configuration

#### Hardware:
##### Kafka server:
* Cores: 16
* RAM: 64 GB
##### Postgres:
* Cores: 16
* RAM: 128 GB

#### Kafka settings:
```
num.io.threads=8
num.network.threads=32
num.partitions=32
```

#### Script:
* Processes: 32

### Results

* Rate /30seg: 16k

### Observations

*-----
