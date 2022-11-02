# 01 - Flickering Light Bulb 💡 1/4 
Pour cette série de challenges, un fichier de capture Wireshark `BTLE_Capture.pcapng` est fourni.

Le nom du challenge et le nom du fichier indique qu'il faudra analyser des paquets Bluetooth d'une ampoule intelligente.

Si en ouvrant le fichier dans Wireshark les paquets ne sont pas bien décodés. Il faut ajuster cette option:

> Go to Preferences->Protocols->DLT_USER and enter "btle" as protocol
> https://github.com/greatscottgadgets/ubertooth/issues/61

Le premier flag consiste à trouver le nom de l'ampoule:
![picture 2](images/41c7d0aa554dca4478e3c6ca347e2211a7dbadc916d7fd4a04bfbf9bb81ea372.png)

Il suffit de prendre un paquet de type `ADV_IND` afin de retrouver le nom de l'appareil

```
Minger_H6001_F6C1
```
