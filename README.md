# Aruba-Decrypter
Decrypt passwords from Aruba config files

Aruba configuration files store `wpa-passphrase` and `username/passwd` entries as hex strings using 3DES with a static key.  The IV is taken from the first 8 bytes of the hex string, and the remainder is the cipher text.

An example hex string found in an Aruba config might look like this:<br />
```
8aa786b345cb6fcfffbec10fbc6b4d6fced5abb72d1173a8
```

Remove the first 8 bytes as the initialization vector (IV):<br />
```
8aa786b345cb6fcf
```

The remaining part of the hex string is the cipher text:<br />
```
ffbec10fbc6b4d6fced5abb72d1173a8
```

The static Key is:<br />
```
327410849117754614758292434904591869159427843003
```

Pass the values into an openssl command pipeline to recover the plain text password:<br />
```
echo -n ffbec10fbc6b4d6fced5abb72d1173a8 | xxd -r -p | openssl enc -des3 --nosalt -K 327410849117754614758292434904591869159427843003 -iv 8aa786b345cb6fcf -d | hexdump -Cv
```

The output will look something like this:<br />
```
00000000    24 65 63 72 65 74 50 40 35 35 77 30 72 64    |$ecretP@55w0rd|
```
