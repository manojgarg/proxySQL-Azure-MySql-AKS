# ProxySQL on AKS - Azure Database for MySQL single server

The main cause of poor application performance is inefficient interactions in the connection between the database and the application. Connection management, including connection pooling, retry logic, etc., can significantly improve  database performance. 
 ProxySQL, a high-performance MySQL proxy, provides out-of-the-box connection management for Azure Database for MySQL. Using ProxySQL has several advantages, including: 
 1.Intelligent load balancing between different databases 
 2.Transparent read / write partitioning that allows you to determine if a database instance is running. This allows read traffic to be redirected accordingly. 
 3. Ready-to-use connection pool 
 4. Built-in retry logic

## Setup User

1. Login
mysql -hmydemomaster.mysql.database.azure.com -umydemo@mydemomaster -p​

2. Create user
CREATE USER 'mydemouser'@'%' IDENTIFIED BY 'secretpassword';

3. Grant permission & flush
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'mydemouser'@'%' WITH GRANT OPTION;​

FLUSH PRIVILEGES;​

## Setup proxySQL on AKS (this assumes that a AKS cluster & Master/replica DBs are provisioned and available)

1. Get credentials
az aks get-credentials --name aks-cluster-name  --resource-group "mydemo"​

2. Deploy ConfigMap
kubectl create configmap proxysql-configmap --from-file=proxysql.cnf​

3. Deploy proxySQL
kubectl apply -f ./proxysqlaks.yml​

4. Get External IP
kubectl get service proxysql -w​
kubectl get pods,services -o wide ​

5. Connect & verify
mysql -h[External_IP] -umydemouser -psecretpassword -P3306​



