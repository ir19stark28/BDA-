## **EXP 1 DOWNLOADING AND INSTALLING HADOOP**
Aim :To perform setting up and installing Hadoop. 

Algorithm :

    1.Install Java (prerequisite).
    2.Download and extract Hadoop.
    3.Set environment variables.
    4.Configure core Hadoop files (core-site.xml, hdfs-site.xml, etc.).
    5.Format the Hadoop filesystem.
    6.Start Hadoop daemons.
    7.Verify Hadoop installation.
Program 
    Configure Hadoop files
        
        core-site.xml

                <configuration>
                <property>
                    <name>fs.defaultFS</name>
                    <value>hdfs://localhost:9000</value>
                </property>
                </configuration>

        hdfs-site.xml
                <configuration>
                <property>
                    <name>dfs.replication</name>
                    <value>1</value>
                </property>
                <property>
                    <name>dfs.namenode.name.dir</name>
                    <value>file:///home/user/hadoopdata/namenode</value>
                </property>
                <property>
                    <name>dfs.datanode.data.dir</name>
                    <value>file:///home/user/hadoopdata/datanode</value>
                </property>
                </configuration>

        mapred-site.xml
                <configuration>
                <property>
                    <name>mapreduce.framework.name</name>
                    <value>yarn</value>
                </property>
                </configuration>

        yarn-site.xml
            <configuration>
            <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
            </property>
            </configuration>

installation Procedure 
1. install java
sudo apt update
sudo apt install openjdk-11-jdk
java -version

2. install hadoop 
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
tar -xzvf hadoop-3.3.6.tar.gz
mv hadoop-3.3.6 /usr/local/hadoop

3.set up environment variables
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64

source ~/.bashrc

Step 4: Edit Hadoop Configuration Files
Modify core-site.xml, hdfs-site.xml, etc., in $HADOOP_HOME/etc/hadoop.

hdfs namenode -format
step 5 : start hadopp services
start-dfs.sh
start-yarn.sh


jps 


****EXP 2 haddop file managaemnt tasks*********



## **AIM:**

To implement basic file management operations in Hadoop Distributed File System (HDFS), including:

* Adding files and directories
* Retrieving files
* Deleting files


##  **ALGORITHM:**

1. **Start Hadoop Services** using the `start-all.sh` script.
2. **Verify running daemons** using the `jps` command.
3. **Create Directories** in HDFS using `hadoop fs -mkdir`.
4. **Create a file locally** and write content into it.
5. **Upload the file to HDFS** using `hadoop fs -put`.
6. **Verify the file exists** using `hadoop fs -ls`.
7. **View file content** using `hadoop fs -cat`.
8. **Delete the file** using `hadoop fs -rm`.
9. **Confirm deletion** using `hadoop fs -ls`.



## 💻 **PROGRAM / IMPLEMENTATION PROCEDURE:**

### 🔹 **Step 1: Start Hadoop Services**

```bash
start-all.sh
```

### 🔹 **Step 2: Check Running Services**

```bash
jps
```

Expected output:

```
NameNode
DataNode
ResourceManager
NodeManager
SecondaryNameNode
```

### 🔹 **Step 3: List Files in Root Directory**

```bash
hadoop fs -ls /
```

### 🔹 **Step 4: Create Directories in HDFS**

```bash
hadoop fs -mkdir /user
hadoop fs -mkdir /user/sample1
hadoop fs -mkdir /user/sample2
hadoop fs -ls /user/
```

### 🔹 **Step 5: Create a Local File**

```bash
echo "This is the new file" > ex.txt
```

**OR**

```bash
nano ex.txt
# Type: This is the new file
# Save and exit
```

### 🔹 **Step 6: Upload File to HDFS**

```bash
hadoop fs -put ex.txt /user/sample1
```

### 🔹 **Step 7: Verify Upload**

```bash
hadoop fs -ls /user/sample1
```

Expected output:

```
Found 1 item
-rw-r--r--   1 yourusername supergroup    XX  Date HH:MM  /user/sample1/ex.txt
```

### 🔹 **Step 8: View File Contents**

