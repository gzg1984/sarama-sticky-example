# sarama-sticky-example
The standard Sarama sticky Strategy can NOT sticky on specified partition from start and not re-assign partition to other consumer. I fix it. 

# How to test
1: create a kafka instance
2: create a topic with 2 partition
```
bin/kafka-topics.sh --create --bootstrap-server 127.0.0.1:9092 --replication-factor 1 --partitions 2 --topic stickytest
```
3: push data into it , you can perform this action via standard sarama tool
```
while :
do
./kafka-console-producer -brokers "127.0.0.1:9092" -topic "stickytest" -value $COUNT
COUNT=`expr $COUNT + 1`
sleep 1
done
```

4: Create 2 separated console , one runs :
```
./sarama-sticky-example -brokers "127.0.0.1:9092" -topics "stickytest" -group "normal"  -partition 1
```
and others run:
```
./sarama-sticky-example -brokers "127.0.0.1:9092" -topics "stickytest" -group "normal"  -partition 0 
```

5: every instance will sticky to the specified partition, even if it is shutdown, the other consumer will NEVER consume the partition(s) which is not specified in parameter

__this feature is very usefull in online-servers which runs with other scaler system (aka k8s) outside the kafka .__