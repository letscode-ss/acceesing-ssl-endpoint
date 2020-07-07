# Accessing SSL endpoint !

If JAVA program wanted to access any https endpoint you need you need to import its client and cacerts else you will get below exception.

```
Caused by: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target

at sun.security.validator.PKIXValidator.doBuild(PKIXValidator.java:397)

at sun.security.validator.PKIXValidator.engineValidate(PKIXValidator.java:302)

at sun.security.validator.Validator.validate(Validator.java:262)
```

## How to fix it

### Download cacert from openssl
```
echo -n | openssl s_client -connect ENDPOINTHOST:PORTNUMBER \

| sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > ca-cert.pem
```
#### Import certs in java cacerts
```
keytool -import -trustcacerts -keystore ${JAVA_HOME}/jre/lib/security/cacerts -storepass  <pass> -alias nexusIQ -import -file ca-cert.pem
```
OR 
#### Create CA and pass it in the command.
```
keytool -import -keystore cacerts -file ca-cert.pem --alias cacerts
java -jar <some.jar> -Djavax.net.ssl.trustStore=cacerts -Djavax.net.ssl.keytrustPassword=<passsword>
```

### Same thing in linux

#### Use below command to import it 
```
$ echo -n | openssl s_client -connect ENDPOINTHOST:PORTNUMBER \

| sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > ca-cert.pem

$ cp -r ca-cert.pem /etc/pki/ca-trust/source/anchors/

$ update-ca-trust
```
