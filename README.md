# CST8915 Lab8

## Video Demo
Youtube Video link: https://www.youtube.com/watch?v=o9zhl8-7psk

## Task 2: Improving and Extending the Deployment

In Task 2, I improved the deployment by making MongoDB and RabbitMQ persistent and more reliable. In the original setup, both services were running inside the cluster without persistent storage, so their data would be lost if the pods were deleted or restarted.

### MongoDB persistence and high availability

To improve MongoDB, I changed it to run as a StatefulSet with 3 replicas. This allows MongoDB to work as a replica set, where one node is the primary and the others are secondary replicas. This improves availability because if one node fails, the others can continue serving requests.
I also made the MongoDB service headless (clusterIP: None) so that each pod has a stable DNS name (like mongodb-0.mongodb).
To make the data persistent, I added a PersistentVolumeClaim (PVC) using volumeClaimTemplates. Each MongoDB pod stores its data in /data/db, so the data is kept even if the pod is deleted.

### RabbitMQ persistence

For RabbitMQ, I added a PersistentVolumeClaim and mounted it at: /var/lib/rabbitmq
This is where RabbitMQ stores its queue data. With this change, messages are not lost when the pod restarts. I also kept the ConfigMap for plugins so that RabbitMQ configuration still works.

### Proving MongoDB persistence

To test MongoDB, I created and processed orders so they would be stored in the database. Then I deleted one MongoDB pod. After it restarted, I connected to another MongoDB pod and checked the data again. The orders were still there. This proves that data is persistent and other nodes of the replica set.

### Proving RabbitMQ persistence

To test RabbitMQ, I created a couple of orders and then deleted the RabbitMQ pod. After the pod restarted, the application still worked and the order workflow was not lost. This shows that RabbitMQ data is also persistent.

### Azure managed services

In a real production environment, these services could be replaced with Azure managed services:

- Azure Cosmos DB for MongoDB: a managed database service with built-in scaling, replication, and backups.
- Azure Service Bus: a managed messaging service that replaces RabbitMQ and handles queues reliably.

