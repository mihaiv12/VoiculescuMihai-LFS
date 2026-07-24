# Plan de Testare
**Denumirea proiectului:** Construirea unui sistem de operare Linux de la zero folosind documentația Linux From Scratch (LFS), ediția stable-systemd (versiunea 13.0-systemd)
**Tip document:** Plan de Testare (PT)
<<<<<<< HEAD
**Organizație:** Academia Tehnica Militara "Ferdinand I"
=======

**Organizație:** Academia Tehnica Militara "Ferdinand I"

>>>>>>> 7e842ac66458bea6cdadfbbfe97e8ddeb20072be
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
     - TC-01 - Verificarea versiunilor uneltelor esențiale de dezvoltare și a kernelului pe sistemul LFS bootat
     - TC-02 - Verificarea partiției rădăcină, a sistemului de fișiere și a swap-ului activ pe sistemul bootat
   - 2.2. LFS Cap. 4 - Pregătiri finale
     - TC-03 - Verificarea structurii de directoare a sistemului
   - 2.3. LFS Cap. 5 - Compilarea toolchain-ului încrucișat
     - TC-04 - Testarea funcțională a toolchain-ului încrucișat prin compilarea unui program C
   - 2.4. LFS Cap. 6 - Compilarea încrucișată a uneltelor temporare
     - TC-05 - Verificarea existenței celor 14 unelte temporare instalate în /usr/bin
     - TC-06 - Verificarea instalării Binutils Pass 2 și GCC Pass 2
   - 2.5. LFS Cap. 7 - Construirea uneltelor temporare suplimentare
     - TC-07 - Verificarea proprietarului ierarhiei rădăcină și a montării sistemelor de fișiere virtuale pe sistemul bootat
     - TC-08 - Verificarea existenței uneltelor suplimentare instalate (Gettext, Bison, Perl, Python, Texinfo, Util-linux)
   - 2.6. LFS Cap. 8 - Instalarea software-ului de bază al sistemului
     - TC-09 - Verificarea toolchain-ului final (Glibc, GCC, Binutils) instalat pe sistem
     - TC-10 - Verificarea instalării și funcționării Systemd și D-Bus
   - 2.7. LFS Cap. 9 - Configurarea sistemului
     - TC-11 - Verificarea fișierelor de configurare a rețelei, localei și consolei
     - TC-12 - Verificarea fișierelor de configurare Systemd și a hostname-ului
   - 2.8. LFS Cap. 10 - Realizarea sistemului LFS bootabil
     - TC-13 - Verificarea /etc/fstab și a kernelului Linux compilat, aflat în execuție
     - TC-14 - Verificarea instalării și configurării GRUB
   - 2.9. LFS Cap. 11 - Finalul
     - TC-15 - Verificarea faptului că sistemul LFS rulează stabil și independent, folosind kernelul și bootloader-ul instalate
