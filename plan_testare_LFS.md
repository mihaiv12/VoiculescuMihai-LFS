# Plan de Testare

**Denumirea proiectului:** Construirea unui sistem de operare Linux de la zero folosind documentația Linux From Scratch (LFS), ediția stable-systemd (versiunea 13.0-systemd)

**Tip document:** Plan de Testare (PT)

**Organizație:** Academia Tehnica Militara "Ferdinand I"

**Grupă:** C-112C

**Contribuitori:**

| Nume și prenume | Rol | Contact |
|---|---|---|
| Voiculescu Mihai | Autor / Tester | — |

---

## Cuprins

1. [Capitolul 1 - Strategia de testare](#capitolul-1---strategia-de-testare)
   - 1.1. [Scopul proiectului](#11-scopul-proiectului)
   - 1.2. [Lista definițiilor](#12-lista-definițiilor)
   - 1.3. [Descrierea resurselor necesare testării](#13-descrierea-resurselor-necesare-testării)
2. [Capitolul 2 - Descrierea testelor](#capitolul-2---descrierea-testelor)
   - 2.1. LFS Cap. 2 - Pregătirea sistemului host
     - TC-01 - Verificarea versiunilor uneltelor software și a kernelului pe sistemul host
     - TC-02 - Verificarea existenței partiției, sistemului de fișiere și montării $LFS
   - 2.2. LFS Cap. 3 - Pachete și patch-uri
     - TC-03 - Verificarea prezenței și integrității pachetelor și patch-urilor descărcate
   - 2.3. LFS Cap. 4 - Pregătiri finale
     - TC-04 - Verificarea structurii minime de directoare și a utilizatorului lfs
     - TC-05 - Verificarea configurării mediului de lucru al utilizatorului lfs
   - 2.4. LFS Cap. 5 - Compilarea toolchain-ului încrucișat
     - TC-06 - Verificarea existenței Binutils Pass 1, GCC Pass 1, header-elor API Linux, Glibc și Libstdc++ în $LFS/tools
     - TC-07 - Testarea funcțională a toolchain-ului prin compilarea unui program C
   - 2.5. LFS Cap. 6 - Compilarea încrucișată a uneltelor temporare
     - TC-08 - Verificarea existenței celor 14 unelte temporare instalate
     - TC-09 - Verificarea instalării Binutils Pass 2 și GCC Pass 2
   - 2.6. LFS Cap. 7 - Intrarea în chroot și construirea uneltelor temporare suplimentare
     - TC-10 - Verificarea proprietarului ierarhiei $LFS, a montării sistemelor de fișiere virtuale și a accesului la chroot
     - TC-11 - Verificarea existenței uneltelor suplimentare instalate în chroot (Gettext, Bison, Perl, Python, Texinfo, Util-linux)
   - 2.7. LFS Cap. 8 - Instalarea software-ului de bază al sistemului
     - TC-12 - Verificarea instalării Glibc și a toolchain-ului final în chroot
     - TC-13 - Verificarea instalării și funcționării Systemd și D-Bus
   - 2.8. LFS Cap. 9 - Configurarea sistemului
     - TC-14 - Verificarea fișierelor de configurare a rețelei, localei și consolei
     - TC-15 - Verificarea fișierelor de configurare Systemd și a hostname-ului
   - 2.9. LFS Cap. 10 - Realizarea sistemului LFS bootabil
     - TC-16 - Verificarea existenței /etc/fstab și a kernelului Linux compilat
     - TC-17 - Verificarea instalării și configurării GRUB
   - 2.10. LFS Cap. 11 - Finalul
     - TC-18 - Verificarea repornirii și pornirii cu succes a noului sistem LFS

---

## Istoric versiuni document

| Versiune | Data | Autor | Descrierea modificărilor |
|---|---|---|---|
| 1.0 | 21.07.2026 | Voiculescu Mihai | Versiunea inițială a Planului de Testare, acoperind parțial procesul LFS |
| 2.0 | 21.07.2026 | Voiculescu Mihai | Refacere completă pentru a acoperi toate cele 10 capitole tehnice ale cărții LFS stable-systemd (versiunea 13.0-systemd) |
| 3.0 | 21.07.2026 | Voiculescu Mihai | Reformulare a tuturor testelor (cu excepția TC-07) din teste de tip „construire și instalare” în teste de **verificare**: fiecare test presupune că etapa corespunzătoare a fost deja parcursă conform documentației LFS și verifică doar existența, integritatea și funcționarea corectă a rezultatului (fișiere, unelte, pachete, servicii), fără a mai descrie pașii de creare/instalare/compilare ca parte a testului |

---

## Capitolul 1 - Strategia de testare

### 1.1. Scopul proiectului

Scopul acestui document este de a defini strategia și cazurile de testare necesare pentru validarea corectitudinii construirii sistemului de operare Linux prin metoda Linux From Scratch (LFS), conform cărții oficiale, ediția **stable-systemd**, versiunea **13.0-systemd**. Testele definite acoperă toate cele 10 capitole tehnice ale cărții (Capitolele 2-11).

Testele din acest document sunt teste de **verificare**, nu de construire: se pornește de la premisa că etapa corespunzătoare din Documentația LFS a fost deja parcursă de dezvoltator, iar rolul testului este exclusiv de a confirma că rezultatul acelei etape există, este complet și funcționează conform așteptărilor (fișiere prezente, unelte instalate și funcționale, servicii pornite corect etc.), conform cerințelor definite în Documentul de Cerințe Software (DCS) al proiectului. Excepție face TC-07, care testează funcțional toolchain-ul prin compilarea efectivă a unui program, aceasta fiind singura modalitate de a-i verifica funcționarea corectă.

### 1.2. Lista definițiilor

| Abreviere | Descriere |
|---|---|
| LFS | Linux From Scratch - metodologie și documentație pentru construirea unui sistem Linux de la zero, pornind de la codul sursă |
| DCS | Document de Cerințe Software |
| PT | Plan de Testare |
| HW | Hardware |
| SW | Software |
| $LFS | Variabilă de mediu care indică punctul de montare al partiției dedicate construirii sistemului LFS |
| Toolchain | Ansamblu de unelte de compilare (Binutils, GCC, Glibc) construite izolat, folosite pentru compilarea restului sistemului |
| Chroot | Mecanism prin care se schimbă directorul rădăcină aparent al unui proces, izolând mediul de build de sistemul host |
| PTY | Pseudo-terminal (Pseudo TeletYpe) |
| Pass 1 / Pass 2 | Cele două etape succesive de construire a componentelor toolchain-ului (Binutils, GCC), fiecare rafinând rezultatul precedentei |
| SBU | Standard Build Unit - unitate de măsură a timpului de compilare, folosită de cartea LFS pentru a estima duratele de build |
| PID 1 | Primul proces pornit de kernel la boot (în acest caz, Systemd), responsabil cu inițializarea restului sistemului |
| GRUB | Grand Unified Bootloader - bootloader-ul folosit pentru a porni sistemul LFS |

### 1.3. Descrierea resurselor necesare testării

**Cerințe hardware (preluate din DCS):**

- Mașină fizică sau virtuală x86_64, pe care sistemul LFS a fost deja construit conform documentației (partiție dedicată, minimum 10-20 GB)
- Minimum 2-4 GB RAM
- Acces la o partiție swap deja configurată

**Cerințe software (preluate din DCS):**

- Sistemul host folosit pentru build, cu variabila `$LFS` încă disponibilă în mediu
- Acces la mediul chroot al sistemului LFS (pentru testele din Capitolele 7-9)
- Acces la consola/monitorul mașinii pentru testul final de boot (TC-18)

**Estimarea resurselor umane:**

Fiind vorba despre teste de verificare (fără pași de construire/instalare), volumul de efort este redus față de faza de build. Se estimează implicarea unei singure persoane (autorul proiectului), pe o perioadă de aproximativ 1-2 zile lucrătoare, pentru parcurgerea celor 18 cazuri de testare.

---

## Capitolul 2 - Descrierea testelor

### 2.1. LFS Cap. 2 - Pregătirea sistemului host

#### TC-01

**Titlu:** Verificarea versiunilor uneltelor software și a kernelului pe sistemul host

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 2.1 - Sistemul host trebuie să dispună de versiunile minime ale uneltelor de dezvoltare și de un kernel Linux cu suport PTY UNIX 98, conform Capitolului 2 din cartea LFS |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 2 - Cerințe software host |
| Modalitate de testare | Pasul 1. Se rulează scriptul `version-check.sh` (existent din etapa de pregătire).<br>Pasul 2. Se verifică, pentru fiecare unealtă listată (Bash, Binutils, Bison, Coreutils, Diffutils, Findutils, Gawk, GCC, G++, Grep, Gzip, M4, Make, Patch, Perl, Python, Sed, Tar, Texinfo, Xz), mesajul afișat.<br>Pasul 3. Se verifică secțiunile „Aliases”, „Compiler check” și verificarea kernelului/PTY din output. |
| Rezultat așteptat | Pentru fiecare unealtă apare deja mesajul „OK: \<unealtă\> \<versiune\> >= \<versiune minimă\>”, „OK: g++ works”, „OK: Linux Kernel \<versiune\> >= 5.4” și „OK: Linux Kernel supports UNIX 98 PTY”. Niciun mesaj „ERROR” nu este prezent. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-02

**Titlu:** Verificarea existenței partiției, sistemului de fișiere și montării $LFS

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 2.2 - Trebuie să existe o partiție dedicată, formatată ext4, montată la punctul indicat de variabila $LFS, cu permisiunile corecte |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 2 - Cerințe de stocare |
| Modalitate de testare | Pasul 1. Se rulează `lsblk -f` și se verifică existența partiției cu sistem de fișiere `ext4` dedicate LFS.<br>Pasul 2. Se rulează `mount | grep $LFS` pentru a verifica dacă partiția este montată.<br>Pasul 3. Se rulează `swapon -s` pentru a verifica dacă partiția swap este activă.<br>Pasul 4. Se rulează `ls -ld $LFS` pentru a verifica proprietarul și permisiunile directorului. |
| Rezultat așteptat | `lsblk -f` arată partiția dedicată formatată `ext4`, `mount` confirmă că `$LFS` este montat pe acea partiție, `swapon -s` listează partiția swap ca activă, iar `ls -ld $LFS` arată proprietarul `root:root` și permisiunile `755`. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.2. LFS Cap. 3 - Pachete și patch-uri

#### TC-03

**Titlu:** Verificarea prezenței și integrității pachetelor și patch-urilor descărcate

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 3.1 - Toate pachetele sursă și patch-urile necesare construirii LFS trebuie să fie prezente în $LFS/sources și să aibă integritatea confirmată |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 3 - Cerințe privind pachetele sursă |
| Modalitate de testare | Pasul 1. Se rulează `ls $LFS/sources | wc -l` și se compară numărul de fișiere cu numărul total de pachete și patch-uri din lista oficială a Capitolului 3.<br>Pasul 2. Din `$LFS/sources`, se rulează `md5sum -c md5sums`.<br>Pasul 3. Se verifică vizual, prin `ls $LFS/sources`, prezența câtorva pachete cheie (ex. `binutils-2.46.0.tar.xz`, `gcc-15.2.0.tar.xz`, `linux-6.18.10.tar.xz`, `glibc-2.43.tar.xz`). |
| Rezultat așteptat | Numărul de fișiere corespunde listei oficiale, `md5sum -c md5sums` afișează „OK” pentru fiecare pachet, fără linii „FAILED”, iar pachetele cheie verificate sunt prezente. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.3. LFS Cap. 4 - Pregătiri finale

#### TC-04

**Titlu:** Verificarea structurii minime de directoare și a utilizatorului lfs

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 4.1 - Trebuie să existe structura minimă de directoare ($LFS/{etc,var,usr/{bin,lib,sbin},tools}) și un utilizator dedicat „lfs”, membru al grupului „lfs”, cu drepturi depline asupra directoarelor de lucru |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 4 - Cerințe privind mediul de build |
| Modalitate de testare | Pasul 1. Se rulează `ls -l $LFS` și se verifică existența directoarelor `etc`, `var`, `usr/bin`, `usr/lib`, `usr/sbin`, `tools` (și symlink-ul `lib64` pe x86_64).<br>Pasul 2. Se rulează `id lfs` pentru a verifica existența utilizatorului și apartenența la grupul `lfs`.<br>Pasul 3. Se verifică cu `ls -l $LFS` proprietarul directoarelor `usr`, `var`, `etc`, `tools`. |
| Rezultat așteptat | Toate directoarele din structura minimă există, `id lfs` confirmă utilizatorul și grupul, iar directoarele `usr`, `var`, `etc`, `tools` (și `lib64`) sunt deținute de utilizatorul `lfs`. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-05

**Titlu:** Verificarea configurării mediului de lucru al utilizatorului lfs

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 4.2 - Utilizatorul lfs trebuie să dispună de un mediu de shell izolat (`.bash_profile` și `.bashrc`), cu `$PATH` restricționat la `$LFS/tools/bin` și uneltele esențiale ale hostului, și cu `umask` setat la 022 |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 4 - Cerințe privind mediul de build |
| Modalitate de testare | Pasul 1. Se verifică existența fișierelor `~lfs/.bash_profile` și `~lfs/.bashrc`.<br>Pasul 2. Se autentifică drept `lfs` cu `su - lfs`.<br>Pasul 3. Se verifică `echo $LFS`, `echo $PATH` și `umask`. |
| Rezultat așteptat | Fișierele `.bash_profile` și `.bashrc` există, `$LFS` este setat corect, `$PATH` conține `$LFS/tools/bin` înaintea directoarelor standard, iar `umask` returnează `0022`. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.4. LFS Cap. 5 - Compilarea toolchain-ului încrucișat

#### TC-06

**Titlu:** Verificarea existenței Binutils Pass 1, GCC Pass 1, header-elor API Linux, Glibc și Libstdc++ în $LFS/tools

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 5.1 - Binutils-2.46.0 (Pass 1), GCC-15.2.0 (Pass 1), header-ele API Linux-6.18.10, Glibc-2.43 și Libstdc++ trebuie să fie instalate în $LFS/tools, conform Capitolului 5 |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 5 - Cerințe privind toolchain-ul |
| Modalitate de testare | Pasul 1. Se verifică existența `$LFS/tools/bin/x86_64-lfs-linux-gnu-as` și `-ld`, cu `x86_64-lfs-linux-gnu-as --version`.<br>Pasul 2. Se verifică existența `$LFS/tools/bin/x86_64-lfs-linux-gnu-gcc`, cu `x86_64-lfs-linux-gnu-gcc --version`.<br>Pasul 3. Se verifică existența directorului `$LFS/tools/include/linux` (header-e kernel).<br>Pasul 4. Se verifică existența `$LFS/tools/lib/libc.so` și `libstdc++.so`. |
| Rezultat așteptat | Toate fișierele și bibliotecile menționate există în `$LFS/tools`, iar comenzile `--version` returnează cod de ieșire 0, cu versiunile 2.46.0 (Binutils) și 15.2.0 (GCC). |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-07

**Titlu:** Testarea funcțională a toolchain-ului prin compilarea unui program C

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 5.2 - Toolchain-ul construit (Binutils + GCC + Glibc) trebuie să fie funcțional și capabil să compileze și să lege corect un program C simplu, izolat de sistemul host |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 5 - Cerințe privind toolchain-ul |
| Modalitate de testare | Pasul 1. Se creează un fișier sursă `test.c` cu un program minimal (ex. „Hello, world!”).<br>Pasul 2. Se compilează cu `$LFS/tools/bin/x86_64-lfs-linux-gnu-gcc test.c -o test`.<br>Pasul 3. Se verifică arhitectura binarului cu `readelf -l test` și se caută linia „Program interpreter”.<br>Pasul 4. Se confirmă că interpretorul indicat este `$LFS/tools/lib/ld-linux-x86-64.so.2` (nu cel al sistemului host).<br>Pasul 5. Se execută binarul rezultat și se verifică codul de ieșire. |
| Rezultat așteptat | Compilarea reușește fără erori, se generează executabilul `test`, interpretorul raportat de `readelf -l` este cel din `$LFS/tools`, iar executarea returnează codul de ieșire 0. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.5. LFS Cap. 6 - Compilarea încrucișată a uneltelor temporare

#### TC-08

**Titlu:** Verificarea existenței celor 14 unelte temporare instalate

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 6.1 - Uneltele temporare (M4, Ncurses, Bash, Coreutils, Diffutils, File, Findutils, Gawk, Grep, Gzip, Make, Patch, Sed, Tar, Xz) trebuie să fie instalate în $LFS/tools |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 6 - Cerințe privind uneltele temporare |
| Modalitate de testare | Pasul 1. Se rulează `ls $LFS/tools/bin` și se verifică prezența executabilelor corespunzătoare celor 14 pachete.<br>Pasul 2. Pentru un eșantion (`bash`, `make`, `tar`, `sed`, `gawk`), se rulează `$LFS/tools/bin/<unealtă> --version`. |
| Rezultat așteptat | `$LFS/tools/bin` conține executabilele corespunzătoare tuturor celor 14 pachete, iar comenzile `--version` din eșantion returnează cod de ieșire 0, cu versiunile corecte. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-09

**Titlu:** Verificarea instalării Binutils Pass 2 și GCC Pass 2

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 6.2 - Binutils-2.46.0 și GCC-15.2.0 (Pass 2) trebuie să fie instalate în $LFS/tools, finalizând toolchain-ul izolat de sistemul host |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 6 - Cerințe privind uneltele temporare |
| Modalitate de testare | Pasul 1. Se rulează `$LFS/tools/bin/x86_64-lfs-linux-gnu-gcc --version` și se verifică versiunea 15.2.0.<br>Pasul 2. Se recompilează programul `test.c` din TC-07 folosind acest compilator și se verifică din nou interpretorul cu `readelf -l`.<br>Pasul 3. Se verifică data de modificare a binarelor (`ls -l`) pentru a confirma că sunt cele rezultate din Pass 2, nu din Pass 1. |
| Rezultat așteptat | Compilatorul instalat este versiunea 15.2.0 din Pass 2, recompilarea programului de test reușește fără erori, iar interpretorul rămâne corect. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.6. LFS Cap. 7 - Intrarea în chroot și construirea uneltelor temporare suplimentare

#### TC-10

**Titlu:** Verificarea proprietarului ierarhiei $LFS, a montării sistemelor de fișiere virtuale și a accesului la chroot

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 7.1 - Proprietarul ierarhiei $LFS trebuie să fie root, sistemele de fișiere virtuale ale kernelului (proc, sys, dev) trebuie să fie montate, iar mediul chroot trebuie să fie accesibil |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 7 - Cerințe privind mediul chroot |
| Modalitate de testare | Pasul 1. Se rulează `ls -l $LFS` și se verifică că directoarele `usr`, `lib`, `var`, `etc`, `bin`, `sbin`, `tools` sunt deținute de `root:root`.<br>Pasul 2. Se rulează `mount | grep $LFS` și se verifică montarea `/dev`, `/dev/pts`, `/proc`, `/sys`, `/run`.<br>Pasul 3. Se intră în mediul chroot și se verifică `whoami` și accesul la `/proc`, `/sys`, `/dev` din interior. |
| Rezultat așteptat | Proprietarul ierarhiei `$LFS` este `root:root`, toate sistemele de fișiere virtuale apar montate, iar intrarea în chroot este posibilă; în interior, `whoami` returnează `root`, iar `/proc`, `/sys`, `/dev` sunt accesibile. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-11

**Titlu:** Verificarea existenței uneltelor suplimentare instalate în chroot (Gettext, Bison, Perl, Python, Texinfo, Util-linux)

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 7.2 - În interiorul chroot trebuie să fie instalate pachetele Gettext, Bison, Perl, Python, Texinfo și Util-linux, alături de directoarele și symlink-urile esențiale |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 7 - Cerințe privind mediul chroot |
| Modalitate de testare | Pasul 1. Din interiorul chroot, se verifică existența symlink-ului `/bin/sh` și a fișierului `/etc/mtab`.<br>Pasul 2. Se rulează `bison --version`, `perl -v`, `python3 --version`, `texi2any --version`, `lsblk --version`. |
| Rezultat așteptat | Symlink-ul și fișierul verificate există, iar toate comenzile de mai sus returnează cod de ieșire 0, cu versiunile corespunzătoare pachetelor instalate. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.7. LFS Cap. 8 - Instalarea software-ului de bază al sistemului

#### TC-12

**Titlu:** Verificarea instalării Glibc și a toolchain-ului final în chroot

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 8.1 - Glibc-2.43 și GCC-15.2.0 (varianta finală) trebuie să fie instalate în chroot, alături de Binutils-2.46.0 final, formând toolchain-ul definitiv al sistemului |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 8 - Cerințe privind software-ul de bază |
| Modalitate de testare | Pasul 1. Din chroot, se rulează `gcc --version` (fără prefix cross) și se verifică versiunea 15.2.0.<br>Pasul 2. Se verifică existența `/lib64/ld-linux-x86-64.so.2` (linker-ul dinamic final al Glibc).<br>Pasul 3. Se recompilează programul `test.c` cu `gcc` (fără prefix) și se verifică `readelf -l` pentru interpretorul final. |
| Rezultat așteptat | `gcc --version` confirmă 15.2.0, linker-ul dinamic final există, iar compilarea programului de test produce un binar cu interpretorul `/lib64/ld-linux-x86-64.so.2` (nu mai `$LFS/tools`). |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-13

**Titlu:** Verificarea instalării și funcționării Systemd și D-Bus

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 8.2 - Systemd-259.1 și D-Bus-1.16.2 trebuie să fie instalate, urmând a fi folosite ca sistem de inițializare (PID 1) al sistemului LFS final |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 8 - Cerințe privind software-ul de bază |
| Modalitate de testare | Pasul 1. Se verifică existența `/usr/lib/systemd/systemd` și `/usr/bin/systemctl`.<br>Pasul 2. Se rulează `systemctl --version` din chroot.<br>Pasul 3. Se rulează `systemctl list-unit-files` și se verifică prezența unităților de bază (ex. `multi-user.target`).<br>Pasul 4. Se verifică existența `/usr/bin/dbus-daemon`. |
| Rezultat așteptat | Binarele Systemd și D-Bus există, `systemctl --version` returnează versiunea 259, iar unitățile de bază sunt prezente în lista afișată de `systemctl list-unit-files`. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.8. LFS Cap. 9 - Configurarea sistemului

#### TC-14

**Titlu:** Verificarea fișierelor de configurare a rețelei, localei și consolei

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 9.1 - Sistemul trebuie să dispună de fișierele de rețea (`/etc/hosts`, `/etc/resolv.conf`), de configurația localei implicite și de configurația consolei (tastatură, font) |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 9 - Cerințe privind configurarea sistemului |
| Modalitate de testare | Pasul 1. Se verifică existența și conținutul `/etc/hosts` și `/etc/resolv.conf`.<br>Pasul 2. Se verifică existența și conținutul `/etc/locale.conf` (ex. `LANG=en_US.UTF-8`).<br>Pasul 3. Se verifică existența și conținutul `/etc/vconsole.conf`. |
| Rezultat așteptat | Toate fișierele există, au sintaxă validă și conțin valorile specificate în documentația proiectului. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-15

**Titlu:** Verificarea fișierelor de configurare Systemd și a hostname-ului

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 9.2 - Numele de host al sistemului (`/etc/hostname`) și fișierele `/etc/inputrc`, `/etc/shells` trebuie să existe și să fie corect completate |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 9 - Cerințe privind configurarea sistemului |
| Modalitate de testare | Pasul 1. Se rulează `cat /etc/hostname` și se verifică numele de host configurat.<br>Pasul 2. Se verifică existența `/etc/inputrc`.<br>Pasul 3. Se rulează `cat /etc/shells` și se verifică prezența `/bin/bash`. |
| Rezultat așteptat | `/etc/hostname` conține numele de host ales, `/etc/inputrc` există, iar `/etc/shells` listează cel puțin `/bin/bash` ca shell valid. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.9. LFS Cap. 10 - Realizarea sistemului LFS bootabil

#### TC-16

**Titlu:** Verificarea existenței /etc/fstab și a kernelului Linux compilat

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 10.1 - Trebuie să existe fișierul `/etc/fstab` cu partițiile sistemului, precum și kernelul Linux-6.18.10 compilat cu suportul necesar pentru hardware-ul țintă |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 10 - Cerințe privind boot-ul sistemului |
| Modalitate de testare | Pasul 1. Se rulează `cat /etc/fstab` și se verifică intrările pentru partiția rădăcină și swap.<br>Pasul 2. Se verifică existența `/boot/vmlinuz-6.18.10-lfs-...`, `/boot/System.map-...` și `/boot/config-...`.<br>Pasul 3. Se verifică existența directorului de module `/lib/modules/6.18.10-lfs-...`. |
| Rezultat așteptat | `/etc/fstab` conține intrările corecte pentru toate partițiile, iar fișierele imaginii kernelului, System.map, config și directorul de module sunt prezente. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-17

**Titlu:** Verificarea instalării și configurării GRUB

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 10.2 - Bootloader-ul GRUB-2.14 trebuie să fie instalat pe disc, iar `/boot/grub/grub.cfg` trebuie să conțină configurația necesară pornirii sistemului LFS |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 10 - Cerințe privind boot-ul sistemului |
| Modalitate de testare | Pasul 1. Se verifică existența directorului `/boot/grub` și a fișierelor sale (module GRUB).<br>Pasul 2. Se rulează `cat /boot/grub/grub.cfg` și se verifică prezența unei intrări de meniu pentru kernelul instalat la TC-16. |
| Rezultat așteptat | `/boot/grub` conține fișierele de instalare GRUB, iar `/boot/grub/grub.cfg` conține cel puțin o intrare de meniu validă, care pointează către kernelul și `initramfs`-ul instalate. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.10. LFS Cap. 11 - Finalul

#### TC-18

**Titlu:** Verificarea repornirii și pornirii cu succes a noului sistem LFS

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 11.1 - Sistemul LFS trebuie să pornească independent, fără mediul chroot sau sistemul host, folosind kernelul și bootloader-ul instalate, și trebuie să ajungă la un prompt de autentificare funcțional |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 11 - Cerințe privind finalizarea sistemului |
| Modalitate de testare | Pasul 1. Se repornește mașina (fizică sau virtuală) și se selectează, din meniul GRUB, intrarea corespunzătoare sistemului LFS.<br>Pasul 2. Se urmărește procesul de boot (mesajele Systemd) până la afișarea unui prompt de autentificare.<br>Pasul 3. Se autentifică cu un utilizator valid și se rulează `uname -a`. |
| Rezultat așteptat | Sistemul pornește fără erori critice, Systemd ajunge la ținta implicită (ex. `multi-user.target`), este afișat un prompt de autentificare funcțional, iar `uname -a` confirmă kernelul 6.18.10 rulând independent de sistemul host. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
