When I started learning about distributed systems a few years ago, I got really confused. It seemed like every system had a sort of "trick" that let it *just work*. 

Fun fact: Dynamo (from the famous 2007 paper) and DynamoDB (what you probably use today) are architected very differently. Marc Brooker recently covered this in a [good blog post](https://brooker.co.za/blog/2025/08/15/dynamo-dynamodb-dsql.html) to set the record straight.

Keeping track of how different systems compare to one another and have evolved over time is hard. Do you know off the top of your head how Kafka does replication as opposed to Cassandra? How about these options compared to ScyllaDB? Why is RocksDB so much faster again??!?

In any case, I found out that it would be useful to describe these systems as a function of certain distributed system primitives, arranged in a certain way. At the end of the day, they really aren't that magical and over the next few weeks, I'll be writing about these building blocks.