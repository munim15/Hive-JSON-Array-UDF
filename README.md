# Hive Nested JSON Arrray UDF  
This UDF takes in a 'JSON string' and a path to a JSON array and collects all elements specified by this path (handles nested JSON arrays as well).

**Example:**

Assume this JSON is in a row of some table:

```json
{
	"request" : {
		"user" : "Mario",
		"location" : "His house.",
		"siblings" : ["Luigi"],
		"countries" : [
			{
				"name" : "USA",
				"regions": ["California", "New York", "Washington"]
			},
			{
				"name" : "Japan",
				"regions" : ["Tokyo", "Osaka", "Aichi"]
			},
			{
				"name" : "Italy",
				"regions" : ["Lazio", "Lombardy", "Veneto"]
			}
		],
		"id" : 619
	},
	"flags" : {
		"activated" : false
	},
	"meta" : {
		"name" : "Alpha"
	}
}
```

Path 1
```shell
hive> select name from json_table LATERAL VIEW explode(jsonArray(data, 'request.countries')) t AS name;
.
.
.
MapReduce Jobs Launched: 
Job 0: Map: 1   Cumulative CPU: 1.3 sec   HDFS Read: 643 HDFS Write: 174 SUCCESS
Total MapReduce CPU Time Spent: 1 seconds 300 msec
OK
{"name":"USA","regions":["California","New York","Washington"]}
{"name":"Japan","regions":["Tokyo","Osaka","Aichi"]}
{"name":"Italy","regions":["Lazio","Lombardy","Veneto"]}
Time taken: 17.082 seconds, Fetched: 3 row(s)
```
Path 2
```shell
hive> select name from json_table LATERAL VIEW explode(jsonArray(data, 'request.countries.regions')) t AS name;
.
.
.
MapReduce Jobs Launched: 
Job 0: Map: 1   Cumulative CPU: 1.23 sec   HDFS Read: 643 HDFS Write: 71 SUCCESS
Total MapReduce CPU Time Spent: 1 seconds 230 msec
OK
California
New York
Washington
Tokyo
Osaka
Aichi
Lazio
Lombardy
Veneto
Time taken: 14.134 seconds, Fetched: 9 row(s)
```


Dependencies : Maven

## Compiling and Building

To compile run:

```
# mvn compile
```

To get JAR (located in target/) run:

```
# mvn package
```

## Usage
```
hive> ADD JAR target/hive-udfs-1.0-SNAPSHOT.jar; 
hive> create temporary function jsonArray as 'ArrayUDF';
hive> create table json_table( data string) 
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe'
STORED AS INPUTFORMAT 'org.apache.hadoop.mapred.TextInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION '/hive/test/';
hive> select name from json_table LATERAL VIEW explode(jsonArray(data, 'request.countries')) t AS name;
```
