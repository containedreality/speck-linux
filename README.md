# speck-linux

the SPECK kernel module ported from 4.17.7 to modern more kernel releases and as a module.

## Why?

SPECK was removed from linux ~4.20. Turns out someone down the line would want to use it though.

I needed fast encryption for an older RPI. AES likely would've been slow and prove itself a bottle neck (possibly even a security issue depending on if the kernel uses constant time implementations), and I don't like Adiantum, I don't know how it works, I don't care to learn how it works (yet). I just like a nice 128 bit block cipher with 256 bit keys, in XTS mode.

### Installation

```console
# git clone https://github.com/containedreality/speck-linux /usr/src/speck-1.0
# dkms add -m speck -v 1.0
# dkms build -m speck -v 1.0
# dkms install -m speck -v 1.0
```