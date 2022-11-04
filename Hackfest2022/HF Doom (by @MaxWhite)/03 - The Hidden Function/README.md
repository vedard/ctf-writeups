# 03 - The Hidden Function

Ce challenge consite à trouver une fonction caché dans une version en WebAssembly du jeux Doom (basé sur https://silentspacemarine.com/).

La première étape est de rechercher dans le code source disponible dans l'inspecteur de Chrome.

En recherchant le terme "flag", on pouvait trouver deux fonctions intéressantes:

- _flag3()
- _flag4()

En appelant dans la console, la fonction _flag3() on obtient un nombre entier qui correspond en fait à une adresse.

En utilisant le Memory inspector de Chrome, il est possible d'entrer l'adresse obtenue (convertie en hex) afin de voir la valeur de retourné de la fonction.
> https://developer.chrome.com/docs/devtools/memory-inspector/

Cette valeur est bien le troisième flag.
