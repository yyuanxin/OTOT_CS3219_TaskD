# CS3219 OTOT Assignment Task D
This consists of a 3-node Apache Kafka cluster using Docker, 
with a Zookeeper ensemble created to manage the Kafka cluster.

The kafka containers are: kafka1, kafka2, kafak3 <br>
The zookeepers containers are: zoo1, zoo2, zoo3

## Set up to run the docker containers
 1. Clone project to local machine
 2. Go to root of project directory
 4. Run `docker-compose up -d` in your terminal

## Implementation of Pub-Sub messaging system using Apache Kafka
###1. Open a bash to a running Kafka Docker container 
 
 Format:
 ```shell script
 docker exec -i -t <String: container_name> bash
 ```
 Example:
 ```shell script
 docker exec -i -t kafka1 bash
 ```
 
###2. Start by creating a topic
    
Format:
```shell script
kafka-topics.sh --create --topic <String: topic> --bootstrap-server <String: server to connect to>
```
Note: 
- `--bootstrap-server` refers to the kafka servers you want to connect to for this topic.
- `<server to connect to>` is in the format of `<hostname : port number>`

Example:
```shell script
kafka-topics.sh --create --topic bulletin --bootstrap-server kafka1:9092, kafka2:9092, kafka3:9092
```
        
###3. You can either publish (producer role) or view messages (consumer role) to the topic you've just created
    
a. **Publisher Role** : Send new messages to the topic

 Format:
 ```shell script
 kafka-console-producer.sh --topic <String: topic> --bootstrap-server <String: server to connect to>
 ```
 Example:
 ```shell script
 kafka-console-producer.sh --topic bulletin --bootstrap-server kafka1:9092, kafka2:9092, kafka3:9092
 ```

 After executing above command, you can start typing in the session
 ```shell script
 > Hello, this is the first message
 > This is the second message
 > If you are seeing this, you are a subscriber to this topic
 > You can send messages if you run the publisher command
 ```
 Run `Ctrl` + `C` to exit session   

---

b. **Subscriber Role** : View messages that were published to the topic

 Format:
 ```shell script
 kafka-console-consumer.sh --topic <String: topic> --from-beginning --bootstrap-server <String: server to connect to>
 ```
 Example:
 ```shell script
 kafka-console-consumer.sh --topic bulletin --from-beginning --bootstrap-server kafka1:9092, kafka2:9092, kafka3:9092
 ```

 After executing above command, you can view all the messages published to this topic
 ```shell script
 Hello, this is the first message
 This is the second message
 If you are seeing this, you are a subscriber to this topic
 You can send messages if you run the publisher command
 ```
 Run `Ctrl` + `C` to exit session   

###4. Exit bash session for this kafka container by running `Ctrl` + `D`

####You can run the above instructions (step 1 - 4) on any other kafka containers. 
Simply replace the `<container_name>` of step 1 to:
- **kafka1** or **kafka2** or **kafka3**
    
## Zookeeper Failover for Kafka Cluster
###1. Open a bash to a running Zookeeper container 

 Format:
 ```shell script
 docker exec -i -t <String: container_name> bash
 ```
 Example:
 ```shell script
 docker exec -i -t zoo1 bash
 ```

###2. Check the status of the zookeeper (if it's a follower or leader) <br>
*if it's a leader, it means this zookeeper is the master node in the cluster*

 ```shell script
 zkServer.sh status
 ```

**To exit bash session for this zookeeper container, run `Ctrl` + `D`**

###3. Run Step 1 - 2 on all zookeeper containers until you find the leader zookeeper.

###4. Once you have found the leader, terminate the running leader's container:

Format:
```shell script
docker stop <String: container_name>
```
Example: <br>
Scenario - *if zoo1 is the master node of the cluster, terminate zoo1 container*
```shell script
docker stop zoo1
```

####You can verify that another node has become the new leader of the kafka cluster by running step 1 - 2 on the other zookeeper containers.
Simply replace the `<container_name>` of step 1 to either:
- **zoo1** or **zoo2** or **zoo3** 
  
   