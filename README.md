# speck-linux

the SPECK kernel module ported from 4.17.7 to modern more kernel releases and as a module.

## Why?

SPECK was removed from linux ~4.20. Turns out someone down the line would want to use it though.

I needed fast encryption for an Intel E2140. AES likely would've been slow and prove itself a bottle neck. I ported this after having concerns with Adiantum, then after learning about it my suspicions have faded with Adiantum, and now this is a pointless repository.

### Benchmarks

#### Genuine Intel(R) CPU            2140  @ 1.60GHz

|Algorithm|Key|Encryption|Decryption|
|---------|---|----------|----------|
|xchacha12,aes-adiantum|256b|202.5 MiB/s|202.4 MiB/s|
|speck128-xts|512b|130.9 MiB/s|108.3 MiB/s|
|serpent-xts|512b|101.0 MiB/s|105.6 MiB/s|
|twofish-xts|512b|92.2 MiB/s|92.6 MiB/s|
|aes-xts|512b|67.8 MiB/s|66.0 MiB/s|

#### AMD Ryzen 5 5500

|Algorithm|Key|Encryption|Decryption|
|---------|---|----------|----------|
|aes-xts|512b|3798.0 MiB/s|3801.2 MiB/s|
|xchacha12,aes-adiantum|256b|1680.2 MiB/s|1738.7 MiB/s|
|serpent-xts|512b|742.9 MiB/s|728.3 MiB/s|
|speck128-xts|512b|453.7 MiB/s|390.6 MiB/s|
|twofish-xts|512b|411.6 MiB/s|410.2 MiB/s|

Sorted from quickest to slowest algorithm, numbers gathered through
```
#!/bin/sh
/sbin/cryptsetup benchmark --cipher xchacha12,aes-adiantum --key-size 256
/sbin/cryptsetup benchmark --cipher speck128-xts-plain64 --key-size 512
/sbin/cryptsetup benchmark --cipher serpent-xts-plain64 --key-size 512
/sbin/cryptsetup benchmark --cipher twofish-xts-plain64 --key-size 512
/sbin/cryptsetup benchmark --cipher aes-xts-plain64 --key-size 512
```

Surprisingly, Serpent is pretty close to SPECK, and Adiantum is quicker, maybe this is all for nothing. Maybe this is why SPECK was removed from the Linux kernel. Adiantum is quicker and instills more confidence than a cipher with a round function consisting of 5 lines.

### Installation

```console
# git clone https://github.com/containedreality/speck-linux /usr/src/speck-1.0
# dkms add -m speck -v 1.0
# dkms build -m speck -v 1.0
# dkms install -m speck -v 1.0
```
