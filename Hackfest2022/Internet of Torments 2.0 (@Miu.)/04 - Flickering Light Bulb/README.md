# 03 - Flickering Light Bulb 💡 3/4 

Le dernier flag consiste à construire la commande une fois connectée  avec `gatttool` afin de mettre l'ampoule orange `RGB(255, 87, 51)`

La commande suit le format suivant `char-write-req  <handle> <new value>`
> http://tvaira.free.fr/flower-power/gatttool.txt

Le handle est le même que celui dans le paquet affiché dans Wireshark.

Donc, voici le flag:
```
char-write-req 0x0015 330502ff573300000000000000000000000000af
```

