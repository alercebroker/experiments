# Summmary

Experiments were run by doing a combination tweaks to the following aspects of the pipeline environment.

1. Kafka service configuration
2. Kafka broker hardware
3. Kafka produer parameters
4. Step settings
5. Database hardware

There was also a lot of effort put into **monitoring** these components of the environment. We used **Grafana** for the most part, but also the steps send metrics to elasticsearch which are visualized using **Kibana**.

Another portion of the experiment work was spent doing **profiling**, which allowed us to gain insights on what parts of the code were more resource intensive and or not functioning as expected.

## Important facts

### Tweaks to Kafka
* **Number of network threads**: At the begining we increased the number of network threads to the kafka service. This allowed to stabilize higher throughputs
* **Hardware**: The best results were achieved with at least 16 cores and 64GB of RAM. Some tests were run increasing the number of disks but these are not conclusive yet.
* **Number of partitions**: Working with 32 partitions had better results.
* **Flush and producer configuration**: Modifying flush behaviour didn't matter too much in terms of throughput but some parameters were needed when the flush is done at the end:
    * queue.buffering.max.messages
    * queue.buffering.max.ms or linger.ms
    * queue.buffering.max.kbytes

### Dummy steps and producing with a script
Some tests were done using *dummy* steps which are APF steps that only consume and produce a message with no processing or functionality involved.

On the other hand, some tests were done using a python *script* that only consumed and produced messages with no processing or functionality involved.

The results showed that:
* **Hardware**: Kafka hardware and configuration that we tested is enough for our desired throughput
* **Best throughput**: Our best result achieved 4.200 alerts per second using the script
* **Profiling**: Serializing and deserializing AVRO takes around 37.5% of the execution time in a dummy step

### Step experiments
With profiling we could check the following facts, but keep in mind performance is cut due to the consumer issue:
* **Elasticsearch**: was performing slow, and causing a bottleneck
    * Disabling Elasticsearch improved performance
* **Correction**: the most time is spent querying the database
    * Some improvements to this were made by increasing Postgresql hardware
* **Features**: the most time is spent actually computing features

### Tweaks to Postgresql

The main change that we made was to change the AWS instance from **4 CPU - 32 GB** to **16 CPU - 128 GB**.

## TODO
There are still a lot of testing to be done. Some of the most important are:
* Run all steps again (maybe dummies too)
* Determine optimal hardware for each step
