# stunredis

No-configuration connections for redis-cli to Redis TLS services 

## Use

To run stunredis.sh:

* Download the files.
* `chmod u+x stunredis.sh` to make it executable.
* Get a connection string for your Redis database.
* Run `./stunredis.sh <connection string>`

## Notes on lechain.pem

The lechain.pem file is a sample of the verification chain for Lets Encrypt. Do not use for production if you are concerned about correctness.

You can be create your own version of lechain.pem by downloading and combining the contents of the [Let's Encrypt X3 Cross-signed PEM file](https://letsencrypt.org/certs/lets-encrypt-x3-cross-signed.pem.txt) and the [IdenTrust Root for X3](https://www.identrust.com/certificates/trustid/root-download-x3.html). (The latter link's content will need to be wrapped in the same -----BEGIN CERTIFICATE-----/-----END CERTIFICATE----- lines that the first links content is wrapped in). Consult lechain.pem for an example of how it should look.

For simplicity, it is located in the same directory as the stunredis.sh script.



