# Service auth provider

[![Build Status](https://travis-ci.org/hmcts/service-auth-provider-app.svg?branch=master)](https://travis-ci.org/hmcts/service-auth-provider-app)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/0cb10a161dc24d0092470cda7c304c87)](https://app.codacy.com/app/HMCTS/service-auth-provider-app)
[![codecov](https://codecov.io/gh/hmcts/service-auth-provider-app/branch/master/graph/badge.svg)](https://codecov.io/gh/hmcts/service-auth-provider-app)

This microservice is used to authenticate services across HMCTS.

## Getting Started

### Prerequisites
- [JDK 8](https://java.com)
 
### Building
To build the project execute the following command:
```bash
$ ./gradlew build
```

### Configuration

In order to setup Service Auth Provider to work with a client service, you need to do the following:

* In the Azure Key Vault named `s2s-{environment}` add the service's secret used for generating OTPs (one-time passwords).
This has to be done in each environment the service is going to be deployed to. Service Auth Provider will use that secret
for validating OTPs. It has to be a BASE32-encoded sequence of ten random bytes (16 characters after encoding). By convention,
the Azure Key Vault secret's name should follow this format: `microservicekey-{service-name}`. [Here's](#generating-secret) how to generate it.
* Add the client service to `local.microservice_key_names` map in [main.tf](infrastructure/main.tf). The key has to be
the service name (as in HTTP requests) and the value must be the name of the Azure Key Vault secret created in the previous step.
For example, service named `test_service` would be configured like this:

```
  microservice_key_names = {
    ...
    "TEST_SERVICE" = "microservicekey-test-service"
    ...
  }
```

#### <a name="generating-secret"></a>Generating the microservice secret

Here's a sample Java snippet to generate a microservice secret:

```
byte[] bytes = new byte[10];
SecureRandom.getInstanceStrong().nextBytes(bytes);
String secret = new Base32().encodeAsString(bytes);
```

Sample Python code to generate that secret:

```
import os
import base64
base64.b32encode(os.urandom(10))
```

### Running
To run the app execute:
```bash
$ ./gradlew bootRun
```
You can also run the app on docker.  
To build:
```bash
$ docker-compose build
```
And to run:
```bash
$ docker-compose up
```
Dockerized app comes with preconfigured sample service. See [docker-compose.yml](docker-compose.yml) for details.

## Documentation
API documentation is provided with Swagger.  
Json spec is available under standard `/v2/api-docs` route.

Flow diagram can be found [here](docs/design.md)

## Developing

### Unit tests
To run all unit tests execute the following command:
```bash
$ ./gradlew test
```

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE.md) file for details.