```bash
hadoop fs -cat /user/sample1/ex.txt
```

Expected output:

```
This is the new file
```

### 🔹 **Step 9: Delete the File**

```bash
hadoop fs -rm /user/sample1/ex.txt
hadoop fs -ls /user/sample1
```

       

##  **OUTPUT:**

* Directories `/user/sample1` and `/user/sample2` are created in HDFS.
* File `ex.txt` is successfully uploaded to `/user/sample1`.
* The content of the file is displayed using the `cat` command.
* The file is successfully deleted from HDFS.

       

##  **RESULT:**

Thus, the file management tasks—**adding**, **retrieving**, and **deleting** files in HDFS—were successfully implemented and verified using Hadoop commands.

       

Absolutely! Here's the **well-structured documentation** for your **Experiment No: 3 - Matrix Multiplication using Hadoop MapReduce**, as per your format:

       

# 📘 **EXPERIMENT NO: 3**

### **Title**: *Implementation of Matrix Multiplication with Hadoop MapReduce*



##  **AIM**:

To develop and execute a Hadoop MapReduce program that performs **matrix multiplication** using distributed data processing techniques.

       

##  **ALGORITHM**

###  **Mapper Algorithm (MatrixMapper.java)**

1. Read each line from the input file.
2. Parse the matrix identifier (A or B), row/column indices, and value.
3. If the value is from matrix A (`A[i][j] = v`):

   * Emit `(i,k)` as key and `A,j,v` as value for every column `k` of the result.
4. If the value is from matrix B (`B[i][j] = v`):

   * Emit `(k,j)` as key and `B,i,v` as value for every row `k` of the result.

       

###  **Reducer Algorithm (MatrixReducer.java)**

1. For each key (i,j) representing a cell in the result matrix:
2. Separate incoming values into two groups: from matrix A and matrix B.
3. Store these values in hash maps using their common index `j`.
4. Perform multiplication between corresponding A and B values where index matches.
5. Sum the products to compute the final result for cell `(i,j)`.
6. Output the final result.

       

###  **Driver Algorithm (MatrixMultiplication.java)**

1. Read input and output paths from arguments.
2. Configure Hadoop job:

   * Set job name, Mapper, Reducer, key/value classes.
3. Add input and output paths.
4. Submit job to Hadoop cluster.
5. Exit with appropriate status based on success/failure.

       

##  **PROGRAMS**

###  **MatrixMapper.java**

```java
package matrixmultiplication;

import java.io.IOException;
import org.apache.hadoop.io.*;
import org.apache.hadoop.mapreduce.Mapper;

public class MatrixMapper extends Mapper<LongWritable, Text, Text, Text> {
    private static final int MATRIX_SIZE = 2;

    public void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        String[] tokens = value.toString().split(",");
        String matrixName = tokens[0];
        int i = Integer.parseInt(tokens[1]);
        int j = Integer.parseInt(tokens[2]);
        int v = Integer.parseInt(tokens[3]);

        if (matrixName.equals("A")) {
            for (int k = 0; k < MATRIX_SIZE; k++) {
                context.write(new Text(i + "," + k), new Text("A," + j + "," + v));
            }
        } else if (matrixName.equals("B")) {
            for (int k = 0; k < MATRIX_SIZE; k++) {
                context.write(new Text(k + "," + j), new Text("B," + i + "," + v));
            }
        }
    }
}
```

       

###  **MatrixReducer.java**

```java
package matrixmultiplication;

import java.io.IOException;
import java.util.HashMap;
import org.apache.hadoop.io.*;
import org.apache.hadoop.mapreduce.Reducer;

public class MatrixReducer extends Reducer<Text, Text, Text, IntWritable> {
    public void reduce(Text key, Iterable<Text> values, Context context) throws IOException, InterruptedException {
        HashMap<Integer, Integer> A_map = new HashMap<>();
        HashMap<Integer, Integer> B_map = new HashMap<>();

        for (Text val : values) {
            String[] tokens = val.toString().split(",");
            String matrix = tokens[0];
            int index = Integer.parseInt(tokens[1]);
            int value = Integer.parseInt(tokens[2]);

            if (matrix.equals("A")) {
                A_map.put(index, value);
            } else if (matrix.equals("B")) {
                B_map.put(index, value);
            }
        }

        int result = 0;
        for (int j : A_map.keySet()) {
            if (B_map.containsKey(j)) {
                result += A_map.get(j) * B_map.get(j);
            }
        }

        context.write(key, new IntWritable(result));
    }
}
```

       

