# Introduction to Installing PySpark & Jupyter Notebooks on Mac OSX

Spark is used for large-scale distributed data processing.  It has become the go to standard for a lot of companies in the technology industry.  The Spark framework is capable of computing at high speeds, processing massive amounts of resilient sets of data, and it does it all while computing in a highly distributed manner. 

Jupyter Notebooks, commenly called "Jupyter", has been a popular application within the Data Science community for many years.   It enables you to edit, run, and share Python code into a web view.  It allows you to execute your code in a step by step process in order to share parts of your code in a very flexible way for data analysis work.  This is why Jupyter is a great tool to prototype in, and should be used at all companies that are data centric. 

# Why use PySpark in a Jupyter Notebook?

Most data engineers argue that the Scala programming language version is more performant than Python version, and it is.  However, I personally love working in PySpark because it is flexibile, easy to learn, and benefits from all my other favorite Python libraries for data processing.  In my opinion Python is a perfect language for Big Data and Machine Learning fields. 

## What you need to get started
According to Spark documentation (https://spark.apache.org/docs/latest/index.html) as of this publication you will need to to have the following installed to use Pyspark 2.3.0.

- Java version (8+)
- Python version (2.7+/3.4+)
- R version (3.1+) 
- Scala version (2.11.x)

**Please Note:** Support for Java 7, Python 2.6 and old Hadoop versions before 2.6.5 were removed as of Spark 2.2.0. Support for Scala 2.10 was removed as of Spark 2.3.0.

# Install Data Science Tools

1. Check for and install XCode Developer Tools 

Launch a Terminal window, found in /Applications/Utilities/ and type the following: 
```
$ gcc
xcode-select: note: no developer tools were found at '/Applications/Xcode.app', requesting install. Choose an option in the dialog to download the command line developer tools.

$ xcode-select -p
/Library/Developer/CommandLineTools

$ gcc --version
Configured with: --prefix=/Library/Developer/CommandLineTools/usr --with-gxx-include-dir=/usr/include/c++/4.2.1
```

If no gcc found, install xcode VIA commandline by typing into the terminal:
```
xcode-select --install
```

2. Install Brew
```
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

3. Install Wheel, Python, Java8, and Apache Spark
```
$ brew install python-wheel python3 java8 scala2.11 apache-spark
```

4. Install Python modules needed for Data Science analysis
```
$ python3 -m pip install numpy scipy matplotlib jupyter pandas seaborn py4j
```

# Set Up Environment Variables

1. Navigate in your terminal window and add the following code below to your e.g. `vi ~/.bash_profile`.  To save the file hit `esc` then `q!` and the enter key. 
```
# For a ipython notebook and pyspark integration
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_162.jdk/Contents/Home/
export SPARK_HOME=`brew info apache-spark | grep /usr | tail -n 1 | cut -f 1 -d " "`/libexec
export PYTHONPATH=$SPARK_HOME/python:$PYTHONPATH
export HADOOP_HOME=`brew info hadoop | grep /usr | head -n 1 | cut -f 1 -d " "`/libexec
export LD_LIBRARY_PATH=$HADOOP_HOME/lib/native/:$LD_LIBRARY_PATH
export PATH=$JAVA_HOME/bin:$SPARK_HOME:$SPARK_HOME/bin:$SPARK_HOME/sbin:$PATH
```
To execute your new edits type the following:
```
source ~/.bash_profile
```

2. You can check SPARK_HOME path using following brew command to see if Pyspark was added successfully. 
```
$ brew info apache-spark

RMD-MAC-782:~ stirlingwaite$ brew info apache-spark
apache-spark: stable 2.3.0, HEAD
Engine for large-scale data processing
https://spark.apache.org/
/usr/local/Cellar/apache-spark/2.3.0 (1,018 files, 243.7MB) *
  Built from source on 2018-03-07 at 13:00:19
From: https://github.com/Homebrew/homebrew-core/blob/master/Formula/apache-spark.rb
==> Requirements
Required: java = 1.8 ✔
==> Options
--HEAD
	Install HEAD version
```

# Starting Jupyter Notebooks

1.  Navigate or “cd” into your project folder
2.  Type `jupyter notebook` into your terminal window

# Using PySpark in Jupyter Notebooks

## Initial Python Modules Needed
Input the following into the first cell of your Jupyter Notebook. 
```
from pyspark import SparkConf, SparkContext, SQLContext
from pyspark.sql import SQLContext, SparkSession, HiveContext, Row
from pyspark.sql.types import *
from pyspark.sql.functions import *

import pandas as pd
```

- `Esc` will take you into command mode where you can navigate around your notebook with arrow keys.
- While in command mode:
	- `A` to insert a new cell above the current cell, `B` to insert a new cell below.

## Initializing Spark
Add a new cell and add the following to start PySpark.   The `local[2]` parameter for the method `setMaster` indicates you want to use 2 cores of your CPU (central processing unit).  If you want all cores you can also use `local[*]`.
```
### Initialize streaming context
conf = SparkConf()\
                .setMaster("local[2]")\
                .setAppName("Spark Example")\
                .set("spark.executor.memory", "6g")\
                .set("spark.driver.memory", "6g") 

sc = SparkContext(conf=conf)
sc.setLogLevel("ERROR")
sqlContext = SQLContext(sc)
spark = SparkSession.builder.appName("spark play").getOrCreate()
```

# Now We Are Ready To Play in PySpark
To perform a simple PySpark test add a another cell to your Jupyter Notebook and input.  

    def basicAvg(nums):
        """Compute the avg"""
        sumCount = nums.map(lambda x: (x, 1)).fold(
            (0, 0), (lambda x, y: (x[0] + y[0], x[1] + y[1])))
        return sumCount[0] / float(sumCount[1])
    
    nums = sc.parallelize([1, 2, 3, 4])
    avg = basicAvg(nums)
    print(avg)

You should see `2.5` displayed below. 

In a follow the next post I will go over PySpark fundamentals in more detail, but this should get you started.


# Resources
1. https://stackoverflow.com/questions/17271319/how-do-i-install-pip-on-macos-or-os-x
2. https://spark.apache.org/docs/latest/index.html
3. https://github.com/zipfian/spark-install