# IOTA Identity Provider

Bridging IOTA's self-sovereign identities to existing "Web 2.0" OAuth solutions.

<img src="docs/login-with-iota-button.png" alt="Login With IOTA" style="height: 64px;"/>

> NOTE: This plugin has NOT been audited or tested in production environments and should only used in non-critical environments until further notice!

## TL;DR
* This repo contains a plugin for the battle-tested [Keycloak](https://www.keycloak.org) _Open Source Identity and Access Management_
* It adds a custom endpoint where IOTA Identity credentials (DID) can be posted to
* The plugin tries to verify your _Verifiable Credential_ with the Tangle and hands over the containing user claims to Keycloak's native user management
* From that point on, every communication is **standard-compliant OAuth / OpenID Connect**
* Clients (such as webshops, sites or other consumers) can follow standard protocols and do not need to be aware of DIDs or IOTA Identity
* **very easy to implement for shops and websites**

> This solution is **not decentralized by design**! It acts as a bridge between two protocols!

## Features
* **Login to any web app** with your IOTA Credentials through any SSI wallet
  * very easy for clients to implement the "Login with IOTA" button and specify an Identity Provider with this plugin enabled
* **Ease-of-use**: no need to remember passwords (passwordless login)
* **Security**: underlying standard-compliant protocol and implementation (OAuth, Keycloak)
* **Privacy**: Plugin is "transparent", user data is only persisted during a user wants to be logged in

### Keywords
**DID**, **SSI**, **OAuth**, **OpenID Connect**, **OIDC**, **IOTA**, **Identity**, **web3**

## Architecture & Diagrams

_--architecture--_

### Components
- Keycloak plugin (plugins are called Service Provider Interfaces "SPI" in Keycloak)
- "sidecar": simple Node.js REST wrapper around `identity.rs` (used for DID resolving, connection to Tangle)
  - _Note: can be removed if credential verification can be performed in Java_
- SSI wallet: [https://github.com/cambriota/identity-cli-wallet]()
- Demo Client app: [https://auth.cambriota.dev/demo/]()

[Read more about the different components here.](./docs/COMPONENTS.md)

## For website owners (clients)

Offer "Login with IOTA" on your website!

> CAUTION: This application has not been audited, it should ONLY be used in non-production environments!

You can run your own Keycloak instance with your own config.
You can also register your existing application with the deployed Identity Provider at [https://auth.cambriota.dev]()
_(still in development)_.

## Usage
You need a DID document published to the Tangle.
You also need to be able to create and sign Verifiable Credentials and Presentations.

You can use [this CLI wallet](https://github.com/cambriota/identity-cli-wallet) to create your DID and Credentials.

Navigate to [https://auth.cambriota.dev/demo/]() to try it out!

## Development & Contribution

### Run

You can find a running instance at [https://auth.cambriota.dev/demo/]()

[Read about running your own instance.]() _--link--_

### Install

#### Prerequisites
* Java 11
* Docker, docker-compose

```
./gradlew jar
```

Then copy the jar to `$KEYCLOAK_HOME/standalone/deployments/` and touch a file in the same directory `keycloak-iota-spi-0.1.0.jar.dodeploy`.

### Dev notes
* [Export an existing realm from Keycloak](scripts/export-realm.sh)

### Request
[Example request](docs/example-request.http)

### Debugging
Run `scripts/run-local.sh`, then attach a debugger to `localhost:8787`.

### TODOs
* [ ] add scheduled task to anonymize/wipe user after given time (anonymize: hash email, overwrite all other fields. on next login: hash email again, if already exists: populate fields again)
* [ ] replace manual "Continue" button click action with automatic forwarding (detect valid session in Keycloak)
* [ ] improve styling of Login page
* [ ] add github actions (security scan, code quality, etc.)
* [ ] add application profile "prod" to build.sh (gradlew, docker)
* [ ] run Keycloak embedded in Spring Boot application / Quarkus / native image?
* [ ] remove unnecessary artifacts (such as `src/main/docker`)
* [ ] create web service that issues VCs for successful email validation. Request those VCs in this plugin to upgrade a user to `email_verified: true`.
* [ ] add some integration tests