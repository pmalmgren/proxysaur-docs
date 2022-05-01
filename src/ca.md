# Certificate Authority Setup

Before we're able to intercept HTTPS requests, we need a certificate authority which is trusted by the system making the requests. When HTTP requests come in, proxysaur uses the certificate authority to generate a certificate signing request (or CSR) and generate certificates for the domain. And because the certificates are signed by a trusted certificate authority, browsers and other HTTPS clients will see them as valid without displaying warnings.

## Security Concerns

Because the risk of an attacker getting ahold of the root certificate poses a grave security threat, proxysaur-generated root certificate authorities expire after 1 day.

In the future, using something like [Linux keyrings](https://man7.org/linux/man-pages/man7/keyrings.7.html) or [Apple's Secure Enclave](https://developer.apple.com/documentation/security/certificate_key_and_trust_services/keys/storing_keys_in_the_secure_enclave) may be implemented so that keys aren't stored in plaintext on disk.

## Generating a Certificate Authority

Proxysaur has a built in subcommand called `generate-ca`. It uses openssl to generate a key and certificate file. If you don't give a path to the subcommand, it will place them in the platform-specific data directory, for example in `$XDG_DATA_DIRS` on Linux.

It will output the location of the CA to `stdout`, and if you run it multiple times it won't overwrite anything in the existing directory. If you pass in `-f` it will clear out all of the certificates.

```bash
$ proxysaur generate-ca
/home/me/.local/share/proxysaur
$ ls -lah /home/me/.local/share/proxysaur
total 28K
drwxrwxr-x  2 me me 4.0K Apr 28 11:29 .
drwxrwxr-x 45 me me 4.0K Apr 27 08:53 ..
-rw-rw-r--  1 me me  204 Apr 28 11:29 config
-rwxrwxrwx  1 me me  326 Apr 28 11:29 generateca.sh
-rw-rw-r--  1 me me 1.3K Apr 28 11:29 myca.crt
-rw-------  1 me me 1.7K Apr 28 11:29 myca.key
-rw-rw-r--  1 me me 1.3K Apr 28 11:29 myca.pem
```

## Trusting the Root Certificate in your Browser

### Firefox

1. Go to `about:preferences` in the URL bar
2. Search for "certificates" and click on "View Certificates"
3. Click on "Authorities" and then "Import"
4. Select the root CA

### Chrome

1. Go to `chrome://settings/certificates` in the URL bar
2. Click on "Authorities" and then "Import"
3. Select the root CA

## Trusting the Root Certificate on your OS

### Linux

On Linux, you can manually trust the root certificate by copying it to your `/usr/local/share/ca-certificates/extra` directory:

```bash
$ CA_LOC=$(proxysaur generate-ca)
$ sudo cp $CA_LOC/myca.crt /usr/local/share/ca-certificates/extra
$ sudo update-ca-certificates
```

### macOS

On macOS, you can either trust the root CA by double-clicking it and adding it to your login keychain. You'll have to select "Always Trust" after adding it to the keychain.

You can also try with the following command:

```bash
$ CA_LOC=$(proxysaur generate-ca)
$ security add-trusted-cert -d -r trustRoot -k $HOME/Library/Keychains/login.keychain $CA_LOC/myca.crt
```

### iOS

You'll have to send the certificate to yourself via AirDrop, and then follow along with [the Apple Support docs](https://support.apple.com/en-us/HT204477).
