# Dummy process

This set of experiments consisted in executing a "dummy" step which didn't have internal processing other than AVRO serialization.

Each dummy step only consumes alerts and immediatly produces the same content.

# Table of Contents
1. [Experiment 1: Default](#experiment1)
2. [Experiment 2: Kafka with more power](#experiment2)
3. [Experiment 3: Flush with every message](#experiment3)
4. [Experiment 4: 16 partitions](#experiment4)
5. [Experiment 5: Pipeline replication](#experiment5)
6. [Experiment 6: Script](#experiment6)
7. [Experiment 7: Script with serialization](#experiment7)

<a id='experiment1'></a>
## Experiment 1: Default
With this experiment we tried to set a reference point for comparison with later experiments.

### Configuration
#### Hardware:
##### Kafka
* Cores: 4
* RAM: 8GB
#### Kafka Service:
```
num.io.threads=8
num.network.threads=3
num.partitions=32
```

### Results
A rate of 11.200 alerts/30s was set in the producer which was able to reach it for a short period and then decayed with the pass of time.

* Number of processed alerts: 140.511
* CPU average use: <10%
* Producer rate: 11.200 (at first, then decayed)


---

<a id='experiment2'></a>
## Experiment 2: Kafka with more power
Given that the production rate wasn't stable, we tried new configurations within kafka in terms of hardware as well as service properties. In particular, we increased the number of threads that handle network requests with the `num.network.threads` parameter.
### Configuration
#### Hardware:
##### Kafka
* Cores: 16
* RAM: 64GB
#### Kafka Service:
```
num.io.threads=8
num.network.threads=32
num.partitions=32
```

### Results
A rate of 16.000 alerts every 30 seconds was set in the producer being maintained through time.
### Observations
The increase in `num.network.threads` allowed to stabilize the producer rate.



---

<a id='experiment3'></a>
## Experiment 3: Flush with every message
The objective with this experiment was to test the incidence of the flush operation in the producer throughput.

Flush is the operation that tells kafka that messages have been sent and kafka responds if they were received and stored. It also waits until any transaction completes if they were still processing.

In this experiment we called flush after every message was produced and run 3 dummy steps as follows:

1. rate_producer
2. correction
3. features

### Configuration
#### Hardware:
##### Kafka
* CPU: 16 cores
* RAM: 64GB
#### Kafka Service
```
num.io.threads=8
num.network.threads=32
num.partitions=32
```

#### Kafka producer
```
'queue.buffering.max.messages': 10000000,
'linger.ms': 5,
'queue.buffering.max.kbytes': 10485760
```
A brief explanation of these parameters:
* `queue.buffering.max.messages` indicates the maximum ammount of messages accumulated by the producer before they are sent
* `linger.ms` indicates the time in milliseconds that the producer accumulates messages before they are sent
* `queue.byffering.max.kbytes` indicates the maximum size in kilobytes that the producer accumulates before they are sent (this parameter has priority over the number of messages)

Together this three parameters control the frequency that the producer produces messages, given by accumulation limit or time limit (whatever happens first)
### Results
* rate_producer rate: 1600 a/s
* correction rate: 1600 a/s
* features rate: 1600 a/s
* number of alerts: 1.2 million
### Observations
With this and further experiments we see that flush frequency is not as important in throughput in our case, but it is recommended to flush at the end or at least not so frequently.



---

<a id='experiment4'></a>
## Experiment 4: 16 partitions

### Configuration
#### Hardware:
##### Kafka
* CPU: 16 cores
* RAM: 64GB
#### Kafka Service
```
num.io.threads=8
num.network.threads=32
num.partitions=16
```

#### Kafka producer
```
'queue.buffering.max.messages': 10000000,
'linger.ms': 5,
'queue.buffering.max.kbytes': 10485760
```
### Results
* rate_producer rate: 1000 a/s
* correction rate: 1000 a/s
* features rate: 1000 a/s
* number of alerts: 1.2 million
### Observations
Lowering the number of partitions and processes also decreased throughput.



---

<a id='experiment5'></a>
## Experiment 5: Pipeline replication
In this experiment we tried to emulate the real environment in which the production pipeline would be run.

![](https://i.imgur.com/0y0WRmR.png)

### Configuration
#### Hardware: steps
* CPU: 32 cores
* RAM: 32 GB
#### Hardware: Kafka
* CPU: 16 cores
* RAM: 64GB
### Results
* Number of alerts: 1.2 million
* Kafka CPU usage: 80-90 %
* Kafka RAM usage: 90-100 %
#### Correction
* Rate a/s: 900-1000
* Rate MB/s 60-70
#### Features
* Rate a/s: 900-1000
* Rate MB/s 60-70
#### Late classifier
* Rate a/s: 900-1000
* Rate MB/s 60-70
#### Outlier
* Rate a/s: 900-1000
* Rate MB/s 60-70
#### Stamp classifier
* Rate a/s: 900-1000
* Rate MB/s 60-70
#### Xmatch
* Rate a/s: 900-1000
* Rate MB/s 60-70
#### S3
* Rate a/s: 900-1000
* Rate MB/s 60-70
#### Magnitude
* Rate a/s: 900-1000
* Rate MB/s 60-70

### Observations
We checked that throughput does not decrease linearly as the number of topics increase and that kafka service configurations put up with that rate.



---

<a id='experiment6'></a>
## Experiment 6: Script
With this experiment we tried to simulate the behaviour of the dummy step, but outside of APF. We only used a python script that runs 32 processes in parallel consuming alerts from a source topic and producing to a target topic.
### Configuration

#### Hardware:
##### PC:
* Processes: 32
* Cores: 32
* RAM: 32 GB
##### Kafka:
* Cores: 16
* RAM: 64 GB

#### Kafka service:
```
num.partitions=32
```

### Results

* Rate a/s: 4200
* Rate MB/s 300
* Number of alerts: 1.2 million
* CPU usage: ~ 100%
* RAM usage: ~ 100%

![](https://i.imgur.com/XHgn3NN.png)
In this picture the script was producing to the topic named `xmatch_exp` and is shown as a red line in the graph.

### Observations

Las tasas obtenidas levantan sospecha sobre el paso dummy ya que se alcanzan una taza mucho más alta que la obtenida con el paso dummy. Además al procesar a una mayor tasa, pudimos establecer párametros en el producer que permitieran consumir ese volúmen sin errores.



---

<a id='experiment7'></a>
## Experiment 7: Script with serialization
With this experiment we tried to do the same as in experiment 6, but with AVRO serialization (using fastavro) for messages as it is done in the real steps.
### Configuration

#### Hardware:
##### PC:
Procesos: 32
Cores: 32
RAM: 32 GB
##### Kafka:
* Cores: 16
* RAM: 64 GB

### Results

* Rate a/s: 2500
* Rate MB/s 175
* Number of alerts: 1.2 million

![](https://i.imgur.com/GHhWsaZ.png)


### Observations
We also did profiling on the dummy steps and get that around 37.5% of the execution time is spent in serialization. The results from this experiment show a decrease of around 40% in throughput, from 4200 to 2500 alerts per second.
