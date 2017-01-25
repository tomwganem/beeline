# Beeline (HDP distribution)

This image contains beeline that can be used to test remote connection to Hive  which is secured by Knox. 

## Usage
Make sure that the trust store (gateway.jks) is copied into that directory from where you are starting the container:

```
docker run -ti -v $(pwd):/cert akanto/beeline -u "jdbc:hive2://<ip address>:8443/;ssl=true;sslTrustStore=/cert/gateway.jks;trustStorePassword=<password of trust store>;transportMode=http;httpPath=gateway/hdc/hive" -d org.apache.hive.jdbc.HiveDriver -n <LDAP user> -p <LDAP Password>
```

## Examples
```
# Execute the show databases SQL
docker run -ti -v $(pwd):/cert akanto/beeline -u "jdbc:hive2://34.249.149.39.eu-west-1.compute.amazonaws.com:8443/;ssl=true;sslTrustStore=/cert/gateway.jks;trustStorePassword=admin;transportMode=http;httpPath=gateway/hdc/hive" -d org.apache.hive.jdbc.HiveDriver -n admin -p admin -e "show databases;"

# Open an interactive beeline to execute multiple SQL commands
docker run -ti -v $(pwd):/cert akanto/beeline -u "jdbc:hive2://ec2-34-249-149-39.eu-west-1.compute.amazonaws.com:8443/;ssl=true;sslTrustStore=/cert/gateway.jks;trustStorePassword=admin;transportMode=http;httpPath=gateway/hdc/hive" -d org.apache.hive.jdbc.HiveDriver -n admin -p admin
```