## 🐘 Big Data Environment Setup: Hadoop + Hive + MySQL + ODBC + Power BI Integration

This repository provides a complete setup guide and configuration files to deploy a working **Big Data environment** on **Ubuntu** (WSL or native). It includes **Hadoop**, **Hive**, **MySQL** (as Hive Metastore), and **ODBC** configuration for seamless **Power BI** connectivity.

---

## 💻 Components

| Component            | Version      | Role                                    |
| :------------------- | :----------- | :-------------------------------------- |
| **Hadoop**           | 3.3.6        | Distributed Storage & Processing        |
| **Hive**             | 4.0.1        | Data Warehouse on Hadoop                |
| **Java**             | 11 (OpenJDK) | Runtime Environment                     |
| **MySQL**            | 8.x          | Hive Metastore Database                 |
| **ODBC Connector**   | -            | Connectivity Layer for Power BI         |
| **Power BI Desktop** | -            | Data Visualization Tool (Local Machine) |

---

## 📂 Repository Folder Structure ⚙️

The `https://github.com/merajsiddieque/PowerBI_ODBC_Hive_Hadoop/tree/main/Config_Files` directory holds all the essential pre-configured XML files for this single-node Hadoop and Hive setup.

```
Config_Files/
├── Hadoop_Config_Files/
│   ├── core-site.xml         
│   ├── hdfs-site.xml
│   ├── mapred-site.xml
│   └── yarn-site.xml
└── Hive_Config_Files/
    └── hive-site.xml
```

Copy the configuration files:

```bash
sudo cp ~https://github.com/merajsiddieque/PowerBI_ODBC_Hive_Hadoop/tree/main/Config_Files/Hadoop_Config_Files/*.xml /usr/local/hadoop/etc/hadoop/
sudo cp ~https://github.com/merajsiddieque/PowerBI_ODBC_Hive_Hadoop/tree/main/Config_Files/Hive_Config_Files/hive-site.xml /usr/local/hive/conf/
```

---

## 🛠️ Prerequisites & Installation

### 1. Install Dependencies

Run the following command to install necessary packages:

```bash
sudo apt update
sudo apt install openjdk-11-jdk wget tar ssh rsync mysql-server unixodbc libmyodbc -y
```

---

## ☕ Java, Hadoop, and Hive Configuration (`.bashrc`)

Add the following environment variables to your `~/.bashrc` file:

```bash
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
```

Apply the changes:

```bash
source ~/.bashrc
```

---

## ⚙️ Hadoop Setup

### 1. Download and Extract

```bash
cd /usr/local
sudo wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
sudo tar -xzf hadoop-3.3.6.tar.gz
sudo mv hadoop-3.3.6 hadoop
```

### 2. Copy Configuration Files

```bash
sudo cp ~https://github.com/merajsiddieque/PowerBI_ODBC_Hive_Hadoop/tree/main/Config_Files/Hadoop_Config_Files/*.xml /usr/local/hadoop/etc/hadoop/
```

### 3. Format NameNode

> ⚠️ Only run this the first time you set up Hadoop.

```bash
hdfs namenode -format
```
### 4. Create NameNode and DataNode Directory
```bash
sudo mkdir -p /home/$USER/hadoop_data/hdfs/namenode
sudo mkdir -p /home/$USER/hadoop_data/hdfs/datanode
sudo chown -R $USER:$USER /home/$USER/hadoop_data

```

### 5. Start Hadoop

```bash
start-dfs.sh
start-yarn.sh
```

---

## 🐝 Hive Setup

### 1. Download and Extract

```bash
cd /usr/local
sudo wget https://downloads.apache.org/hive/hive-4.0.1/apache-hive-4.0.1-bin.tar.gz
sudo tar -xzf apache-hive-4.0.1-bin.tar.gz
sudo mv apache-hive-4.0.1-bin hive
```

### 2. Configure Hive

```bash
sudo cp ~https://github.com/merajsiddieque/PowerBI_ODBC_Hive_Hadoop/tree/main/Config_Files/Hive_Config_Files/hive-site.xml /usr/local/hive/conf/
```

### 3. Set Hadoop Path in `hive-env.sh`

Edit the Hive environment file (`/usr/local/hive/conf/hive-env.sh`) and add/update:

```bash
export HADOOP_HOME=/usr/local/hadoop
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```

---

## 💾 MySQL Metastore Setup

### 1. Start & Secure MySQL

```bash
sudo service mysql start
sudo mysql_secure_installation
```

### 2. Create Metastore Database and User

```bash
sudo mysql -u root -p
```

Inside the MySQL shell, execute:

```sql
CREATE DATABASE metastore;
CREATE USER 'hiveuser'@'localhost' IDENTIFIED BY 'hivepassword';
GRANT ALL PRIVILEGES ON metastore.* TO 'hiveuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## 🔗 ODBC Setup for Power BI

### 1. Install MySQL ODBC Driver

```bash
sudo apt install unixodbc libmyodbc -y
```

### 2. Configure ODBC Files

Configure the DSN (Data Source Name) in `/etc/odbc.ini` and the driver in `/etc/odbcinst.ini`.

**Example `/etc/odbc.ini` (for HiveServer2):**

```ini
[HiveDSN]
Driver=/usr/local/hive/lib/libhiveodbc.so  # Path to your Hive ODBC driver
HOST=localhost
PORT=10000
Schema=default
HiveServerType=2
```

### 3. Test Connection

You can test the ODBC connection using:

```bash
isql -v HiveDSN hiveuser hivepassword
```

---

## 📊 Power BI Connection

1. Open **Power BI Desktop** (on your Windows machine).
2. Go to **Get Data → ODBC**.
3. Choose your configured DSN (e.g., `HiveDSN`).
4. Authenticate and load your Hive tables for visualization.

---

## ⚠️ Troubleshooting

| Issue                        | Solution                                                                                                         |
| :--------------------------- | :--------------------------------------------------------------------------------------------------------------- |
| RPC failed / Git push issues | Use a stable Internet connection / increase Git buffer size (`git config --global http.postBuffer 524288000`).   |
| Hive Metastore errors        | Check the JDBC URL in `hive-site.xml` and ensure MySQL privileges are correct.                                   |
| NameNode format issues       | Stop Hadoop, remove old NameNode data (`rm -rf /usr/local/hadoop/hdfs/data`), and rerun `hdfs namenode -format`. |

---

## ⬇️ Download Links

| Component    | Official Link                                                                                                                                                                | Backup Mirror (Faster)                                                                                                                                     |
| :----------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hadoop 3.3.6 | [https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz](https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz) | [https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz](https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz) |
| Hive 4.0.1   | [https://downloads.apache.org/hive/hive-4.0.1/apache-hive-4.0.1-bin.tar.gz](https://downloads.apache.org/hive/hive-4.0.1/apache-hive-4.0.1-bin.tar.gz)                       | [https://dlcdn.apache.org/hive/hive-4.0.1/apache-hive-4.0.1-bin.tar.gz](https://dlcdn.apache.org/hive/hive-4.0.1/apache-hive-4.0.1-bin.tar.gz)             |

---

## 📜 License

This repository is licensed under the **Apache 2.0 License**. Feel free to fork and adapt for your data-engineering projects.

---

## ✍️ Author

**Meraj Alam**
GitHub: [@merajsiddieque](https://github.com/merajsiddieque)
