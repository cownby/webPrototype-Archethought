---
layout: post
title: "Trials & Tribulations of Data Completion 1"
author: carolyn_ownby
share: true
modified:
categories: blog
tags:	[linux, data]
date:   2017-01-26T12:00:00

---

# Overview

A wise person once said, "Data completion is 90% of the work in a data pipeline." (Technology consultant Dixon Dick) My experience thus far bears out this statement!

This was my first foray into Spark, where my goal was to perform data investigation. Here I outline my path to get to the data.

Summary of tools used:

* Apache Spark 1.6
* Scala
* sed
* [csvkit](http://csvkit.readthedocs.org/en/0.9.1/)
* [jq](https://stedolan.github.io/jq/)

# Starting point

I started with a 1.7 GB cvs (comma separated values) file of unknown content:

~~~
	1715069652 Rosetta.csv
~~~

Once I found the schema, I used cat to prepend headers: 

~~~
	$ cat headers.csv Rosetta.csv > RosettaWithHdrs.csv
~~~

# Issue
I invoked Spark with 

~~~bash
sudo /opt/spark-1.6.1-bin-hadoop2.6/bin/spark-shell --packages com.databricks:spark-csv_2.11:1.4.0 
~~~

And ran code:

~~~
val df = sqlContext.read.format("com.databricks.spark.csv").option("header","true").option("inferschema","true").load("RosettaWithHdrs.csv")
~~~

Which failed with

~~~
java.io.IOException: (startline 1) EOF reached before encapsulated token finished
~~~

Yikes! Looks like the csv may be malformed. It's a huge file! How to find the problem?

# Break it down, and again
I used sed to successively cut out lines of the file until I found a segment I *could* read, and thus identifying at least one faulty record. Example:

~~~bash
sed -n 1,500000p RosettaWithHdrs.csv  > 5-10e6.csv
~~~
The first record to cause a problem was on line 1031. Turns out that some of the text fields spanned multiple lines. Each record has 24 fields, and the file is too big to view in an editor. It took me a while to realize the issue, and that Spark simply could not accomodate a CSV file of this complexity.

## Find a different format: JSON
I found [csvkit](http://csvkit.readthedocs.org/en/0.9.1/) _enormously_ helpful in exploring the file. 
This suite of tools can also convert csv to json with [csvjson](https://csvkit.readthedocs.io/en/1.0.1/scripts/csvjson.html), so thought I'd see if Spark could handle inter-field carriage returns in JSON format.  The '-z' option specifies maximum field size.

~~~
$ csvjson -z 10000000 RosettaWithHdrs.csv > all.json
~~~
**DISCOVERY: Spark dislikes human-readable JSON**  
Yes! JSON format accomodates records with inter-field carriage returns.

I originally tried sed to split the file, realizing I'd still need to look at the surrounding lines to ensure I captured complete records. I killed the sed process after an hour and looked for an alternative. Enter [jq](https://stedolan.github.io/jq/)!  This is how I determined the number of records in the dataset.  By default, jq produces human readable output. The "-c" flag overrides the default, producing the compressed mess that Spark prefers.

~~~bash
jq 'length' offRec.json 
1
$ jq 'length' all.json
171679

$ jq -c '.[0:1000]' all.json > subset1000.json
scala>
  val datafile="subset1000.json"
  val df = sqlContext.read.json(datafile)

~~~
**DISCOVERY: csvkit indented output is an array of objects, while no indentation yeilds a list of objects; i.e., [{},{},..] vs {},{},...**  

I didn't have enough memory for the entire dataset, but was able to read 16,500 records.
More experimentation later with data persistence and other Spark settings.
	