### **MatrixMultiplication.java (Driver)**

```java
package matrixmultiplication;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.*;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class MatrixMultiplication {
    public static void main(String[] args) throws Exception {
        if (args.length != 2) {
            System.err.println("Usage: MatrixMultiplication <input path> <output path>");
            System.exit(-1);
        }

        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf, "Matrix Multiplication");

        job.setJarByClass(MatrixMultiplication.class);
        job.setMapperClass(MatrixMapper.class);
        job.setReducerClass(MatrixReducer.class);

        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(Text.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

       

##  **INSTALLATION / IMPLEMENTATION PROCEDURE**

1. **Create Directory Structure**

```bash
mkdir -p ~/matrixmultiplication/src/matrixmultiplication
cd ~/matrixmultiplication/src/matrixmultiplication
```

2. **Create Java Source Files**

```bash
nano MatrixMapper.java
nano MatrixReducer.java
nano MatrixMultiplication.java
```

3. **Create Input File**

```bash
nano input.txt
```

Content:

```
A,0,0,1
A,0,1,2
A,1,0,3
A,1,1,4
B,0,0,5
B,1,0,6
B,0,1,7
B,1,1,8
```

4. **Create Build Directory and Compile**

```bash
mkdir -p ~/matrixmultiplication/build
cd ~/matrixmultiplication
javac -classpath $(hadoop classpath) -d build src/matrixmultiplication/*.java
```

5. **Create JAR**

```bash
cd build
jar -cvf MatrixMultiplication.jar matrixmultiplication/
```

6. **Upload Input to HDFS**

```bash
hadoop fs -mkdir /matrixinput
hadoop fs -put ~/matrixmultiplication/src/matrixmultiplication/input.txt /matrixinput
```

7. **Update Configuration (if required)**
   Edit:

```bash
sudo nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```

Add:

```xml
<property>
    <name>yarn.app.mapreduce.am.env</name>
    <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
</property>
```

(Add similar properties for `map` and `reduce` if needed.)

8. **Start Hadoop Services**

```bash
start-dfs.sh
start-yarn.sh
```

9. **Run Job**

```bash
hadoop jar ~/matrixmultiplication/build/MatrixMultiplication.jar matrixmultiplication.MatrixMultiplication /matrixinput /matrixoutput
```

10. **View Output**

```bash
hadoop fs -cat /matrixoutput/part-r-00000
```

       

## 📤 **OUTPUT:**

```
0,0    17
0,1    23
1,0    39
1,1    53
```

Matrix Multiplication Result for A × B:

```
[ [17, 23],
  [39, 53] ]
```

       

##  **RESULT:**

Thus, a MapReduce program for performing **matrix multiplication** was successfully developed, compiled, executed in Hadoop, and the result was verified using a 2x2 input matrix dataset.




##  **EX. NO: 4**


**TITLE:**
Run a basic WordCount MapReduce program to understand MapReduce Paradigm



### AIM:

To run a basic WordCount MapReduce program to understand the MapReduce Paradigm.



### ALGORITHM:

**Mapper (TokenizerMapper):**

1. Read one line of input text.
2. Split the line into individual words.
3. For each word, emit a key-value pair: (word, 1).

**Reducer (IntSumReducer):**

1. For each unique word, receive the list of values (all 1s).
2. Sum all the values.
3. Output: (word, total count).

**Driver (Main Method):**

1. Configure and set up the Hadoop job.
2. Specify Mapper, Reducer, and job input/output classes.
3. Provide input and output directories in HDFS.
4. Submit and run the job.


       

### JAVA PROGRAM: `WordCount.java`

```java
import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCount {

    public static class TokenizerMapper extends Mapper<Object, Text, Text, IntWritable> {
        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
                word.set(itr.nextToken());
                context.write(word, one);
            }
        }
    }

    public static class IntSumReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
        public void reduce(Text key, Iterable<IntWritable> values, Context context)
                throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values)
                sum += val.get();
            context.write(key, new IntWritable(sum));
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf, "word count");

        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

       

### IMPLEMENTATION PROCEDURE:

**Step 1: Start Hadoop services**

```bash
$ start-all.sh
$ jps
```

**Step 2: Create working directory**

```bash
$ mkdir ~/wordcount
$ cd ~/wordcount
```

**Step 3: Create and save the Java program**

```bash
$ nano WordCount.java
```

(Paste the Java code and save)

**Step 4: Create input file**

```bash
$ nano input.txt
```

**Input contents:**

```
Hadoop is a framework.
Hadoop MapReduce processes Big Data efficiently.
Hadoop is scalable and distributed.
```

**Step 5: Create directory for compiled classes**

```bash
$ mkdir wordcount_classes
```

**Step 6: Compile Java code**

```bash
$ javac -classpath `hadoop classpath` -d wordcount_classes WordCount.java
```

**Step 7: Package into a JAR file**

```bash
$ jar -cvf wordcount.jar -C wordcount_classes/ .
```

**Step 8: Upload input file to HDFS**

```bash
$ hadoop fs -mkdir /wcinput1
$ hadoop fs -put ~/wordcount/input.txt /wcinput1/
$ hadoop fs -ls /wcinput1/
```

**Step 9: Run the WordCount MapReduce job**

```bash
$ hadoop jar wordcount.jar WordCount /wcinput1 /wcoutput1
```

**Step 10: View output**

```bash
$ hadoop fs -ls /wcoutput1
$ hadoop fs -cat /wcoutput1/part-r-00000
```

       

### OUTPUT:

```
Big              1
Data             1
Hadoop           3
MapReduce        1
a                1
and              1
distributed.     1
efficiently.     1
framework.       1
is               2
processes        1
scalable         1
```

       

### RESULT:

Thus, the basic WordCount MapReduce program was implemented successfully and the output was verified.

       


Here is the full, neatly formatted lab record for the experiment **"Implementation of KMeans Clustering using MapReduce"** – suitable for your submission:

---

## **EX. NO: 5**


**TITLE:**
Implementation of KMeans Clustering using MapReduce

---

### AIM:

To implement KMeans Clustering using MapReduce.

---

### ALGORITHM:

#### **Mapper (KMeansSimpleMapper.java):**

1. Read each point from the input, formatted as "x,y".
2. Compare the point to a set of predefined centroid coordinates.
3. Calculate the Euclidean distance from the point to each centroid.
4. Assign the point to the nearest centroid.
5. Emit the centroid as key and the point as value.

#### **Reducer (KMeansSimpleReducer.java):**

1. Receive all points assigned to a specific centroid.
2. Sum up the x and y values of all received points.
3. Calculate the average x and y to determine the new centroid.
4. Output the new centroid.

#### **Driver (KMeansSimpleDriver.java):**

1. Configure the Hadoop job.
2. Set the Mapper, Reducer, and I/O key-value classes.
3. Define input and output directories in HDFS.
4. Launch the job and wait for its completion.

---

### JAVA PROGRAMS:

#### **KMeansSimpleMapper.java**

```java
import java.io.IOException;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

public class KMeansSimpleMapper extends Mapper<Object, Text, Text, Text> {
    public void map(Object key, Text value, Context context) throws IOException, InterruptedException {
        String[] point = value.toString().split(",");
        double x = Double.parseDouble(point[0]);
        double y = Double.parseDouble(point[1]);
        
        double[][] centroids = {{1, 1}, {5, 4}};
        double minDist = Double.MAX_VALUE;
        String nearest = "";

        for (double[] centroid : centroids) {
            double dist = (x - centroid[0]) * (x - centroid[0]) + (y - centroid[1]) * (y - centroid[1]);
            if (dist < minDist) {
                minDist = dist;
                nearest = centroid[0] + "," + centroid[1];
            }
        }

        context.write(new Text(nearest), value);
    }
}
```

#### **KMeansSimpleReducer.java**

```java
import java.io.IOException;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

public class KMeansSimpleReducer extends Reducer<Text, Text, Text, Text> {
    public void reduce(Text key, Iterable<Text> values, Context context) throws IOException, InterruptedException {
        double sumX = 0, sumY = 0;
        int count = 0;

        for (Text val : values) {
            String[] point = val.toString().split(",");
            sumX += Double.parseDouble(point[0]);
            sumY += Double.parseDouble(point[1]);
            count++;
        }

        double newX = sumX / count;
        double newY = sumY / count;

        context.write(null, new Text(newX + "," + newY));
    }
}
```

#### **KMeansSimpleDriver.java**

```java
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class KMeansSimpleDriver {
    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf, "Simple KMeans");

        job.setJarByClass(KMeansSimpleDriver.class);
        job.setMapperClass(KMeansSimpleMapper.class);
        job.setReducerClass(KMeansSimpleReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);

        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

---

### EXECUTION PROCEDURE:

**Step 1: Start Hadoop Services**

```bash
$ start-all.sh
$ jps
```

**Step 2: Create a Working Directory**

```bash
$ mkdir SimpleKMeans
$ cd SimpleKMeans
```

**Step 3: Create Java Files**

```bash
$ nano KMeansSimpleMapper.java
$ nano KMeansSimpleReducer.java
$ nano KMeansSimpleDriver.java
```

(Paste and save the corresponding Java code in each file)

**Step 4: Create Input File**

```bash
$ nano points.txt
```

**Sample Input Data:**

```
1,1
2,1
4,3
5,4
6,5
```

**Step 5: Create Directory for Class Files**

```bash
$ mkdir simple_classes
```

**Step 6: Compile Java Files**

```bash
$ javac -classpath `hadoop classpath` -d simple_classes *.java
```

**Step 7: Create a JAR File**

```bash
$ jar -cvf simple_kmeans.jar -C simple_classes/ .
```

**Step 8: Upload Input File to HDFS**

```bash
$ hadoop fs -mkdir /input
$ hadoop fs -put ~/SimpleKMeans/points.txt /input/
$ hadoop fs -ls /input/
```

**Step 9: Run the MapReduce Job**

```bash
$ hadoop jar ~/SimpleKMeans/simple_kmeans.jar KMeansSimpleDriver /input/points.txt /output
```

**Step 10: View Output**

```bash
$ hadoop fs -ls /output/
$ hadoop fs -cat /output/part-r-00000
```

---

### OUTPUT:

```
1.5,1.0
5.0,4.0
```

These are the new centroids calculated from the input data points after one iteration.

---

### RESULT:

Thus, KMeans Clustering was successfully implemented using the MapReduce programming paradigm and executed in the Hadoop environment.



---

# **Experiment 6 : Installation of Hive along with Practice Examples**

---



## **AIM:**

To install and run Apache Hive and use it to create databases and tables, and perform basic data manipulation.

---

## **ALGORITHM:**

1. **Ensure Prerequisites are Met:**

   * Java is installed.
   * Hadoop is installed and running.

2. **Download and Install Hive:**

   * Download Hive tarball from the official archive.
   * Extract it into the `/usr/local/hive` directory.

3. **Set Environment Variables for Hive:**

   * Add Hive-related paths in the `~/.bashrc` file.
   * Reload the shell configuration using `source ~/.bashrc`.

4. **Start Hadoop Services:**

   * Start all necessary Hadoop daemons using `start-all.sh`.
   * Verify with `jps`.

5. **Create Hive Warehouse Directories in HDFS:**

   * Create directories `/user/hive/warehouse` in HDFS using `hadoop fs -mkdir`.

6. **Initialize Hive Metastore:**

   * Use the command `schematool -dbType derby -initSchema` to initialize the metastore.

7. **Start Hive Shell:**

   * Launch Hive CLI by running `hive`.

8. **Create a Database and Table in Hive:**

   * Use HiveQL to create a database and a table with specified schema.

9. **Prepare and Upload Sample Data:**

   * Create a local CSV file containing employee data.
   * Upload the file to HDFS.

10. **Load Data into Hive Table:**

    * Use the `LOAD DATA` command to insert the CSV data into the Hive table.

11. **Query the Table:**

    * Run a `SELECT * FROM table_name;` query to view the data.

---

## **PROGRAM / COMMANDS:**

### **Step 1: Download Hive**

```bash
wget https://archive.apache.org/dist/hive/hive-3.1.3/apache-hive-3.1.3-bin.tar.gz
```

### **Step 2: Create Hive Directory and Extract**

```bash
sudo mkdir -p /usr/local/hive
sudo tar -xvzf apache-hive-3.1.3-bin.tar.gz -C /usr/local/hive --strip-components=1
```

### **Step 3: Configure Environment Variables**

```bash
nano ~/.bashrc
# Add the following lines at the end:
export HIVE_HOME=/usr/local/hive
export PATH=$PATH:$HIVE_HOME/bin
export HIVE_CONF_DIR=$HIVE_HOME/conf

# Save and apply changes
source ~/.bashrc
```

### **Step 4: Start Hadoop Services**

```bash
start-all.sh
jps  # to confirm all daemons are running
```

### **Step 5: Create Hive Directories in HDFS**

```bash
hadoop fs -mkdir /user
hadoop fs -mkdir /user/hive
hadoop fs -mkdir /user/hive/warehouse
```

### **Step 6: Initialize Hive Metastore**

```bash
schematool -dbType derby -initSchema
```

### **Step 7: Start Hive Shell**

```bash
hive
```

### **Step 8: Create Database and Table in Hive**

```sql
CREATE DATABASE mydb;
USE mydb;

CREATE TABLE employees (
  id INT,
  name STRING,
  age INT,
  city STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE;
```

### **Step 9: Exit Hive Shell**

```bash
# Press Ctrl + D to exit
```

### **Step 10: Create CSV File**

```bash
cat > ~/employees.csv
# Paste the following data and then press Ctrl + D
1,John,30,Chennai
2,Mahesh,25,Madurai
3,Ram,35,Trichy
4,Babu,28,Kanchipuram
```

### **Step 11: Upload File to HDFS**

```bash
hadoop fs -put ~/employees.csv /user/hive/warehouse/
```

### **Step 12: Load Data into Hive Table**

```bash
hive
USE mydb;

LOAD DATA INPATH '/user/hive/warehouse/employees.csv' INTO TABLE employees;
```

### **Step 13: View the Data**

```sql
SELECT * FROM employees;
```

---

## **OUTPUT:**

```
1   John     30   Chennai
2   Mahesh   25   Madurai
3   Ram      35   Trichy
4   Babu     28   Kanchipuram
```

---

## **RESULT:**

Thus, Apache Hive was successfully installed, configured, and used to create a database and table. Sample data was loaded and queried successfully.


Yes, based on your previous formatting, here is the **clearly structured version** of the **Experiment No: 7a – Installation of HBase along with practice examples**, including **Date, Aim, Algorithm, Program, Output, and Result**, with **no emojis and clean format**.

---

# **EX. NO: 7a**
## **Installation of HBase along with Practice Examples**

### **AIM:**

To install and run HBase and use it to create and manage databases and tables.

---

### **ALGORITHM:**

1. Ensure the system has the following prerequisites:

   * Java JDK 8 or 11
   * SSH (optional)
   * Hadoop (optional, for distributed mode)

2. Download HBase binary distribution from the official Apache archive.

3. Create an installation directory and extract HBase into it.

4. Configure environment variables by updating the `~/.bashrc` file.

5. Create required HBase data directories.

6. Edit the `hbase-site.xml` file to configure HBase in standalone mode.

7. Start HBase using the startup script.

8. Open the HBase shell to interact with the database.

9. Create a table with a column family.

10. Insert records into the created table using `put` commands.

11. Retrieve data using `get` and `scan` commands.

12. Exit the HBase shell after completing operations.

---

### **PROGRAM / COMMANDS:**

#### **Step 1: Download HBase**

```bash
wget https://archive.apache.org/dist/hbase/2.5.6/hbase-2.5.6-bin.tar.gz
```

#### **Step 2: Create Installation Directory**

```bash
sudo mkdir -p /usr/local/hbase
```

#### **Step 3: Extract HBase**

```bash
sudo tar -xvzf hbase-2.5.6-bin.tar.gz -C /usr/local/hbase --strip-components=1
```

#### **Step 4: Set Environment Variables**

```bash
nano ~/.bashrc
# Add these lines at the bottom:
export HBASE_HOME=/usr/local/hbase
export PATH=$PATH:$HBASE_HOME/bin
# Save and apply changes
source ~/.bashrc
```

#### **Step 5: Create Data Directories**

```bash
sudo mkdir -p /usr/local/hbase/hbase_data
sudo mkdir -p /usr/local/hbase/zookeeper
```

#### **Step 6: Configure `hbase-site.xml`**

```bash
sudo nano /usr/local/hbase/conf/hbase-site.xml
```

**Replace contents with:**

```xml
<?xml version="1.0"?>
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>false</value>
  </property>
  <property>
    <name>hbase.tmp.dir</name>
    <value>./tmp</value>
  </property>
  <property>
    <name>hbase.unsafe.stream.capability.enforce</name>
    <value>false</value>
  </property>
  <property>
    <name>hbase.rootdir</name>
    <value>file:///usr/local/hbase/hbase_data</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/usr/local/hbase/zookeeper</value>
  </property>
</configuration>
```

#### **Step 7: Start HBase**

```bash
sudo JAVA_HOME=$JAVA_HOME $HBASE_HOME/bin/start-hbase.sh
```

#### **Step 8: Start HBase Shell**

```bash
$HBASE_HOME/bin/hbase shell
```

#### **Step 9: Create Table**

```hbase
create 'students', 'info'
```

#### **Step 10: List Tables**

```hbase
list
```

#### **Step 11: Insert Data**

```hbase
put 'students', 'row1', 'info:name', 'Arun'
put 'students', 'row1', 'info:age', '21'
put 'students', 'row2', 'info:name', 'Balan'
put 'students', 'row2', 'info:age', '22'
```

#### **Step 12: Retrieve Data**

```hbase
get 'students', 'row1'
scan 'students'
```

#### **Step 13: Exit Shell**

```hbase
exit
```

---

### **OUTPUT:**

```
ROW       COLUMN+CELL
row1      column=info:age, timestamp=..., value=21
row1      column=info:name, timestamp=..., value=Arun
row2      column=info:age, timestamp=..., value=22
row2      column=info:name, timestamp=..., value=Balan
```

---

### **RESULT:**

Thus, HBase was successfully installed, and a table was created, data was inserted and retrieved using HBase shell commands.



Certainly. Below is the **clearly formatted version** of **Experiment No: 7b – Installation of Apache Thrift**, including **DATE, AIM, ALGORITHM, PROGRAM/COMMANDS, OUTPUT, and RESULT**, exactly as per your required structure and without any emojis.

---

# **EX. NO: 7b**

## **Installation of Apache Thrift**

---

### **DATE:**

*Write the date of execution here*

---

### **AIM:**

To install and run Apache Thrift on a Linux system.

---

### **ALGORITHM:**

1. Update the system's package list using `apt update`.

2. Install necessary development tools and libraries required for building Thrift.

3. Install Java Development Kit (JDK) and Apache Ant for Java-based components.

4. Clone the Apache Thrift source code from GitHub.

5. Install additional Python build dependencies required for the build process.

6. Build the source code by running `bootstrap.sh`, `configure`, and `make`.

7. Install the Thrift compiler using the package manager.

8. Verify that Thrift is installed by checking its version.

---

### **PROGRAM / COMMANDS:**

#### **Step 1: Install Prerequisites**

```bash
sudo apt update
sudo apt install -y git automake bison flex g++ libevent-dev libtool make pkg-config libssl-dev
```

#### **Step 2: Install Java Libraries**

```bash
sudo apt install -y openjdk-8-jdk ant
```

#### **Step 3: Clone Apache Thrift Repository**

```bash
git clone https://github.com/apache/thrift.git
```

#### **Step 4: Install Python Build Dependencies**

```bash
sudo apt install python3-distutils
sudo apt install python3-setuptools
```

#### **Step 5: Build and Install Thrift**

```bash
cd thrift
./bootstrap.sh
./configure
make -j$(nproc)
```

#### **Step 6: Install Thrift Compiler**

```bash
sudo apt install thrift-compiler
```

#### **Step 7: Verify the Installation**

```bash
thrift --version
```



### **OUTPUT:**

```
Thrift version 0.13.0
```



### **RESULT:**

Thus, Apache Thrift was successfully installed and verified using the version command.

Sure! Here’s the clearly formatted **EX. NO: 8** for **Practice Importing and Exporting Data from Various Databases** as per your requested format (DATE, AIM, ALGORITHM, PROGRAM/COMMANDS, OUTPUT, RESULT):

---

# **EX. NO: 8**

## **Practice Importing and Exporting Data from Various Databases**



### **AIM:**

To practice importing and exporting data between different databases, such as MySQL and Hive.

---

### **ALGORITHM:**

#### A) MySQL Operations:

1. Update the package repository and install MySQL server.
2. Secure the MySQL installation by setting root password and other security settings.
3. Login to MySQL and set the root password authentication method.
4. Create a new database and a table for storing employee data.
5. Insert sample employee records into the table.
6. Query and verify the inserted data.

#### B) Hive Operations:

1. Ensure Hadoop and Hive are installed and running.
2. Start Hive CLI.
3. Create and use a new database.
4. Create a Hive table to store employee data.
5. Prepare employee data file in CSV format and upload it to HDFS.
6. Load data from HDFS into the Hive table.
7. Query and verify the data in Hive table.

---

### **PROGRAM / COMMANDS:**

#### A) MySQL Operations:

```bash
sudo apt update
sudo apt install mysql-server

sudo mysql_secure_installation

sudo mysql
```

Inside MySQL prompt:

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'new_password';
EXIT;
```

Re-login with password:

```bash
mysql -u root -p
# Enter password: new_password
```

Inside MySQL prompt:

```sql
CREATE DATABASE new_company;
USE new_company;

CREATE TABLE employees (
  id INT PRIMARY KEY,
  name VARCHAR(50),
  age INT,
  city VARCHAR(50)
);

INSERT INTO employees (id, name, age, city) VALUES
(1, 'John Doe', 30, 'New York'),
(2, 'Jane Smith', 25, 'Los Angeles'),
(3, 'Michael Johnson', 35, 'Chicago');

SELECT * FROM employees;
```

---

#### B) Hive Operations:

Start Hive CLI:

```bash
hive
```

Inside Hive shell:

```sql
CREATE DATABASE mydb;
USE mydb;

CREATE TABLE employees (
  id INT,
  name STRING,
  age INT,
  city STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE;
```

Upload employee data CSV to HDFS:

```bash
hadoop dfs -mkdir -p /user/hive/warehouse/input
hadoop dfs -put employees.csv /user/hive/warehouse/input/
```

Load data into Hive table:

```sql
LOAD DATA INPATH '/user/hive/warehouse/input/employees.csv' INTO TABLE employees;
SELECT * FROM employees;
```

---

### **OUTPUT:**

#### MySQL Output:

```
+----+----------------+-----+-------------+
| id | name           | age | city        |
+----+----------------+-----+-------------+
|  1 | John Doe       |  30 | New York    |
|  2 | Jane Smith     |  25 | Los Angeles |
|  3 | Michael Johnson|  35 | Chicago     |
+----+----------------+-----+-------------+
```

#### Hive Output:

```
+----+-------+-----+-------------+
| id | name  | age | city        |
+----+-------+-----+-------------+
|  1 | John  |  30 | Chennai     |
|  2 | Mahesh|  25 | Madurai     |
|  3 | Ram   |  35 | Trichy      |
|  4 | Babu  |  28 | Kanchipuram |
+----+-------+-----+-------------+
```

---

### **RESULT:**

Thus, data was successfully imported and exported between MySQL and Hive databases, demonstrating interoperability and basic database operations.

