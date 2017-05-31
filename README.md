# Beeline (HDP distribution)

This image contains beeline that can be used to test remote connection to Hive  which is secured by Knox. 

## Download certificate

You can download the certificate with the follwoing commands:
```
openssl s_client -connect ${GATEWAY_ADDRESS}:8443 -showcerts </dev/null | openssl x509 -outform PEM > gateway.pem

#Import it to gateway.jks file
keytool -import -alias gateway-identity -file gateway.pem -keystore gateway.jks -storepass ${GATEWAY_JKS_PASSWORD}
```

## Examples
```
export GATEWAY_ADDRESS=34.249.149.39
export GATEWAY_PORT=8443
export GATEWAY_JKS_PASSWORD=admin123
openssl s_client -servername ${GATEWAY_ADDRESS} -connect  ${GATEWAY_ADDRESS}:${GATEWAY_PORT} -showcerts </dev/null | openssl x509 -outform PEM > gateway.pem
keytool -import -alias gateway-identity -file gateway.pem -keystore gateway.jks -storepass ${GATEWAY_JKS_PASSWORD}

Owner: CN=34.249.149.39, OU=Test, O=Hadoop, L=Test, ST=Test, C=US
Issuer: CN=34.249.149.39, OU=Test, O=Hadoop, L=Test, ST=Test, C=US
Serial number: deaf73452b09b163
Valid from: Wed Jan 25 09:21:00 CET 2017 until: Thu Jan 25 09:21:00 CET 2018
Certificate fingerprints:
	 MD5:  1B:27:F0:5F:3D:0C:DA:4B:F9:93:3C:77:B1:61:39:DB
	 SHA1: 81:1F:8A:46:E7:58:5D:4A:AC:5B:A9:CF:24:B2:0F:01:04:7B:4A:FF
	 SHA256: 81:24:21:DF:0D:78:71:FC:3F:BC:6B:D3:31:16:00:F9:B5:E6:6F:DC:25:D1:44:99:FB:42:B1:D2:E9:96:FE:DE
	 Signature algorithm name: SHA1withRSA
	 Version: 3
Trust this certificate? [no]:  yes
Certificate was added to keystore

```

## Usage
Make sure that the trust store (gateway.jks) genetared with the above keytool command is located in the directory from where you are starting the container:

```
docker run -ti -v $(pwd):/cert akanto/beeline -u "jdbc:hive2://${GATEWAY_ADDRESS}:${GATEWAY_PORT}/;ssl=true;sslTrustStore=/cert/gateway.jks;trustStorePassword=${GATEWAY_JKS_PASSWORD};transportMode=http;httpPath=gateway/hdc/hive" -d org.apache.hive.jdbc.HiveDriver -n <LDAP user> -p <LDAP Password>
```

## Examples
```
export GATEWAY_ADDRESS=34.249.149.39
export GATEWAY_JKS_PASSWORD=admin123
# Execute the show databases SQL
docker run -ti -v $(pwd):/cert akanto/beeline -u "jdbc:hive2://${GATEWAY_ADDRESS}:8443/;ssl=true;sslTrustStore=/cert/gateway.jks;trustStorePassword=${GATEWAY_JKS_PASSWORD};transportMode=http;httpPath=gateway/hdc/hive" -d org.apache.hive.jdbc.HiveDriver -n admin -p admin -e "show databases;"

# Open an interactive beeline to execute multiple SQL commands
docker run -ti -v $(pwd):/cert akanto/beeline -u "jdbc:hive2://${GATEWAY_ADDRESS}:8443/;ssl=true;sslTrustStore=/cert/gateway.jks;trustStorePassword=${GATEWAY_JKS_PASSWORD};transportMode=http;httpPath=gateway/hdc/hive" -d org.apache.hive.jdbc.HiveDriver -n admin -p admin

#create table hello(name string);
#insert into table hello values('hdcloud');
```