# Hive Nested JSON Arrray UDF  
This UDF takes in a 'JSON string' and a path to a JSON array and collects all elements specified by this path (handls nested JSON arrays as well).

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
hive> create table json_table( data string) ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe' STORED AS INPUTFORMAT 'org.apache.hadoop.mapred.TextInputFormat' OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat' LOCATION '/hive/test/';
hive> select name from json_table LATERAL VIEW explode(jsonArray(data, 'request.countries')) t AS name;
```