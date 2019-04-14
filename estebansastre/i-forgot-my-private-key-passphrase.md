# Oh no, I forgot my private key's passphrase

## Introduction

After being quite disconnected from the technology world for months and pretty much limiting my interactions to my working schedule, I decided to go back into setting up my personal workstation with the minimal memory footprint and using as little privacy-invading technologies as possible. 

One step of this process meant setting up again my GPG keys to be used while signing my emails. Thus, I retrieve my private key from an offline medium I keep safe and check whether the corresponding generated public key matches the one I have broadcasted publicly. (like here in [https://estebansastre.com/about](https://estebansastre.com/about))


```sh
.ssh λ ssh-keygen -y -e -f secret-key.asc
Enter passphrase:
```

I try every single password combination I can think of and nothing. **F*ck**. So, what do I do now?


## Steps to take

### 1. Identify where is my public key available.

When a user creates a new key pair, they can choose to publish the public key to what is called a [key server](https://en.wikipedia.org/wiki/Key_server_%28cryptographic%29), which is one or a group of computers on the  internet in charge of making them available for other users to send encrypted messages. 

By default, the **GPG** application uploads them to `keys.gnupg.net`. This doesn't mean that a key is in a single computer. Servers normally take part in pools and synchronize their databases, say for example the [SKS](https://sks-keyservers.net/) or the [PGP Global Directory](https://keyserver.pgp.com/vkd/GetWelcomeScreen.event)


```sh
.ssh λ gpg --search-keys esteban.s.f0@gmail.com
gpg: searching for "esteban.s.f0@gmail.com" from hkp server keys.gnupg.net
(1)     Esteban <esteban.s.f0@gmail.com>
          4096 bit RSA key 1E117998, created: 2018-05-07, expires: 2023-05-06
```

Taking a look at the first page of google of available keyservers, I see my key has been replicated into pretty much all of them.

* [https://pgp.key-server.io](https://pgp.key-server.io/pks/lookup?search=esteban.s.f0%40gmail.com&fingerprint=on&op=vindex)
* [https://pgp.mit.edu](https://pgp.mit.edu/pks/lookup?search=esteban.s.f0%40gmail.com&op=index)
* [http://keys.gnupg.net](http://keys.gnupg.net/pks/lookup?search=esteban.s.f0%40gmail.com&fingerprint=on&op=index) (The default gpg's one)

### 2. Create revocation certificate for the keys.

Since I forgot my private key's passphrase, it is safe to assume it is equivalent to it being compromised and must no longer be taken into account for sending messages with the public key or verify any signature. The next step to take is generating a revocation certificate and upload it to the keyserver, then until it synchronizes and invalidates the other ones:

```sh
.ssh λ gpg --output revocation-certificate.asc --gen-revoke 1E117998

sec  4096R/1E117998 2018-05-07 Esteban <esteban.s.f0@gmail.com>

(..etc..)

You need a passphrase to unlock the secret key for
user: "Esteban <esteban.s.f0@gmail.com>"
4096-bit RSA key, ID 1E117998, created 2018-05-07

Enter passphrase: 

```

**F*ck**, again. So, I need to know my passphrase in order to create the revocation certificate in order to revoke the private key I had initially forgotten the passphrase to. What are the options I have left?

### 3. Generate new key pair

```sh
.ssh λ gpg2 --full-gen-key
```

And this time do remember the passphrase!

### 4. Generate revocation certificate

```sh
.ssh λ gpg2 --output revocation-certificate.asc --gen-revoke 38DF1841                                 │
```

### 5. Export private key

```sh
.ssh λ gpg2 --armor --export-secret-keys 38DF1841 > myprivkey_38DF1841.priv
```

The `--armor` option allows the private key to be encoded in plain text and thus it can be sent over an email for example.


### 6. Save both private key & revocation certificate

Now I have created the new key pair, added to my keyring and successfully exported the private key along with the revocation certificate I need to store them somewhere safe and accessible offline.


### 7. Upload again my new public key to public keyserver

```sh
.ssh λ gpg2 --send-keys 38DF1841
gpg: sending key 38DF1841 to hkp://keys.gnupg.net
```


### 8. Change my public key my own website: *estebansastre.com*.


## Conclusions

PGP is a very valuable tool to encrypt your communications, but it comes with the never ending hassle of key management. I have now four takeaways for the next time:

* Every time you generate a new key pair, automatically generate the revocation certificate with it just in case.
* Always keep your private key & revocation certificate in a safe place.
* Don't forget your passphrase...
* Use a password manager once and for all. I've heard great things about [pass](https://www.passwordstore.org/).



Have fun!


## References

* [https://debian-administration.org/article/450/Generating_a_revocation_certificate_with_gpg](https://debian-administration.org/article/450/Generating_a_revocation_certificate_with_gpg)
* [https://superuser.com/questions/227991/where-to-upload-pgp-public-key-are-keyservers-still-surviving#228033](https://superuser.com/questions/227991/where-to-upload-pgp-public-key-are-keyservers-still-surviving#228033)
* [https://blog.eleven-labs.com/en/openpgp-almost-perfect-key-pair-part-1/](https://blog.eleven-labs.com/en/openpgp-almost-perfect-key-pair-part-1/)
