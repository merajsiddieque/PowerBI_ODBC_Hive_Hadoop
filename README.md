Big Data Environment Setup
Hadoop + Hive + MySQL + ODBC + Power BI Integration
This repository provides a complete setup guide and configuration files to deploy a working Big Data environment on Ubuntu (WSL or native). It includes Hadoop, Hive, MySQL (as Hive Metastore), and ODBC configuration for Power BI connectivity.

Components


Hadoop 3.3.6


Hive 4.0.1


Java 11 (OpenJDK)


MySQL 8.x (Metastore Database)


ODBC Connector (for Power BI)


Power BI Desktop for data visualization



Folder Structure
Config_Files/
├── Hadoop_Config_Files/
│   ├── core-site.xml    ← (see file in repo)  
│   ├── hdfs-site.xml  
│   ├── mapred-site.xml  
│   └── yarn-site.xml  
└── Hive_Config_Files/
    └── hive-site.xml

Each of these XML files is pre‐configured for a single node Hadoop + Hive setup on Ubuntu.

Prerequisites
Install Dependencies
sudo apt update
sudo apt install openjdk-11-jdk wget tar ssh rsync mysql-server unixodbc libmyodbc -y


Java Configuration (.bashrc)
Add the following lines to your ~/.bashrc:
# ---------------------------------------------
# JAVA ENVIRONMENT
# ---------------------------------------------
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH

# ---------------------------------------------
# HADOOP ENVIRONMENT
# ---------------------------------------------
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export HADOOP_YARN_HOME=$HADOOP_HOME
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

# ---------------------------------------------
# HIVE ENVIRONMENT
# ---------------------------------------------
export HIVE_HOME=/usr/local/hive
export PATH=$PATH:$HIVE_HOME/bin

# ---------------------------------------------
# PIG ENVIRONMENT (optional)
# ---------------------------------------------
export PIG_HOME=/usr/local/pig
export PATH=$PATH:$PIG_HOME/bin

Then apply:
source ~/.bashrc


Hadoop Setup
Download Hadoop
cd /usr/local
sudo wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
sudo tar -xzf hadoop-3.3.6.tar.gz
sudo mv hadoop-3.3.6 hadoop

Copy Config Files
sudo cp ~/your‐repo/Config_Files/Hadoop_Config_Files/*.xml /usr/local/hadoop/etc/hadoop/

Format NameNode
hdfs namenode -format

Start Hadoop
start-dfs.sh
start-yarn.sh


Hive Setup
Download Hive
cd /usr/local
sudo wget https://downloads.apache.org/hive/hive-4.0.1/apache-hive-4.0.1-bin.tar.gz
sudo tar -xzf apache-hive-4.0.1-bin.tar.gz
sudo mv apache-hive-4.0.1-bin hive

Configure Hive
sudo cp ~/your‐repo/Config_Files/Hive_Config_Files/hive-site.xml /usr/local/hive/conf/

Set Hadoop Path in Hive
Edit hive-env.sh in Hive's conf directory:
export HADOOP_HOME=/usr/local/hadoop
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64


MySQL Metastore Setup
Start & Secure MySQL
sudo service mysql start
sudo mysql_secure_installation

Create Hive Metastore Database
sudo mysql -u root -p

Inside MySQL shell:
CREATE DATABASE metastore;
CREATE USER 'hiveuser'@'localhost' IDENTIFIED BY 'hivepassword';
GRANT ALL PRIVILEGES ON metastore.* TO 'hiveuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;


ODBC Setup for Power BI
Install MySQL ODBC Driver
sudo apt install unixodbc libmyodbc -y

Configure /etc/odbc.ini and /etc/odbcinst.ini
Example /etc/odbc.ini:
[HiveDSN]
Driver=/usr/local/hive/lib/libhiveodbc.so
HOST=localhost
PORT=10000
Schema=default
HiveServerType=2

Test Connection
isql -v HiveDSN hiveuser hivepassword


Power BI Connection


Open Power BI Desktop (Windows)


Go to Get Data → ODBC


Choose your DSN (HiveDSN)


Load your Hive tables for visualization



Troubleshooting
IssueSolutionRPC failed / Git push issuesUse a stable Internet connection / increase Git buffer size.Hive Metastore errorsCheck JDBC URL in hive-site.xml and MySQL user privileges.NameNode format issuesRemove old NameNode data (/usr/local/hadoop/hdfs/data) then re‐format.

Download Links (Official + Backup Mirrors)
ComponentOfficialBackup Mirror (Faster)Hadoop 3.3.6https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gzhttps://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gzHive 4.0.1https://downloads.apache.org/hive/hive-4.0.1/apache-hive-4.0.1-bin.tar.gzhttps://dlcdn.apache.org/hive/hive-4.0.1/apache-hive-4.0.1-bin.tar.gz

License
This repository is licensed under the Apache 2.0 License.
Feel free to fork and adapt for your data‐engineering projects.

Author
Meraj Alam
GitHub: @merajsiddieque

Would you like me to include sample MySQL metastore JDBC configuration inside hive-site.xml (with comments for beginners)? I can append that section to the README automatically.
