# speck-linux

the SPECK kernel module ported from 4.17.7 to modern more kernel releases and as a module.

## Why?

SPECK was removed from linux ~4.20. Turns out someone down the line would want to use it though.

I needed fast encryption for an Intel E2140. AES likely would've been slow and prove itself a bottle neck. I ported this after having concerns with Adiantum, then after learning about it my suspicions have faded with Adiantum, and now this is a pointless repository.

### Benchmarks

#### Intel E2140

|Algorithm|Key|Encryption|Decryption|
|---------|---|----------|----------|
|xchacha12,aes-adiantum|256b|200.6 MiB/s|200.9 MiB/s|
|speck128-xts|512b|131.0 MiB/s|108.5 MiB/s|
|serpent-xts|512b|100.9 MiB/s|105.7 MiB/s|
|twofish-xts|512b|92.4 MiB/s|92.7 MiB/s|
|aes-xts|512b|67.9 MiB/s|66.1 MiB/s|
|speck128-cbc|256b|98.6 MiB/s|86.8 MiB/s|
|twofish-cbc|256b|77.6 MiB/s|93.1 MiB/s|
|aes-cbc|256b|57.2 MiB/s|57.5 MiB/s|
|serpent-cbc|256b|31.0 MiB/s|105.7 MiB/s|
|speck128-ctr|256b|121.5 MiB/s|123.0 MiB/s|
|twofish-ctr|256b|66.9 MiB/s|67.6 MiB/s|
|aes-ctr|256b|64.5 MiB/s|64.5 MiB/s|
|serpent-ctr|256b|29.3 MiB/s|29.7 MiB/s|

#### AMD Ryzen 5 5500

|Algorithm|Key|Encryption|Decryption|
|---------|---|----------|----------|
|aes-xts|512b|3831.5 MiB/s|3820.2 MiB/s|
|xchacha12,aes-adiantum|256b|1738.1 MiB/s|1732.9 MiB/s|
|serpent-xts|512b|750.2 MiB/s|745.6 MiB/s|
|speck128-xts|512b|453.9 MiB/s|392.6 MiB/s|
|twofish-xts|512b|416.1 MiB/s|423.7 MiB/s|
|aes-cbc|256b|933.5 MiB/s|2999.5 MiB/s|
|twofish-cbc|256b|253.0 MiB/s|448.2 MiB/s|
|speck128-cbc|256b|231.1 MiB/s|255.5 MiB/s|
|serpent-cbc|256b|128.5 MiB/s|830.8 MiB/s|
|aes-ctr|256b|3241.2 MiB/s|3245.3 MiB/s|
|speck128-ctr|256b|351.9 MiB/s|352.7 MiB/s|
|twofish-ctr|256b|193.4 MiB/s|193.9 MiB/s|
|serpent-ctr|256b|115.7 MiB/s|115.6 MiB/s|

Sorted from quickest to slowest algorithm per mode of operation, numbers gathered through:
```sh
#!/bin/sh
/sbin/cryptsetup benchmark --cipher xchacha12,aes-adiantum --key-size 256
/sbin/cryptsetup benchmark --cipher speck128-xts-plain64 --key-size 512
/sbin/cryptsetup benchmark --cipher serpent-xts-plain64 --key-size 512
/sbin/cryptsetup benchmark --cipher twofish-xts-plain64 --key-size 512
/sbin/cryptsetup benchmark --cipher aes-xts-plain64 --key-size 512

/sbin/cryptsetup benchmark --cipher speck128-cbc --key-size 256
/sbin/cryptsetup benchmark --cipher serpent-cbc --key-size 256
/sbin/cryptsetup benchmark --cipher twofish-cbc --key-size 256
/sbin/cryptsetup benchmark --cipher aes-cbc --key-size 256

/sbin/cryptsetup benchmark --cipher speck128-ctr --key-size 256
/sbin/cryptsetup benchmark --cipher serpent-ctr --key-size 256
/sbin/cryptsetup benchmark --cipher twofish-ctr --key-size 256
/sbin/cryptsetup benchmark --cipher aes-ctr --key-size 256
```

Surprisingly, Serpent is pretty close to SPECK on the old E2140 in XTS mode, and quicker on modern hardware in XTS mode. Adiantum is quicker, maybe this is all for nothing. Maybe this is why SPECK was removed from the Linux kernel. Adiantum is quicker and instills more confidence than a cipher with a round function consisting of 5 lines.

Note that the only block cipher mode that you should really use for disk encryption like this, is XTS mode.

### Installation

```console
# git clone https://github.com/containedreality/speck-linux /usr/src/speck-1.0
# dkms add -m speck -v 1.0
# dkms build -m speck -v 1.0
# dkms install -m speck -v 1.0
```
