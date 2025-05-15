# Understanding publisher and message broker
a. How much data your publisher program will send to the message broker in one run?
In one run, my publisher program sends 5 messages to the message broker, where each message contains a serialized UserCreatedEventMessage struct with a user_id (e.g., "1") and a user_name (e.g., "2306229840-Amir"), totaling approximately 100–125 bytes (assuming ~20–25 bytes per message, including Borsh serialization overhead). The exact size may vary slightly based on string lengths and serialization details, but the payload remains minimal since it only consists of small text data.

b. The url of: “amqp://guest:guest@localhost:5672” is the same as in the subscriber program, what does it mean?
The identical AMQP URL (amqp://guest:guest@localhost:5672) in both the publisher and subscriber programs means they are connecting to the same RabbitMQ broker instance on the local machine, using the default credentials and port, ensuring that messages published to the user_created queue by the publisher are delivered to the subscriber, enabling proper communication between the two components through the centralized message broker.

# Running RabbitMQ as message broker
![Running RabbitMQ as message broker](static/images/rabbitmq.png)

# Sending and processing event
The image below shows the interaction between the publisher and the subscriber. When cargo run is executed in the publisher directory (right side), it sends five user_created events to the message broker (RabbitMQ). On the left side, the subscriber console shows the incoming messages being received and processed, each containing a UserCreatedEventMessage with different user IDs and names. This confirms that the subscriber is successfully consuming the events published by the publisher.

![Sending and processing event](static/images/connection.png)

# Monitoring chart based on publisher
The chart below is a screenshot of the RabbitMQ Management UI, displaying a visible spike in message rates on the second graph (messages published). This spike occurs every time the publisher is run using cargo run. Each run triggers the sending of five user_created events to the message broker, which results in a short burst of publishing activity. The spike clearly represents the burst of messages being sent by the publisher and confirms that RabbitMQ is actively receiving and routing the messages.

![Monitoring chart based on publisher](static/images/spikes.png)

# Simulation slow subscriber
The chart below shows a spike in queued messages in RabbitMQ after running the publisher 10 times. Although each run was expected to send 5 messages (totalling 50), the total number of queued messages peaked at around 35 before gradually dropping to 0. This happened because not all messages were consumed immediately, some may have temporarily queued due to slight delays in the subscriber processing or RabbitMQ scheduling. The messages were eventually delivered and acknowledged, as shown by the Ready, Unacked, and Total counts dropping back to zero. This confirms that the queue handled the burst efficiently, and the subscriber successfully processed all events.

![Simulation slow subscriber](static/images/slowpoke.png)