---
## Istoric versiuni document
| Versiune | Data | Autor | Descrierea modificărilor |
|---|---|---|---|
| 1.0 | 21.07.2026 | Voiculescu Mihai | Versiunea inițială a Planului de Testare, acoperind parțial procesul LFS |
| 2.0 | 21.07.2026 | Voiculescu Mihai | Refacere completă pentru a acoperi toate cele 10 capitole tehnice ale cărții LFS stable-systemd (versiunea 13.0-systemd) |
| 3.0 | 21.07.2026 | Voiculescu Mihai | Reformulare a tuturor testelor (cu excepția TC-07) din teste de tip „construire și instalare” în teste de **verificare** |
| 4.0 | 24.07.2026 | Voiculescu Mihai | Reformulare a modalității de testare pentru toate cele 18 cazuri, astfel încât fiecare test să poată fi rulat integral dintr-o sesiune de shell pe sistemul LFS deja bootat, fără acces la host sau chroot |
| 5.0 | 24.07.2026 | Voiculescu Mihai | Eliminarea testelor TC-03 (pachete/patch-uri sursă), TC-05 (mediu de lucru utilizator lfs) și TC-06 (existența toolchain-ului Pass 1 în /tools) din versiunea anterioară; renumerotare a celor 15 cazuri de testare rămase; eliminarea tuturor referințelor la directorul `/tools`, întrucât sistemul final nu are un director `/tools` separat — uneltele se găsesc direct în `/usr/bin` (respectiv `/bin`, symlink către `/usr/bin`) |
---
## Capitolul 1 - Strategia de testare
### 1.1. Scopul proiectului
Scopul acestui document este de a defini strategia și cazurile de testare necesare pentru validarea corectitudinii construirii sistemului de operare Linux prin metoda Linux From Scratch (LFS), conform cărții oficiale, ediția **stable-systemd**, versiunea **13.0-systemd**. Testele definite acoperă etapele tehnice relevante ale cărții (Capitolele 2, 4-11) și sunt concepute astfel încât să poată fi rulate integral **dintr-o sesiune de shell pe sistemul LFS deja bootat**, fără a necesita acces la sistemul host folosit inițial pentru build sau reintrarea în mediul chroot.
Testele din acest document sunt teste de **verificare**, nu de construire: se pornește de la premisa că toate etapele din Documentația LFS au fost deja parcurse de dezvoltator, iar sistemul rezultat a fost bootat cu succes. Rolul testelor este exclusiv de a confirma, prin comenzi rulate pe sistemul viu, că rezultatul fiecărei etape există, este complet și funcționează conform așteptărilor (fișiere prezente, unelte instalate și funcționale, servicii pornite corect etc.), conform cerințelor definite în Documentul de Cerințe Software (DCS) al proiectului. Excepție face TC-04, care testează funcțional toolchain-ul încrucișat prin compilarea efectivă a unui program, aceasta fiind singura modalitate de a-i verifica funcționarea corectă. Testele nu execută pași de instalare, configurare sau montare.
Sistemul final construit **nu are un director `/tools` separat** — toate uneltele (temporare și finale) au fost instalate direct sub `/usr` (respectiv accesibile prin `/bin`, symlink către `/usr/bin`), astfel încât testele fac referire la aceste căi directe.
### 1.2. Lista definițiilor
| Abreviere | Descriere |
|---|---|
| LFS | Linux From Scratch - metodologie și documentație pentru construirea unui sistem Linux de la zero, pornind de la codul sursă |
| DCS | Document de Cerințe Software |
| PT | Plan de Testare |
| HW | Hardware |
| SW | Software |
| Toolchain | Ansamblu de unelte de compilare (Binutils, GCC, Glibc) construite izolat, folosite pentru compilarea restului sistemului |
| Chroot | Mecanism, folosit în etapa de build, prin care se schimbă directorul rădăcină aparent al unui proces; nu mai este necesar pentru testele din acest document, deoarece acestea rulează direct pe sistemul bootat |
| PTY | Pseudo-terminal (Pseudo TeletYpe) |
| Pass 1 / Pass 2 | Cele două etape succesive de construire a componentelor toolchain-ului (Binutils, GCC), fiecare rafinând rezultatul precedentei |
| SBU | Standard Build Unit - unitate de măsură a timpului de compilare, folosită de cartea LFS pentru a estima duratele de build |
| PID 1 | Primul proces pornit de kernel la boot (în acest caz, Systemd), responsabil cu inițializarea restului sistemului |
| GRUB | Grand Unified Bootloader - bootloader-ul folosit pentru a porni sistemul LFS |
### 1.3. Descrierea resurselor necesare testării
**Cerințe hardware (preluate din DCS):**
- Mașină fizică sau virtuală x86_64, pe care sistemul LFS a fost deja construit și **bootat cu succes** conform documentației (partiție dedicată, minimum 10-20 GB)
- Minimum 2-4 GB RAM
- Acces la o partiție swap deja configurată și activă
**Cerințe software (preluate din DCS):**
- O sesiune de autentificare funcțională pe sistemul LFS bootat (consolă locală, monitor al mașinii virtuale sau conexiune la distanță), cu drepturi de root pentru comenzile care necesită acces la fișiere de sistem
- Nu este necesar accesul la sistemul host folosit pentru build și nici reintrarea în mediul chroot
- Acces la consola/monitorul mașinii pentru confirmarea vizuală a promptului de autentificare (TC-15)
**Estimarea resurselor umane:**
Fiind vorba despre teste de verificare, rulate integral dintr-o singură sesiune pe sistemul bootat, volumul de efort este redus. Se estimează implicarea unei singure persoane (autorul proiectului), pe o perioadă de aproximativ 1 zi lucrătoare, pentru parcurgerea celor 15 cazuri de testare.
---
## Capitolul 2 - Descrierea testelor
### 2.1. LFS Cap. 2 - Pregătirea sistemului host
#### TC-01
**Titlu:** Verificarea versiunilor uneltelor esențiale de dezvoltare și a kernelului pe sistemul LFS bootat
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 2.1 - Sistemul trebuie să dispună de versiunile minime ale uneltelor de dezvoltare și de un kernel Linux cu suport PTY UNIX 98, conform Capitolului 2 din cartea LFS |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 2 - Cerințe software host |
| Modalitate de testare | Pasul 1. Autentificat pe sistemul LFS bootat, se rulează, pentru fiecare unealtă relevantă instalată pe sistem (Bash, Binutils, Bison, Coreutils, Diffutils, Findutils, Gawk, GCC, G++, Grep, Gzip, M4, Make, Patch, Perl, Python, Sed, Tar, Texinfo, Xz), comanda `--version` (ex. `bash --version`, `gcc --version`).<br>Pasul 2. Se rulează `uname -r` pentru a verifica versiunea kernelului aflat în execuție.<br>Pasul 3. Se verifică suportul PTY UNIX 98 rulând `python3 -c "import pty; print('OK')"` sau deschizând un pseudo-terminal cu `script /dev/null -c true`. |
| Rezultat așteptat | Fiecare unealtă returnează o versiune ≥ versiunea minimă cerută de cartea LFS, `uname -r` raportează kernelul ≥ 5.4, iar verificarea PTY confirmă suportul UNIX 98 (fără erori). |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
#### TC-02
**Titlu:** Verificarea partiției rădăcină, a sistemului de fișiere și a swap-ului activ pe sistemul bootat
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 2.2 - Trebuie să existe o partiție dedicată, formatată ext4, folosită ca rădăcină a sistemului bootat, cu permisiunile corecte |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 2 - Cerințe de stocare |
| Modalitate de testare | Pasul 1. Se rulează `lsblk -f` și se verifică că partiția folosită ca rădăcină are sistemul de fișiere `ext4`.<br>Pasul 2. Se rulează `findmnt /` (sau `mount | grep ' / '`) pentru a confirma partiția montată ca rădăcină.<br>Pasul 3. Se rulează `swapon -s` pentru a verifica dacă partiția swap este activă.<br>Pasul 4. Se rulează `ls -ld /` pentru a verifica proprietarul și permisiunile rădăcinii. |
| Rezultat așteptat | `lsblk -f` arată partiția rădăcină formatată `ext4`, `findmnt /` confirmă montarea corectă, `swapon -s` listează partiția swap ca activă, iar `ls -ld /` arată proprietarul `root:root` și permisiunile `755`. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
### 2.2. LFS Cap. 4 - Pregătiri finale
#### TC-03
**Titlu:** Verificarea structurii de directoare a sistemului
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 4.1 - Trebuie să existe structura minimă de directoare a sistemului (`/{etc,var,usr/{bin,lib,sbin}}`), cu proprietarul corect |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 4 - Cerințe privind mediul de build |
| Modalitate de testare | Pasul 1. Se rulează `ls -l /` și se verifică existența directoarelor `etc`, `var`, `usr/bin`, `usr/lib`, `usr/sbin` (și symlink-ul `lib64` pe x86_64).<br>Pasul 2. Se verifică cu `ls -ld /usr /var /etc` proprietarul directoarelor. |
| Rezultat așteptat | Toate directoarele din structura minimă există, iar directoarele de sistem sunt deținute de `root:root`. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | Sistemul nu are un director `/tools` separat — uneltele de build au fost instalate direct sub `/usr`. |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
### 2.3. LFS Cap. 5 - Compilarea toolchain-ului încrucișat
#### TC-04
**Titlu:** Testarea funcțională a toolchain-ului încrucișat prin compilarea unui program C
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 5.1 - Toolchain-ul construit (Binutils + GCC + Glibc) trebuie să fie funcțional și capabil să compileze și să lege corect un program C simplu |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 5 - Cerințe privind toolchain-ul |
| Modalitate de testare | Pasul 1. Din sesiunea de pe sistemul bootat, se creează un fișier sursă `test.c` cu un program minimal (ex. „Hello, world!”).<br>Pasul 2. Se compilează cu `x86_64-lfs-linux-gnu-gcc test.c -o test` (unealta se găsește direct în `/usr/bin`, fiind în `$PATH`).<br>Pasul 3. Se verifică arhitectura binarului cu `readelf -l test` și se caută linia „Program interpreter”.<br>Pasul 4. Se confirmă că interpretorul indicat este linkerul dinamic prezent pe sistem (`/lib64/ld-linux-x86-64.so.2`).<br>Pasul 5. Se execută binarul rezultat și se verifică codul de ieșire. |
| Rezultat așteptat | Compilarea reușește fără erori, se generează executabilul `test`, interpretorul raportat de `readelf -l` este cel prezent pe sistem, iar executarea returnează codul de ieșire 0. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
### 2.4. LFS Cap. 6 - Compilarea încrucișată a uneltelor temporare
#### TC-05
**Titlu:** Verificarea existenței celor 14 unelte temporare instalate în /usr/bin
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 6.1 - Uneltele temporare (M4, Ncurses, Bash, Coreutils, Diffutils, File, Findutils, Gawk, Grep, Gzip, Make, Patch, Sed, Tar, Xz) trebuie să fie instalate și disponibile pe sistem |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 6 - Cerințe privind uneltele temporare |
| Modalitate de testare | Pasul 1. Se rulează `ls /usr/bin` și se verifică prezența executabilelor corespunzătoare celor 14 pachete.<br>Pasul 2. Pentru un eșantion (`bash`, `make`, `tar`, `sed`, `gawk`), se rulează `<unealtă> --version`. |
| Rezultat așteptat | `/usr/bin` conține executabilele corespunzătoare tuturor celor 14 pachete, iar comenzile `--version` din eșantion returnează cod de ieșire 0, cu versiunile corecte. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
#### TC-06
**Titlu:** Verificarea instalării Binutils Pass 2 și GCC Pass 2
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 6.2 - Binutils-2.46.0 și GCC-15.2.0 (Pass 2) trebuie să fie instalate, finalizând toolchain-ul izolat |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 6 - Cerințe privind uneltele temporare |
| Modalitate de testare | Pasul 1. Se rulează `x86_64-lfs-linux-gnu-gcc --version` și se verifică versiunea 15.2.0.<br>Pasul 2. Se recompilează programul `test.c` din TC-04 folosind acest compilator și se verifică din nou interpretorul cu `readelf -l`.<br>Pasul 3. Se verifică data de modificare a binarelor (`ls -l /usr/bin/x86_64-lfs-linux-gnu-gcc`) pentru a confirma că sunt cele rezultate din Pass 2, nu din Pass 1. |
| Rezultat așteptat | Compilatorul instalat este versiunea 15.2.0 din Pass 2, recompilarea programului de test reușește fără erori, iar interpretorul rămâne corect. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
### 2.5. LFS Cap. 7 - Construirea uneltelor temporare suplimentare
#### TC-07
**Titlu:** Verificarea proprietarului ierarhiei rădăcină și a montării sistemelor de fișiere virtuale pe sistemul bootat
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 7.1 - Proprietarul ierarhiei rădăcină trebuie să fie root, iar sistemele de fișiere virtuale ale kernelului (proc, sys, dev) trebuie să fie montate |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 7 - Cerințe privind mediul de execuție al sistemului |
| Modalitate de testare | Pasul 1. Se rulează `ls -ld /usr /lib /var /etc /bin /sbin` și se verifică că sunt deținute de `root:root`.<br>Pasul 2. Se rulează `findmnt` (sau `mount`) și se verifică montarea `/dev`, `/dev/pts`, `/proc`, `/sys`, `/run`.<br>Pasul 3. Se rulează `ps -p 1 -o comm=` pentru a confirma că procesul PID 1 este `systemd`, rulând nativ pe sistem (nu într-un mediu chroot). |
| Rezultat așteptat | Proprietarul ierarhiei rădăcină este `root:root`, toate sistemele de fișiere virtuale apar montate, iar `ps -p 1 -o comm=` returnează `systemd`. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
#### TC-08
**Titlu:** Verificarea existenței uneltelor suplimentare instalate (Gettext, Bison, Perl, Python, Texinfo, Util-linux)
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 7.2 - Pe sistem trebuie să fie instalate pachetele Gettext, Bison, Perl, Python, Texinfo și Util-linux, alături de directoarele și symlink-urile esențiale |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 7 - Cerințe privind mediul de execuție al sistemului |
| Modalitate de testare | Pasul 1. Se verifică existența symlink-ului `/bin/sh` și a fișierului `/etc/mtab`.<br>Pasul 2. Se rulează `bison --version`, `perl -v`, `python3 --version`, `texi2any --version`, `lsblk --version`. |
| Rezultat așteptat | Symlink-ul și fișierul verificate există, iar toate comenzile de mai sus returnează cod de ieșire 0, cu versiunile corespunzătoare pachetelor instalate. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
### 2.6. LFS Cap. 8 - Instalarea software-ului de bază al sistemului
#### TC-09
**Titlu:** Verificarea toolchain-ului final (Glibc, GCC, Binutils) instalat pe sistem
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 8.1 - Glibc-2.43 și GCC-15.2.0 (varianta finală) trebuie să fie instalate, alături de Binutils-2.46.0 final, formând toolchain-ul definitiv al sistemului |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 8 - Cerințe privind software-ul de bază |
| Modalitate de testare | Pasul 1. Se rulează `gcc --version` (fără prefix cross) și se verifică versiunea 15.2.0.<br>Pasul 2. Se verifică existența `/lib64/ld-linux-x86-64.so.2` (linker-ul dinamic final al Glibc).<br>Pasul 3. Se recompilează programul `test.c` cu `gcc` (fără prefix) și se verifică `readelf -l` pentru interpretorul final. |
| Rezultat așteptat | `gcc --version` confirmă 15.2.0, linker-ul dinamic final există, iar compilarea programului de test produce un binar cu interpretorul `/lib64/ld-linux-x86-64.so.2`. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
#### TC-10
**Titlu:** Verificarea instalării și funcționării Systemd și D-Bus
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 8.2 - Systemd-259.1 și D-Bus-1.16.2 trebuie să fie instalate și folosite ca sistem de inițializare (PID 1) al sistemului LFS |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 8 - Cerințe privind software-ul de bază |
| Modalitate de testare | Pasul 1. Se verifică existența `/usr/lib/systemd/systemd` și `/usr/bin/systemctl`.<br>Pasul 2. Se rulează `systemctl --version`.<br>Pasul 3. Se rulează `systemctl list-unit-files` și se verifică prezența unităților de bază (ex. `multi-user.target`).<br>Pasul 4. Se verifică existența `/usr/bin/dbus-daemon` și starea serviciului cu `systemctl status dbus`. |
| Rezultat așteptat | Binarele Systemd și D-Bus există, `systemctl --version` returnează versiunea 259, iar unitățile de bază sunt prezente în lista afișată de `systemctl list-unit-files`; serviciul D-Bus este activ. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
### 2.7. LFS Cap. 9 - Configurarea sistemului
#### TC-11
**Titlu:** Verificarea fișierelor de configurare a rețelei, localei și consolei
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 9.1 - Sistemul trebuie să dispună de fișierele de rețea (`/etc/hosts`, `/etc/resolv.conf`), de configurația localei implicite și de configurația consolei (tastatură, font) |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 9 - Cerințe privind configurarea sistemului |
| Modalitate de testare | Pasul 1. Se verifică existența și conținutul `/etc/hosts` și `/etc/resolv.conf`.<br>Pasul 2. Se verifică existența și conținutul `/etc/locale.conf` (ex. `LANG=en_US.UTF-8`), și se rulează `locale` pentru a confirma locala activă.<br>Pasul 3. Se verifică existența și conținutul `/etc/vconsole.conf`. |
| Rezultat așteptat | Toate fișierele există, au sintaxă validă și conțin valorile specificate în documentația proiectului; `locale` confirmă locala activă pe sistemul rulat. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
#### TC-12
**Titlu:** Verificarea fișierelor de configurare Systemd și a hostname-ului
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 9.2 - Numele de host al sistemului (`/etc/hostname`) și fișierele `/etc/inputrc`, `/etc/shells` trebuie să existe și să fie corect completate |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 9 - Cerințe privind configurarea sistemului |
| Modalitate de testare | Pasul 1. Se rulează `hostnamectl` (sau `cat /etc/hostname`) și se verifică numele de host configurat, coroborat cu numele afișat efectiv în prompt.<br>Pasul 2. Se verifică existența `/etc/inputrc`.<br>Pasul 3. Se rulează `cat /etc/shells` și se verifică prezența `/bin/bash`. |
| Rezultat așteptat | `/etc/hostname` conține numele de host ales, iar `hostnamectl` raportează același nume pe sistemul rulat; `/etc/inputrc` există, iar `/etc/shells` listează cel puțin `/bin/bash` ca shell valid. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
### 2.8. LFS Cap. 10 - Realizarea sistemului LFS bootabil
#### TC-13
**Titlu:** Verificarea /etc/fstab și a kernelului Linux compilat, aflat în execuție
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 10.1 - Trebuie să existe fișierul `/etc/fstab` cu partițiile sistemului, precum și kernelul Linux-6.18.10 compilat cu suportul necesar pentru hardware-ul țintă, aflat efectiv în execuție |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 10 - Cerințe privind boot-ul sistemului |
| Modalitate de testare | Pasul 1. Se rulează `cat /etc/fstab` și se verifică intrările pentru partiția rădăcină și swap.<br>Pasul 2. Se rulează `uname -r` și se verifică existența `/boot/vmlinuz-6.18.10-lfs-...`, `/boot/System.map-...` și `/boot/config-...` corespunzătoare.<br>Pasul 3. Se verifică existența directorului de module `/lib/modules/6.18.10-lfs-...` pentru kernelul rulat. |
| Rezultat așteptat | `/etc/fstab` conține intrările corecte pentru toate partițiile, `uname -r` raportează kernelul 6.18.10-lfs, iar fișierele imaginii kernelului, System.map, config și directorul de module corespunzătoare sunt prezente. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
#### TC-14
**Titlu:** Verificarea instalării și configurării GRUB
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 10.2 - Bootloader-ul GRUB-2.14 trebuie să fie instalat pe disc, iar `/boot/grub/grub.cfg` trebuie să conțină configurația necesară pornirii sistemului LFS |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 10 - Cerințe privind boot-ul sistemului |
| Modalitate de testare | Pasul 1. Se verifică existența directorului `/boot/grub` și a fișierelor sale (module GRUB).<br>Pasul 2. Se rulează `cat /boot/grub/grub.cfg` și se verifică prezența unei intrări de meniu pentru kernelul confirmat la TC-13.<br>Pasul 3. Se confirmă indirect funcționarea GRUB prin faptul că sistemul curent a fost pornit cu succes prin acesta (sesiunea activă este dovada rulării). |
| Rezultat așteptat | `/boot/grub` conține fișierele de instalare GRUB, iar `/boot/grub/grub.cfg` conține cel puțin o intrare de meniu validă, care pointează către kernelul și `initramfs`-ul instalate și confirmate ca fiind cele în execuție. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
### 2.9. LFS Cap. 11 - Finalul
#### TC-15
**Titlu:** Verificarea faptului că sistemul LFS rulează stabil și independent, folosind kernelul și bootloader-ul instalate
| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 11.1 - Sistemul LFS trebuie să ruleze independent, fără mediul chroot sau sistemul host, folosind kernelul și bootloader-ul instalate, și trebuie să fi ajuns la o stare de execuție funcțională |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 11 - Cerințe privind finalizarea sistemului |
| Modalitate de testare | Pasul 1. Din sesiunea curentă, autentificat pe sistemul deja bootat, se rulează `systemctl is-system-running` și `systemctl get-default`.<br>Pasul 2. Se rulează `journalctl -b -p 0..3` pentru a verifica absența erorilor critice de la boot-ul curent.<br>Pasul 3. Se rulează `who -b` pentru a confirma momentul boot-ului și `uname -a` pentru a confirma kernelul rulat. |
| Rezultat așteptat | `systemctl is-system-running` returnează `running` (nu `degraded` sau `maintenance`), ținta implicită este `multi-user.target` (sau echivalentă), `journalctl -b -p 0..3` nu raportează erori critice, iar `uname -a` confirmă kernelul 6.18.10-lfs rulând nativ pe sistem. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
