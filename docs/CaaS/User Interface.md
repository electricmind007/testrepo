# User Interface

The primary function of the User Interface is to serve as an interface between the user
and the the [Intent Router](./Intent%20Router.md). It will take an utterance from the user,
ask the Intent Router to classify it, and then display the results to the user. If necessary,
further services as identified by the results returned from the Intent Router will be invoked.

## Design

The user interface is located in `caas-ui` in the source repository. This service is written in
Node.js, uses the [Express](https://expressjs.com/) framework for defining the web server, and uses
[AngularJS](https://angularjs.org/) for the front-end web pages. It uses the standard build tools
for the CEDP. As it uses Thrift to communicate with the Intent Router and other analytic modules,
it uses the Thrift IDL in `/thrift` to generate the interfaces that it will need to use.

## Implementation Details

The User Interface is a simple web server that takes an utterance from the user, calls the intent router,
gets a list of intents back from the intent router, and then displays the returned intents that have a
confidence that exceed a certain threshold. If the intent refers to another analytic module, then that
module will be queried to get a response for that intent. The server will only accept connections over
HTTPS and users will need to authenticate with the IBM SSO before being allowed access. Eventually, the
data that the user is authorized to see will be constrained.

### Directives

In order to support visualizations based on data being returned from NLQA modules, the user interface supports
the concept of directives. These directives are produced by the NLQA module and are consumed by the user interface
to determine how the data being returned should be rendered. The directive should appear on a line by itself and be
prefixed with a percent sign. The token that represents the directive is used to determine which rendering approach
should be used. Presently the single directive that is understood is `%md` which instructs the user interface to
render the response from the NLQA module as Markdown.

In the future, the directives can be expanded to include the ability to render CSV data as a table (`%table`),
chart (`%chart`), or graph (`%graph`), render relationship data as a relationship network force graph (`%force-graph`)
or table, or render SVG (`%svg`) data as a graphic. In addition, there is no reason that each of these directives
could not support interaction with the user.

The directive system is meant to be extensible, therefore as co-creators need new rendering techniques the
engine can be enhanced with new directives so that everyone using this user interface can benefit.

### Production Mode versus Development Mode

The fact that the server only accepts HTTPS connections and that the user must authenticate through
the SSO has implications for development and testing. A developer or tester cannot simply start the
service and start interacting with it; they will need to configure server certificates and keys and
register their system with the IBM SSO before they would be able to do anything. This is not
acceptable, so the server can be run in development mode to eliminate these issues.

A developer or tester can start the server in development mode by defining the `CAAS_UI_DEV` environment
variable before starting the server. If that variable is not defined, then the server will start in
production mode.

There are a number of differences when running in development versus production mode.

- In development mode, the `NODE_ENV` environment variable is set to `development`, otherwise it is
set to `production`. Various Node.js modules use that variable to control how they operate, including
the Express framework.
- Development mode does not require configuration (but it can be configured if so desired),
but production mode does (see [Configuration](#configuration)). If the server is misconfigured in
production mode, it is locked down and simply returns a page stating that it needs to be configured.
- In development mode, you can access the server either over HTTP on port 8080 or over HTTPS on port 8443.
In production mode, you can only access the server over HTTPS on port 8443.
- In production mode, you must authenticate through the SSO before you can access the server.

### Docker Image

As with all of the CaaS services, the user interface is packaged and run on the platform as a Docker image.
The [Dockerfile](/caas-ui/Dockerfile) contains the pertinent information for creating the Docker container.

## Configuration

A number of things need to be configured before the server can be properly run. When running in development
mode, a lot of these things can be ignored, and suitable defaults will be applied. When running in production
mode, most of these need to be given as default values are either unknown or inappropriate.

First, the server recognizes the `INTENT_ROUTER_HOST` and `INTENT_ROUTER_PORT` environment variables. These
tell the server what host and port the intent router is running on, so it knows who to talk to in order
to process utterances. If either of these variables is not defined, they will take on a default value.
For `INTENT_ROUTER_HOST`, the default value is `localhost`. For `INTENT_ROUTER_PORT`, the default value
is `9998`.

Second, the server looks for a configuration directory that contains the main configuration file plus all of
the related files that the configuration file refers to. The configuration directory is defined to be the `config`
directory in the main directory of the server. If running the server locally, this can be accomplished by
creating a link to the physical configuration directory.

```bash
# Create a link to the directory that contains the configuration for the server
cd cognitive-data-platform/caas-ui
ln -s <path to real configuration directory> config
```

If running the server as a docker image, then the configuration directory on the host needs to be mapped to
the `config` directory within the image when the image is started.

```bash
# Start docker image mapping configuration directory into the image
docker run ... -v <path to real configuration directory>:/app/config ... caas-ui
```

The main configuration file within the `config` directory is called `config.json`
Its contents should be a single JSON object, with the fields within the object defining
the various variables that can be configured.

- **hostname** - This gives the fully-qualified hostname of the machine that the server is running on. This is
necessary because the SSO configuration needs to know the hostname in order for the SSO registration to take place.
In development mode, this can be omitted, and will take the default value of `10.0.32.8`. In production mode,
this must be given or else it is considered a configuration error. This value must be defined before doing
the SSO registration.
- **port** - This gives the unsecure port that the server will listen on. If omitted, it will take the default
value of `8080`. This is ignored in production mode as the server does not listen to the unsecure port in that case.
- **sessionLength** - This gives the the amount of time (in seconds) a user session will be considered valid before redirecting
the user to login again. If not given, it will take the default value of `43200` (which is 12 hours). This only takes
effect if SSO authentication is configured.
- **https** - This is an object that gives values controlling the secure web server, which are detailed below.
- **https.port** - This gives the secure port that the server will listen on. If omitted, it will take the default
value of `8443`.
- **https.externalPort** - This gives the secure port that is exposed to the world. When running in a Kubernetes pod, the actual secure port is hidden behind a service. This is needed because SSO registration needs to know the URL to talk to the server, and for that it needs to know the port that is exposed. If omitted, it will be the same as `https.port`.
- **https.key** - This gives the private key of the secure server. Its value is the name of the file in the `config`
directory that contains the PEM formatted private key. This private key can optionally be encrypyted. If either of
the `https.key` or `https.cert` values is not given, then a key and certificate pair built into the server will
be used if in development mode. The built in certificate identifies the server as `10.0.32.8`, which is appropriate
if using the default `hostname`. In production mode, missing either of these values is considered to be a
configuration error.
- **https.passphrase** - If the private key is encrypted, this gives the password phrase that is used to decrypt the
private key. If the private key is not encrypted, then this option is optional and ignored.
- **https.cert** - This gives the server certificate that is associated with the private key, and optionally the
intermediate certificates in its certificate chain. Each certificate is given as the name of a file in the `config`
directory that contains the PEM formatted certificate. If a single certificate is given, this can be a single
value giving the name of the file that contains the certificate. If multiple certificates are given (because of
the intermediate certificates in the chain), this must be list of values, each giving the name of the file that
contains the certificate. The certificate associated with the private key **must** be given first, followed by the
intermediate certificates.
- **SSO** - This is an object that gives values controlling the SSO configuration for the server, each of which is
detailed below. If any value within this object is missing, SSO authentication cannot be performed, and in
production mode is considered to be an configuration error.
- **SSO.key** - This gives the private key that identifies the server to the SSO infrastructure. Its value is the name
of the file in the `config` directory that contains the PEM formatted private key. Unlike `https.key`, the private
key cannot be encrypted. This value must be defined before doing the SSO registration.
- **SSO.cert** - This gives the certificate that is associated with `SSO.key`. Its value is the name of the file
in the `config` directory that contains the PEM formatted certificate. Unlike `https.cert`, only a single certificate
can be given here, so the value should be a simple string, not a list of strings. This value must be defined
before doing the SSO registration.
- **SSO.IDP** - This is an object that gives values identifying the Identity Provider; these values are obtained
during SSO registration.
- **SSO.IDP.loginURL** - This gives the login URL as returned from the SSO registration.
- **SSO.IDP.cert** - This value **is** the encoded certificate identifying the Identity Provider, and is obtained
through the SSO registration. Unlike the other certificates defined in the configuration, this is **not** the name
of a file, mainly because the SSO registration provides this value as a simple (albeit long) string.

Here is an example of a complete `config.json` file.

```json
{
    "hostname": "csx00814.canlab.ibm.com",
    "sessionLength": 43200,
    "https": {
        "key": "csx00814.key.enc.pem",
        "passphrase": "Big scary secret",
        "cert": ["csx00814.cert.pem", "ibm_intermediate_root_ca.pem"]
    },
    "SSO": {
        "key": "spKey.pem",
        "cert": "spCert.pem",
        "IDP": {
            "loginURL": "https://w3id.alpha.sso.ibm.com/auth/sps/samlidp/saml20/logininitial?RequestBinding=HTTPPost&PartnerId=https://csx00814.canlab.ibm.com:8443/metadata.xml&NameIdFormat=email&Target=https://csx00814.canlab.ibm.com:8443",
            "cert": "MIIDdzCCAl+gAwIBAgIENmZ8gDANBgkqhkiG9w0BAQsFADBsMQswCQYDVQQGEwJVUzELMAkGA1UECBMCTlkxDzANBgNVBAcTBkFybW9uazEQMA4GA1UEChMHaWJtLmNvbTEMMAoGA1UECxMDQ0lTMR8wHQYDVQQDExZ3M2lkLmFscGhhLnNzby5pYm0uY29tMB4XDTE1MTEyMDE3NDAyM1oXDTE4MTExOTE3NDAyM1owbDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAk5ZMQ8wDQYDVQQHEwZBcm1vbmsxEDAOBgNVBAoTB2libS5jb20xDDAKBgNVBAsTA0NJUzEfMB0GA1UEAxMWdzNpZC5hbHBoYS5zc28uaWJtLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAIMdVEidEhUKdfUFwR5+bOk12uJHtiE2D+6LyeMJOdLInWbNFrl3WP9TL5fjeF9oNACQcuQy7Ll2rt+UqUXbU3pVvT7bzoyfeJWAPjzRqFP2f/U40sBbftVHnbC8mRcwD7VyJqjB3IIfV78mYfrJUM6b3hYW2GGU9dzkNpKc1S7dDmbpkOwFqn4PgRVJNnS3eZq7T46hS6gjnfD1MAsDTPszkwMWB4jMQuhvswt3+fgcGBmKlQFVXidDRyg7aA8oYq5Mj07307ulywzt/oyXljQCai/9M1wg3MpuQ+Qb0iJ+W1oiBRpRUATbYuUFC3fPV886LmH7UOd6MxrWrGjuuS8CAwEAAaMhMB8wHQYDVR0OBBYEFAtr3Gj757xspsi5XIZI7E9041j+MA0GCSqGSIb3DQEBCwUAA4IBAQBGM7PALAwVEsqS8egOHMOGuKTSSPZVCdlxi8xpP4f5feoaK0f7gqiD2ujsi7Oy7IuZ4USAh61tjSmdayHDTwrfpih+vaMts5lz4O+Z5Ox/2itYggDOPp3ajohVfGn0EupWnRVy3RotiWQ9WvHeBTCitmEICLnSi4gYmfILeJKBCo5zrM1E2OD2ESdtTLwgQyx9xxL78Zf/yF0l8e4o1ttwuxx/e/IghC1OQcX2mIlH/68NABTYD3jLdNUK65zrbMhRPhWpqXEdOOmKdQjtcZPpihVawDSHGjVhqPaYam7/aAMJ5jJHuy2qZCM8ZgY05KVmlcdO/S4rCgeXLS8uMhpE"
        }
    }
}
```

### Obtaining a Server Certificate

If running a test server or in development mode where the default hostname is not appropriate, you can simply
generate a self-signed certificate that identifies the sever.

```bash
cd <real configuration directory>

# Generate the private key for the server, using 2048 bits
openssl genrsa -out serverKey.pem 2048

# Generate the CSR. When prompted, you must give the fully
#  qualified name of the server host in the CN field
openssl req -new -key serverKey.pem -out serverCsr.pem

# Generate the self-signed certificate
openssl x509 -req -days 9999 -in serverCsr.pem -signkey serverKey.pem -out serverCert.pem
rm serverCsr.pem

# Update config.json with https.key and https.cert pointing
#  at serverKey.pem and serverCert.pem
```

If you need something other than a self-signed certificate, then you should generate a
certificate for the server using the [IBM Internal Certificate Authority](http://w3.ibm.com/tools/ibmcapki).

### SSO Registration

When running in production mode, the server **must** be configured to do SSO authentication. This is accomplished
by registering the server with the [SSO Self-Service Provisioner](https://w3.ibm.com/tools/sso). Note that the
following steps were done when registering against the staging Identity Provider. When registering for production
purposes, there may be more steps that need to be completed.

1. As a first step, the server must be partially configured in order to provide the information necessary for the
SSO registration. This means that a server key and certificate have been generated (feel free to use the ones
that were generated for the secure server) and the `hostname`, `SSO.key`, and `SSO.cert` fields in the configuration file
are set appropriately.
2. Next, the Service Provider metadata needs to be generated. This can be done in several ways.
  * Start the server. It will not be completely configured, so there will be a configuration error and the server will be locked down. However, you will be able to hit the `https://my.server.name:8443/metadata.xml` endpoint; this will generate the Service Provider metadata that you will need in the SSO registration. Save the returned data as `metadata.xml`. At this point, you can bring the server down until you finish configuration.
  * You can run Node directly in the checked out and built repository to generate the Service Provider metadata. Ensure that your configuration directory exists at `caas-ui/config`, and then execute the following in the `caas-ui` directory: `node generateSSOmetadata.js >metadata.xml`.
  * If your `caas-ui` docker image has been built, you can generate the metadata by executing the following command:
`docker run -it --rm -v /path/to/config:/app/config --entrypoint node caas-ui generateSSOmetadata.js > metadata.xml`
3. Go to the [SSO Self-Service Provisioner](https://w3.ibm.com/tools/sso), click on `Register a w3id application`, and
agree to the terms and conditions.
4. Fill in the required fields with appropriate values and hit `Next`. The home page should be your secure server, ie.
`https://my.server.name:8443/`. Make sure you account for Kubernetes service port mapping!
5. Select the `SAML 2.0` for the `w3id Protocol Selection` and the hit `Next`. This is
necessary because the server is written using SAML.
6. The next page allows you to select the identity provider. For test deployments, you should select
`w3id Staging`. When deploying for production, you should select `w3id Production`. When done, hit `Next`.
7. The next page allows you to download the Identity Provider metadata. Click the download link and save the downloaded
file. Take note of the downloaded file name as you will need that later on. For the `Application Template`, pick `Default`;
this provides the server with the necessary information during the authentication process. For the `Target Application URL`,
enter your home page, ie. `https://my.server.name:8443`. Hit `Next`.
8. The next page allows you to upload your Service Provider metadata, which you generated in step 2. Select
`Upload a Service Provider Metadata File`, click `Browse`, navigate to where you saved the generated metadata file,
and select it. Hit `Next`.
9. The next page provides the `SAML Information For SP Setup`. Take note of the value for `IDP-Initiated Login URL`. Update
your configuration file, giving that value to the `SSO.IDP.loginURL` field. Hit `Next`.
10. The next page provides a summary of what you entered. If everything looks good, hit `Save and continue`. Your SSO
registration is now in a pending state.
11. Complete your configuration by copying the Identity Provider certificate to your configuration. Open the downloaded
Identity Provider metadata from step 7. Find the `<md:KeyDescriptor use="signing">` element. Underneath it is a
`<X509Certificate>` element. Copy the contents of that element and set it as the value in your configuration for the
`SSO.IDP.cert` field.

At this point, your SSO registration is complete (you just need to wait for it to be activated). Also, your configuration
should now be complete, so you should be able to start the server in either production or development mode as you see fit.

## Building

The user interface can be built using the standard build tools and targets from the root of the `cognitive-data-platform` repository.

```bash
# Build the user interface (downloads dependencies and generates Thrift bindings)
./gradlew caas-ui:build

# Build a docker image
./gradlew caas-ui:dockerBuild
```
## Running

The user interface requires the catalog and intent router services to be running.  The catalog must have
at least one module loaded, and the intent router must be trained on the content of the catalog.
As a quick start guide, the following steps can be performed:

```bash
./gradlew assemble
./gradlew --no-daemon catalog-service:run &
export CONFIG_FILE=/vagrant/intent-router-service/config/intentrouter.properties
export CATALOG_SERVICE_PORT=9999
./gradlew --no-daemon intent-router-service:run &
cli/bin/cedp --host localhost configure
cli/bin/cedp register cognitive-modules/FAQ-Module.yml
cli/bin/cedp register cognitive-modules/Sink-Module.yml
cli/bin/cedp train
```

The above steps will perform a local build, start the services, and register and train the module.
You can start the UI as given below. The UI currently runs on ports 8080/8443. If running from within
a Vagrant image, the browser should be pointed at http://10.0.32.8:8080. The `configure` command tells
the CLI what host to talk to for the various services; this gets persisted to `$HOME/.cedp/cliConfig.yml`,
so it only needs to be done once (unless you wish to change the host).

The user interface can be run in a variety of ways when doing development. The easiest way is to invoke it directly via Node.
You can run the following commands from the `caas-ui` directory in the source tree.

```bash
# Start the user interface in production mode (configuration needs to be done)
node index.js
# Alternate way
npm start

# Start the user interface in development mode (configuration optional)
CAAS_UI_DEV= node index.js

# Start the user interface in development with extended debugging
CAAS_UI_DEV= DEBUG="*,-saml2" node index.js
```

Alternatively, the docker container for the service can be run using the command below. The docker container is also
started as part of the `caas-start-all.sh` script, but this version runs the docker container interactively.

```bash
docker run -it -p 8080:8080 -p 8443:8443 -e INTENT_ROUTER_HOST=10.0.32.8 -e CAAS_UI_DEV= -v <config dir on host>:/app/config caas-ui
```
