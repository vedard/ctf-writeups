# 04 - The Other Hidden Function

En lisant le code source de la fonction _flag4() trouvé précédemment, on remarque qu'il faut passer la chaine "🔫" en paramètre afin de retourner le flag.

Par contre, comme la valeur de retour, le paramètre doit être une adresse pointant vers la chaine.

En inspectant, le fichier "websockets-doom.wasm" on peut retrouver l'addresse nécessaire.

Il suffit alors de convertir l'addresse en entier, de la passer en paramètre à la fonction _flag4() et d'utiliser le Memory Inspector pour retrouver la valeur retournée comme pour le troisième flag.
