# Zeek Kafka Plugin
Zeek Streams obviously relies on your Zeek instance(s) having the Kafka package/plugin up and running.

The Zeek Kafka plugin is on Github with the instruction on how to install. 

- <https://github.com/apache/metron-bro-plugin-kafka>

## Important Stuff

### ZKG Install
When you use **zkg** to install the plugin you definitely need the `-version master` flag. Without that flag I've not have any success on getting the test to pass or the plugin to install.


```
$ zkg install apache/metron-bro-plugin-kafka --version master
```

You can verify install with 

```
$ zeek -N Apache::Kafka
$ zkg list
```

### Kafka Plugin Configuration
You can setup your Kafka Configuration in different ways but you must have a **separate** topic for each log type. Kafka stream best practices are to have each stream be a different type of data (schema). Combining different data/schema into one topic is bad practice and will only cause issues down the road.

- <https://github.com/apache/metron-bro-plugin-kafka#example-4---send-each-zeek-log-to-a-unique-topic>

Here's a shortcut config to have each log create its own topic.

```
@load packages/metron-bro-plugin-kafka
redef Kafka::topic_name = "";
redef Kafka::send_all_active_logs = T;
redef Kafka::kafka_conf = table(
    ["metadata.broker.list"] = "localhost:9092",
    ["client.id"] = "zeek"
);
```
This config will have ANY log created by zeek make a new topic.. so dns.log will create a topic named **dns**, weird.log will create a topic named **weird**.. and so on. Again this is an important part of the configuration to setup. **Zeek Streams** will process each of these streams separately and will rely on this standard topic naming convention.

