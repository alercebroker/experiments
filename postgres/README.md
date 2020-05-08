# Postgres

We use 2 configurations:

1. **4 CPU - 32 GB**
In grafana we realised that CPU and RAM were used almost 100% so we decided to increase to a bigger instance.

2. **16 CPU - 128 GB**
Using this configuration we observe that at the beginning of the experiment the CPU was used at 100% and the use of RAM start to increase linearly. At the end the use of CPU decreased at less than 10% and the RAM was used almost at 100%.

We need to monitor Disk I/O in grafana and maybe add more indicators specific to PostgreSQL.
