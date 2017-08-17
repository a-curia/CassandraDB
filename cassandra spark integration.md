### install java 8
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java7-installer
java -version

### install cassandra
echo "deb http://www.apache.org/dist/cassandra/debian 310x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list 
curl https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add - 
sudo apt-get update 
sudo apt-key adv --keyserver pool.sks-keyservers.net --recv-key A278B781FE4B2BDA 
sudo apt-get install cassandra 
nodetool status

### sample usage of cassandra
// Create a keyspace

CREATE KEYSPACE test WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 1 };

// create a table in that keyspace 

USE "test";
CREATE TABLE some_test_table(key uuid PRIMARY KEY, value int);

// store some goodies

INSERT INTO some_test_table(key, value) VALUES (1, 4232);
INSERT INTO some_test_table(key, value) VALUES (2, 23423);

// and retrieve them again

SELECT * from some_test_table;

### install spark
wget http://apache.is.co.za//spark/spark-1.5.1/spark-1.5.1-bin-hadoop2.6.tgz
tar xvzf spark-1.5.1-bin-hadoop2.6.tgz
bin/spark-shell
sc.parallelize( 1 to 50 ).sum()

### install spark cassandra connector
sudo apt-get install git
git clone https://github.com/datastax/spark-cassandra-connector.git
cd spark-cassandra-connector
git checkout v1.4.0
./sbt/sbt assembly

cp spark-cassandra-connector/target/scala-2.10/spark-cassandra-connector-assembly-1.4.0-SNAPSHOT.jar ~
bin/spark-shell --jars ~/spark-cassandra-connector-assembly-1.4.0-SNAPSHOT.jar

sc.stop
import com.datastax.spark.connector._, org.apache.spark.SparkContext, org.apache.spark.SparkContext._, org.apache.spark.SparkConf
val conf = new SparkConf(true).set("spark.cassandra.connection.host", "localhost")
val sc = new SparkContext(conf)
val test_spark_rdd = sc.cassandraTable("test", "some_test_table")
test_spark_rdd.first
