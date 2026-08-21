# Raportti

## Lähdekoodi
```c
#include <stdio.h>

int main() {
    printf("Hello World!\n");
    return 0;
}
```
### Lähdekoodi käännetään binääriksi
Komento:
```bash
gcc test.c -o test
```
Komento:
```bash
./test
```
Tulos:
Hello World!

## Binäärin analysointi
### 1. Binäärin perustiedot
Komento:
```bash
file test
```
Tulos:
test: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=65516172b524113dbad404d53f19610bdc74d5d2, for GNU/Linux 3.2.0, not stripped

file-komento kertoo test-tiedoston perusteella, että kyseessä on 64-bittinen ELF-binääri. Binääri on tarkoitettu x86-64-arkkitehtuurille, ohjelma käyttää dynaamisesti linkitettyjä kirjastoja ja binäärissä on symbolitietoja.
### 2. Binääristä löytyvät merkkijonot
Komento:
```bash
strings test | grep "Hello"
```
Tulos:
Hello World!
Lähdekoodin Hello World! löytyy myös käännetystä binääristä. Merkkijono on siis tallennettu binääriin. 

### 3. Sectionit
Komento:
```bash
readelf -S test
```
Tulos:
  [16] .text             PROGBITS         0000000000001060  00001060
       0000000000000107  0000000000000000  AX       0     0     16
  [18] .rodata           PROGBITS         0000000000002000  00002000
       0000000000000011  0000000000000000   A       0     0     4

Binääri on jaettu erilaisiin sectioneihin. Section .text sisältää ohjelman koodia ja .rodata sisältää luettavaa dataa esim. merkkijonoja.

### 4. Konekielinen koodi
Komento:
```bash
objdump -d test | grep -A 20 "<main>"
```
Tulos:
0000000000001149 <main>:
    1149:       f3 0f 1e fa             endbr64
    114d:       55                      push   %rbp
    114e:       48 89 e5                mov    %rsp,%rbp
    1151:       48 8d 05 ac 0e 00 00    lea    0xeac(%rip),%rax        # 2004 <_IO_stdin_used+0x4>
    1158:       48 89 c7                mov    %rax,%rdi
    115b:       e8 f0 fe ff ff          call   1050 <puts@plt>
    1160:       b8 00 00 00 00          mov    $0x0,%eax
    1165:       5d                      pop    %rbp
    1166:       c3                      ret

Main alkaa osoitteesta 0x1149. objdump-komennolla siis näkee esim main()-funktion sijainnin ja sen konekielisen toteutuksen. 

## Lähteet
ChatGPT (GPT-5.6 Luna), käytetty apuna Linux-komentojen ja ELF-binäärin perusteiden ymmärtämisessä, sekä raportin rakenteen ja kieliopin tarkistamiseen.
