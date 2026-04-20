Current partition by Employer is a good start. 

We could add concurrency to the member portion (DB might become bottleneck without Kafka/RabbitMQ?)

i.e. - subdivide members per employer into job pages 
     - place in queue
     - have concurrent futures polling queue (use config to enable this on both nodes) (i.e. run 10 pages concurrently x2)

