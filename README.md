# How to use GPG

### **GpG configuration**

DO THIS BEFORE GENERATING PRIVATE KEYS

`~/.gnupg/gpg.conf`

```conf
# This configuration is based on the tutorial below, it allows for a robust setup
# https://blog.eleven-labs.com/en/openpgp-almost-perfect-key-pair-part-1

# Get rid of the copyright notice
no-greeting

# Disable inclusion of the version string in ASCII armored output
no-emit-version
# Do not write comment packets
no-comments
# Export the smallest key possible
# This removes all signatures except the most recent self-signature on each user ID
export-options export-minimal

# Display long key IDs
keyid-format 0xlong
# List all keys (or the specified ones) along with their fingerprints
with-fingerprint

# Display the calculated validity of user IDs during key listings
list-options show-uid-validity
verify-options show-uid-validity show-keyserver-urls

# Select the strongest cipher
personal-cipher-preferences AES256
# Select the strongest digest
personal-digest-preferences SHA512
# This preference list is used for new keys and becomes the default for "setpref" in the edit menu
default-preference-list SHA512 SHA384 SHA256 RIPEMD160 AES256 TWOFISH BLOWFISH ZLIB BZIP2 ZIP Uncompressed

# Use the strongest cipher algorithm
cipher-algo AES256
# Use the strongest digest algorithm
digest-algo SHA512
# Message digest algorithm used when signing a key
cert-digest-algo SHA512
# Use RFC-1950 ZLIB compression
compress-algo ZLIB

# Disable weak algorithm
disable-cipher-algo 3DES
# Treat the specified digest algorithm as weak
weak-digest SHA1

# The cipher algorithm for symmetric encryption for symmetric encryption with a passphrase
s2k-cipher-algo AES256
# The digest algorithm used to mangle the passphrases for symmetric encryption
s2k-digest-algo SHA512
# Selects how passphrases for symmetric encryption are mangled
s2k-mode 3
# Specify how many times the passphrases mangling for symmetric encryption is repeated
s2k-count 65011712
```

### **Generate primary key**

Generate key
```sh
gpg --quick-gen-key 'Example User <example@user.com> (optional comment)' ed25519 cert never
```

### **Generate subkeys**

```sh
gpg --quick-addkey key_fingerprint ed25519 sign 1y
```
```sh
gpg --quick-addkey key_fingerprint ed25519 auth 1y
```
```sh
gpg --quick-addkey key_fingerprint rsa4096 encr 1y
```

### **Generate revoke certificate**

key_fingerprint_short = last 8 digits of your key fingerprint

```sh
gpg --output key_fingerprint_short.rev.asc --gen-revoke example@user.com
```

### **Export keys**

(private) primary key
```sh
gpg --export-secret-keys --armor key_fingerprint_short > key_fingerprint_short.sec.asc
```

(private) subkeys
```sh
gpg --export-secret-subkeys --armor key_fingerprint_short > key_fingerprint_short.ssb.asc
```

public key
```sh
gpg --export --armor key_fingerprint_short > key_fingerprint_short.pub.asc
```

### **Remove keys**

Remove private key
```sh
gpg --delete-secret-key key_fingerprint_short
```

Remove public key
```sh
gpg --delete-key key_fingerprint_short
```

### **Import keys**

```sh
gpg --import key_fingerprint_short.ssb.asc
```

### **Use SSH**

Generate the keygrip
```sh
gpg --list-keys --with-keygrip
```

Copy the keygrip to `~/.gnupg/sshcontrol`

