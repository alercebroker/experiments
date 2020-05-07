# Kafka
This set of experiments mainly consists of testing, modifying and optimizing KAFKA own configurations to obtain better results both in performance and in the processing times of the different pipeline steps. 
## 1 - File Descriptor
## 2 - Ram Cache
## 3 - increased disks
### Description: 
At the beginning of the tests, the EC2 instance only had one disk (root), to scale, we created 3 disks, which were mounted in 3 corresponding folders. With this we managed to segment the W / R of the topics achieving better R / W speeds.
### Steps for implementation
1 - Add 3 new volumes on AWS <br/>
2 - Associate them to the kafka instance <br/>
3 - The disks must be mounted as follows: 
- lsblk - see partitions <br/>
- sudo mkfs -t xfs /dev/xxx - format the partition <br/>
- sudo mkdir /data - create the folder where we will mount the partition <br/>
- sudo mount /dev/xxx /data - mount volume <br/>
4 - Change Kafka configuration file <br/>
- We must find and edit the kafka configuration file (Server.properties) <br/>
- Edit the log-dirs line adding the new paths, separating them by (,) <br/>
5 Give write and read permissions to the user where Kafka runs. <br/>
- sudo chown ec2-user:ec2-user -R /path/data <br/>
6 restart kafka service <br/>

## 4 - Configuraciones de Server.Properties
