# Procesos Pipeline

## Running Steps:

There are four steps with their respective machines IPs:
1. Rate Producer, 52.86.26.221
2. Correction, 34.238.238.229
3. Features, 3.229.237.125
4. Metrics, 35.169.179.140 

To run a step get into their respective virtual machine and run the following command on the `experiment` directory:
    
    docker-compose up --scale <service_name>=<n>
    
Where `n` is the number of partitions and `service_name`is the step services, wich are `rate_producer`, `correction` 
and `features`.

To follow the step logs, run:

    docker-compose logs --follow <service_name>
    
To verify wich step is up, run `docker-compose ps`.

Every step has a consumer and a procuer. They run on inverse order to generate the consumer before the previous step 
starts producing.
To modify the consumers and producers, go to the `docker-compose.yml` file on the `experiment` directory and edit the
`CONSUMER_TOPICS` and `PRODUCER_TOPICS` values.

## Experiments

Experiments mainly focused on `correction` and `features`, so it was testest with diferent combinations of steps
and machines.

### Experiment 1-a:

Experiment for correction and feature steps with 32 partitions.

#### Hardware:
* Cores: 4 
* RAM: 32 GB

#### Results:
* Input: 1.5 K/s - 100 MB/s
* Correction: 400/s - 20 MB/s
* Features: 80/s - 150 KB/s

### Experiment 1-b:

Experiment for correction and feature steps with 32 partitions.

#### Hardware:
* Cores: 16
* RAM: 64 GB

#### Results:
* Input: 1.5 K/s - 40 MB/s
* Correction: 800/s - 40  MB/s
* Features: 130/s - 180 KB/s

### Experiment 2-a:

Experiment for feature step with 32 partitions.

### Experiment 2-b:

Experiment for feature step with 32 partitions.

### Experiment 3-a:

Experiment for correction and feature steps with 64 partitions.

### Experiment 3-b:

Experiment for correction and feature steps with 64 partitions.

### Experiment 4-a:

Experiment for feature step with 64 partitions.

### Experiment 4-b:

Experiment for feature step with 64 partitions.

### Experiment 5-a:

Experiment for correction and feature steps with 96 partitions.

### Experiment 5-b:

Experiment for correction and feature steps with 96 partitions.

### Experiment 6-a:

Experiment for feature step with 96 partitions.

### Experiment 6-b:

Experiment for feature step with 96 partitions.
