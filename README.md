This is a tool to clone X509 certificate chains.

Call it with a host:port that will present an SSL certificate chain, and
Apostille will retrieve the chain, and output a key, and a chain of 
certificates that matches the certs that were retrieved. 

Obviously, we are not breaking RSA/DSA/EC here, so the PrivateKey will be 
freshly generated, along with its PublicKey, and an X509Certificate[] 
chain that matches the source chain in every respect EXCEPT the key values.

If you provide a keystore and passwords, all private keys and certificates 
generated will be stored in the keystore, as well as being output in PEM 
form on stdout. This will include the keys for all the intermediate 
certificates, which would not otherwise be output or saved.

Automatically generated "parents" of the chain will be named by their CN.

To run it, install [Maven](https://maven.apache.org/download.cgi), package it with

	mvn package

Then run either:

	java -jar target/apostille-1.0-SNAPSHOT.jar example.com:443 > example.com.key+chain

Or


	java -jar target/apostille-1.0-SNAPSHOT.jar example.com:443 keystore.jks password password > example.com.key+chain

Alternatively, you can provide a file containing the certificate chain, which
will be cloned. This supports the case where the certificate is not directly reachable
via a socket connection, or is not TLS.

Provide a certificate chain similar to what is generated by:

	openssl s_client -connect example.com:443 -showcerts < /dev/null > example.com.pem

NB: The certificate chain must either contain a complete chain right up to 
the last certificate authority, or the certificate authority must be available
in the Java trust store, or the provided keystore. There is no special alias required
for the CA certificate in the keystore, all trusted certificates are checked for a
matching CN. Note that if the CA certificate is installed using an alias of its CN, it
will be overwritten with the cloned CA certificate and key.

Then clone it using:

	java -jar target/apostille-1.0-SNAPSHOT.jar example.com.pem > example.com.key+chain

Or

	java -jar target/apostille-1.0-SNAPSHOT.jar example.com.pem keystore password password > example.com.key+chain
