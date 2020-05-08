# Xmatch

This set of experiments consisted in executing a "XMatch" using Spark structured streaming job and a script using usinf fink library and CDS API (Simbad).


# Table of Contents
1. [Experiment 1: Spark structured streaming usinf fink, CDS, Simbad](#experiment1)
2. [Experiment 2: Script Wrapper using CDS, Simbad](#experiment2)

<a id='experiment1'></a>
## Experiment 1: Spark structured streaming usinf fink, CDS, Simbad
With this experiment we prepare a light(alerts without schema) topic from historic one, then we run a spark structured streaming job which parse the alerts and do the cross match.

### Configuration
#### Hardware:
##### Kafka
* Cores: 4
* RAM: 8GB

### Results
* Number of processed alerts: 7000
#### Xmatch
* Input Rate a/s: 150
* XMatch Rate a/s 25

### Observations
There were very few alerts with the same schema.
Is needed to repeat this experiments with more alerts and tunning spark


---

<a id='experiment2'></a>
## Experiment 2: Script Wrapper using CDS, Simbad
In this experiment we develop a script composed of:  KafkaConsumer, KafkaProducer, AlertAccumulator and a XmatchClient that runned with 32 processes. We request CDS Xmatch API by means of a wrapper using Pandas DataFrames.
### Configuration
#### Hardware: 
Instance with 32 CPU - 128 GB RAM
### Results  
3000 alerts/second

