# 1- Just a bit of reverse and coding (by @salt)

Pour ce challenge, un exécutable `chal1` est fourni en plus d'une adresse vers un serveur TCP.

```sh
$ file chal1
chal1: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=9f0776b32e3f34a4c6da8c7341491d2156d1096e, not stripped

$ checksec --file=chal1
RELRO           STACK CANARY      NX            PIE             RPATH      RUNPATH      Symbols         FORTIFY Fortified       Fortifiable     FILE
Partial RELRO   No canary found   NX enabled    PIE enabled     No RPATH   No RUNPATH   87 Symbols        No    0               2               chal1

$ ./chal1
|------- WELCOME TO THE B&V shop -------|
| 1. Alcohol list                       |
| 2. About us                           |
| 3. Quit                               |
|---------------------------------------|
```

En décompilant l'exécutable avec Ghidra, on peut voir dans la fonction `main` qu'il y a une option secrète `admin` disponible en choisissant `1337` sur le menu.
```c
int main()
{
  seed = time((time_t *)0x0);
  srand((uint)seed);
  choice = 0;
  max = 999;
  min = 100;
  iVar1 = rand();
  rand_value = min + iVar1 % ((max - min) + 1);
  while (choice != 3) {
    print_menu();
    __isoc99_scanf(&DAT_00102270,&choice);
    if (choice == 1) {
      print_alcohol();
    }
    if (choice == 2) {
      about();
    }
    if (choice == 1337) {
      admin(rand_value);
    }
  }
  puts("Bye!");
  return 0;
}
```

La fonction `admin` affiche le flag contenu dans un fichier `.txt`, mais il faut d'abord trouver le mot de passe.
```c
void admin(int random_value)
{
  static_password = get_password(0xccccccc);
  random_password = static_password * random_value;
  printf("Admin password: ");
  __isoc99_scanf(&DAT_00102270,&input);
  if (random_password == input) {
    puts("Good job!");
    flag_filestream = fopen("flag1.txt","r");
    if (flag_filestream == (FILE *)0x0) {
      puts("ERROR! The flag cannot be read! Try again and if the problem persists contact the designe r!");
    }
    else {
      puts("This is your flag:");
      fgets((char *)&local_48,0x19,flag_filestream);
    }
    puts((char *)&local_48);
    fclose(flag_filestream);
  }
  else {
    puts("Wrong password!");
  }
  return;
}
```

Pour générer le mot de passe, l'exécutable appel une série de fonctions imbriquées afin de générer une valeur statique `44160`. Cette valeur est ensuite multipliée avec une valeur aléatoire.

```c
static_password = get_password(0xccccccc);
random_password = static_password * random_value;
```

Localement, on peut se mettre un breakpoint à l'adresse `00101384` ou `<admin+105>` afin de voir le mot de passe dans le registre RAX.

```c
gdb-peda$
[----------------------------------registers-----------------------------------]
RAX: 0x58f200
RBX: 0x7fffffffded8 --> 0x7fffffffe252 ("/home/vincent/Downloads/chal1")
RCX: 0x0
RDX: 0x0
RSI: 0x539
RDI: 0x55555555625f ("Admin password: ")
RBP: 0x7fffffffdda0 --> 0x7fffffffddc0 --> 0x1
RSP: 0x7fffffffdd50 --> 0x555555556008 ("|------- WELCOME TO THE B&V shop -------|\n| 1. Alcohol list", ' ' <repeats 23 times>, "|\n| 2. About us", ' ' <repeats 27 times>, "|\n| 3. Quit", ' ' <repeats 31 times>, "|\n|", '-' <repeats 31 times>...)
RIP: 0x555555555384 (<admin+105>:	mov    eax,0x0)
R8 : 0x1999999999999999
R9 : 0xa ('\n')
R10: 0x7ffff7f35ac0 --> 0x100000000
R11: 0x7ffff7f363c0 --> 0x2000200020002
R12: 0x0
R13: 0x7fffffffdee8 --> 0x7fffffffe270 ("ALACRITTY_LOG=/tmp/Alacritty-6229.log")
R14: 0x0
R15: 0x7ffff7ffd000 --> 0x7ffff7ffe2c0 --> 0x555555554000 --> 0x10102464c457f
EFLAGS: 0x206 (carry PARITY adjust zero sign trap INTERRUPT direction overflow)
[-------------------------------------code-------------------------------------]
   0x555555555376 <admin+91>:	imul   eax,DWORD PTR [rbp-0x44]
   0x55555555537a <admin+95>:	mov    DWORD PTR [rbp-0xc],eax
   0x55555555537d <admin+98>:	lea    rdi,[rip+0xedb]        # 0x55555555625f
=> 0x555555555384 <admin+105>:	mov    eax,0x0
   0x555555555389 <admin+110>:	call   0x555555555050 <printf@plt>
   0x55555555538e <admin+115>:	lea    rax,[rbp-0x1c]
   0x555555555392 <admin+119>:	mov    rsi,rax
   0x555555555395 <admin+122>:	lea    rdi,[rip+0xed4]        # 0x555555556270
[------------------------------------stack-------------------------------------]
0000| 0x7fffffffdd50 --> 0x555555556008 ("|------- WELCOME TO THE B&V shop -------|\n| 1. Alcohol list", ' ' <repeats 23 times>, "|\n| 2. About us", ' ' <repeats 27 times>, "|\n| 3. Quit", ' ' <repeats 31 times>, "|\n|", '-' <repeats 31 times>...)
0008| 0x7fffffffdd58 --> 0x84f7e2bbfa
0016| 0x7fffffffdd60 --> 0x0
0024| 0x7fffffffdd68 --> 0x0
0032| 0x7fffffffdd70 --> 0x0
0040| 0x7fffffffdd78 --> 0x7fffffffdd00 --> 0x1
0048| 0x7fffffffdd80 --> 0x0
0056| 0x7fffffffdd88 --> 0x0
[------------------------------------------------------------------------------]
Legend: code, data, rodata, value
0x0000555555555384 in admin ()
```

La valeur hexadécimal `0x58f200` du registre `RAX` se convertit en `5829120` en décimal

```
Admin password: 5829120
Good job!
ERROR! The flag cannot be read! Try again and if the problem persists contact the designer!
```

Cela fonctionne localement, mais sur le serveur il ne sera pas possible de débugger l'exécution.

Il ne semble pas avoir de vulnérabilités pouvant leaker la valeur aléatoire ou le mot de passe durant l'exécution du programme.

Il y a 899 mots de passe différents, il pourrait être possible de lancer l'exécution à répétition avec un mot de passe identifié localement et éventuellement être chanceux, mais il existe une meilleure méthode.

```c
max = 999;
min = 100;
iVar1 = rand();
rand_value = min + iVar1 % ((max - min) + 1);
```

Puisque la seed utilisée est la date et heure au tout début de l'exécution, on peut de se connecter au serveur en même temps que de lancer l'exécutable sur son poste afin d'avoir exactement le même mot de passe.

```c
seed = time((time_t *)0x0);
srand((uint)seed);
```

Il suffit alors d'extraire le mot de passe du registre RAX localement et de le fournir au serveur afin d'obtenir le flag.