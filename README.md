# kafkaml-anomaly-detection
Project for real-time anomaly detection using kafka and python

It's assumed that zookeeper and kafka are running in the localhost, it follows this process:

- Train an unsupervised machine learning model for anomalies detection
- Save the model to be used in real-time predictions
- Generate fake streaming data and send it to a kafka topic
- Read the topic data with several subscribers to be analyzed by the model
- Predict if the data is an anomaly, if so, send the data to another kafka topic
- Subscribe a slack bot to the last topic to send a message in slack channel if
an anomaly arrives

This could be illustrated as:

![Diagram](./docs/kafka_anomalies.png?style=centerme)

Article explaining how to run this project: [medium](https://towardsdatascience.com/real-time-anomaly-detection-with-apache-kafka-and-python-3a40281c01c9)


# Demo
Generate fake transactions into a kafka topic:
![Transactions](./docs/transactions_producer.gif)

Predict and send anomalies to another kafka topic
![Anomalies](./docs/anomalies.gif)

Producer and anomaly detection running at the same time

![Concurrent](./docs/concurrent.gif)


Send notifications to Slack
![Slack](./docs/slack_alerts.gif)

# Usage (with Window):

* Install library
```bash
pip install -r requirements.txt 
```

* First train the anomaly detection model, run the file:

```bash
python model/train.py
```

* Create the required topics

```bash
cd c:/kafka
bin\windows\kafka-topics.bat --bootstrap-server localhost:9092 --topic transactions --create --partitions 3 --replication-factor 1
bin\windows\kafka-topics.bat --bootstrap-server localhost:9092 --topic anomalies --create --partitions 3 --replication-factor 1
```

* Check the topics are created

```bash
bin\windows\kafka-topics.bat --bootstrap-server localhost:9092 --list
```

* Check file **settings.py** and edit the variables if needed

* Start the producer, run the file

```bash
python producer.py
```

```bash
bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic transactions
```

* Start the anomalies detector, run the file

```bash
python anomalies_detector.py
```

```bash
bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic anomalies
```

* Start sending alerts to Slack, make sure to register the env variable SLACK_API_TOKEN,
then run

```bash
python bot_alerts.py
```
