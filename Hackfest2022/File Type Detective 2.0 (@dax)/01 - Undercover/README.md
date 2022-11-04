# 01 - Undercover

Pour ce challenge, un seul fichier nommé `unknown` est fourni.

```
$ file unknown
unknown: data

$ hexdump unknown -C
00000000  3d 3d 20 65 64 32 35 35  31 39 76 31 2d 70 75 62  |== ed25519v1-pub|
00000010  6c 69 63 3a 20 74 79 70  65 30 20 3d 3d 00 00 00  |lic: type0 ==...|
00000020  18 2e 72 89 e1 11 7d 1b  5f 0c f3 5b 61 82 af 8c  |..r...}._..[a...|
00000030  cf 81 1f 5e 71 2b 97 dc  13 f2 a6 b6 8e a7 56 8a  |...^q+........V.|
00000040
```

En recherchant la chaine `== ed25519v1-public: type0 ==`, nous sommes arrivé sur un repo Github contenant une librairie Python qui permet de générer des adresses Onion utilisé dans le réseau Tor.

https://github.com/cmehay/pytor/blob/master/pytor/onion.py#L221

On constate que l'en tête du fichier `== ed25519v1-public: type0 ==` signifie que c'est une clef publique probablement utilisée pour générer une adresse Onion.

Le script suivant se base sur les [specs](https://github.com/torproject/torspec/blob/main/rend-spec-v3.txt#L2161) afin de générer l'adresse Onion. La variable `PUBKEY` contient les bytes retrouvés dans le fichier `unknown` après l'en tête et les trois bytes null (`\x00`)

```py
from hashlib import sha3_256
from base64 import b32encode

PUBKEY = b"\x18\x2e\x72\x89\xe1\x11\x7d\x1b\x5f\x0c\xf3\x5b\x61\x82\xaf\x8c\xcf\x81\x1f\x5e\x71\x2b\x97\xdc\x13\xf2\xa6\xb6\x8e\xa7\x56\x8a"
VERSION = b"\x03"

checksum = sha3_256(b".onion checksum" + PUBKEY + VERSION).digest()[:2]
onion_address = b32encode(PUBKEY + checksum + VERSION).decode().lower() + ".onion"

print(onion_address)
```

```
daxhfcpbcf6rwxym6nnwdavprthych26oevzpxat6ktlndvhk2flpbqd.onion
```

En navigant avec le Tor Browser sur l'adresse obtenue, le flag s'affiche.
