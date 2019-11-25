# Unifi Controller

Inspired by https://github.com/jacobalberty/unifi-docker.

## Installation

- Start:

```sh
docker-compose up -d
```

- Create and prepare a certificate with mkcert for `localhost` and the current wired IP (currently `10.0.0.19`):

```sh
rm certs/*
mkcert -cert-file certs/localhost.pem -key-file certs/localhost.key localhost 10.0.0.19
docker-compose exec unifi-controller \
  openssl pkcs12 -export -passout pass:aircontrolenterprise \
    -in /home/certs/localhost.pem \
    -inkey /home/certs/localhost.key \
    -out /home/certs/chain.pem -name unifi
```

- Remove old certificates (if necessary):

```sh
docker-compose exec unifi-controller \
  keytool -delete -alias unifi -keystore data/keystore -deststorepass aircontrolenterprise
```

- Import the certificate (if asked for a password, enter `aircontrolenterprise`):

```sh
docker-compose exec unifi-controller \
  keytool -trustcacerts -importkeystore \
    -deststorepass aircontrolenterprise \
    -destkeypass aircontrolenterprise \
    -destkeystore data/keystore \
    -srckeystore /home/certs/chain.pem \
    -srcstoretype PKCS12 \
    -srcstorepass aircontrolenterprise \
    -alias unifi
```

- Restart once:

```sh
docker-compose restart
```

# Usage

- Browse to the management interface: http://grisu.local:8080
- Change the controller hostname/IP to the current wired IP and enable `Override inform host with controller hostname/IP`.
- Configure the rest!
