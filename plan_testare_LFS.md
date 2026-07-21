# Plan de Testare

**Denumirea proiectului:** Construirea unui sistem de operare Linux de la zero folosind documentația Linux From Scratch (LFS), ediția stable-systemd (versiunea 13.0-systemd)

**Tip document:** Plan de Testare (PT)

**Organizație:** [Denumire instituție / facultate]

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
     - TC-02 - Verificarea creării partiției, sistemului de fișiere și montării $LFS
   - 2.2. LFS Cap. 3 - Pachete și patch-uri
     - TC-03 - Verificarea descărcării și integrității pachetelor și patch-urilor
   - 2.3. LFS Cap. 4 - Pregătiri finale
     - TC-04 - Verificarea structurii minime de directoare și a utilizatorului lfs
     - TC-05 - Verificarea configurării mediului de lucru al utilizatorului lfs
   - 2.4. LFS Cap. 5 - Compilarea toolchain-ului încrucișat
     - TC-06 - Verificarea instalării Binutils Pass 1, GCC Pass 1, header-elor API Linux, Glibc și Libstdc++
     - TC-07 - Testarea funcțională a toolchain-ului prin compilarea unui program C
   - 2.5. LFS Cap. 6 - Compilarea încrucișată a uneltelor temporare
     - TC-08 - Verificarea construirii celor 14 unelte temporare
     - TC-09 - Verificarea Binutils Pass 2 și GCC Pass 2
   - 2.6. LFS Cap. 7 - Intrarea în chroot și construirea uneltelor temporare suplimentare
     - TC-10 - Verificarea schimbării proprietarului, montării sistemelor de fișiere virtuale și intrării în chroot
     - TC-11 - Verificarea construirii uneltelor suplimentare în chroot (Gettext, Bison, Perl, Python, Texinfo, Util-linux)
   - 2.7. LFS Cap. 8 - Instalarea software-ului de bază al sistemului
     - TC-12 - Verificarea reconstruirii Glibc și a toolchain-ului final în chroot
     - TC-13 - Verificarea instalării și funcționării Systemd și D-Bus
   - 2.8. LFS Cap. 9 - Configurarea sistemului
     - TC-14 - Verificarea configurării rețelei, localei și consolei
     - TC-15 - Verificarea configurării Systemd (hostname, fișiere de configurare)
   - 2.9. LFS Cap. 10 - Realizarea sistemului LFS bootabil
     - TC-16 - Verificarea creării /etc/fstab și compilării kernelului Linux
     - TC-17 - Verificarea instalării și configurării GRUB
   - 2.10. LFS Cap. 11 - Finalul
     - TC-18 - Verificarea repornirii și pornirii cu succes a noului sistem LFS

---

## Istoric versiuni document

| Versiune | Data | Autor | Descrierea modificărilor |
|---|---|---|---|
| 1.0 | 21.07.2026 | Voiculescu Mihai | Versiunea inițială a Planului de Testare, acoperind parțial procesul LFS (pregătirea sistemului host, partiționare, pachete, utilizator, toolchain, temporary tools) |
| 2.0 | 21.07.2026 | Voiculescu Mihai | Refacere completă a Planului de Testare pentru a acoperi toate cele 10 capitole tehnice ale cărții Linux From Scratch, ediția stable-systemd (versiunea 13.0-systemd): pregătirea hostului, pachete, pregătiri finale, toolchain, unelte temporare, chroot, software de bază al sistemului, configurare, boot și finalizare |

---

## Capitolul 1 - Strategia de testare

### 1.1. Scopul proiectului

Scopul acestui document este de a defini strategia și cazurile de testare necesare pentru validarea corectitudinii construirii sistemului de operare Linux prin metoda Linux From Scratch (LFS), conform cărții oficiale, ediția **stable-systemd**, versiunea **13.0-systemd**. Testele definite acoperă toate cele 10 capitole tehnice ale cărții (Capitolele 2-11): pregătirea sistemului host, descărcarea pachetelor, pregătirile finale, construirea toolchain-ului încrucișat, construirea uneltelor temporare, intrarea în chroot, instalarea software-ului de bază al sistemului, configurarea sistemului, realizarea sistemului bootabil și verificarea finală a pornirii sistemului.

Testele au ca obiectiv verificarea faptului că fiecare etapă a procesului de construire produce rezultatul așteptat conform cerințelor definite în Documentul de Cerințe Software (DCS) al proiectului, astfel încât produsul software final (sistemul LFS funcțional și bootabil) să poată fi validat înainte de predarea proiectului.

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

- Mașină fizică sau virtuală x86_64
- Minimum 10-20 GB spațiu de stocare liber, alocat unei partiții dedicate procesului LFS (necesarul crește față de estimarea inițială odată cu includerea Capitolelor 8-10, care instalează întregul sistem final, kernelul și GRUB)
- Minimum 2-4 GB RAM recomandat pentru compilare, în special pentru pachetele mari din Capitolul 8 (GCC, Systemd, kernel)
- Acces la o partiție swap

**Cerințe software (preluate din DCS):**

- Sistem de operare Linux host cu versiunile minime de unelte specificate în Capitolul 2 al cărții LFS (Bash, Binutils, Bison, Coreutils, Diffutils, Findutils, Gawk, GCC/G++, Grep, Gzip, Kernel Linux ≥ 5.4, M4, Make, Patch, Perl, Python, Sed, Tar, Texinfo, Xz)
- Acces la internet pentru descărcarea pachetelor sursă și a patch-urilor (wget)
- Utilitare fdisk, mkfs, mkswap pentru partiționare
- Bootloader GRUB instalat pe sistemul host sau acces la un mediu care permite testarea boot-ului noului sistem (mașină virtuală recomandată, pentru a putea reveni ușor la o stare anterioară în caz de eșec la boot)

**Estimarea resurselor umane:**

Având în vedere dimensiunea proiectului (un singur produs software - sistemul LFS complet și bootabil, construit de un singur dezvoltator/tester) și volumul de 18 cazuri de testare care acoperă toate cele 10 capitole tehnice ale cărții, se estimează implicarea unei singure persoane (autorul proiectului) în activitatea de testare, pe o perioadă de aproximativ 5-7 zile lucrătoare, testarea desfășurându-se în paralel cu etapele de build corespunzătoare (Capitolele 5-7 fiind cele mai consumatoare de timp datorită numărului mare de compilări).

---

## Capitolul 2 - Descrierea testelor

### 2.1. LFS Cap. 2 - Pregătirea sistemului host

#### TC-01

**Titlu:** Verificarea versiunilor uneltelor software și a kernelului pe sistemul host

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 2.1 - Sistemul host trebuie să dispună de versiunile minime ale uneltelor de dezvoltare și de un kernel Linux cu suport PTY UNIX 98, conform Capitolului 2 din cartea LFS |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 2 - Cerințe software host |
| Modalitate de testare | Pasul 1. Se creează scriptul `version-check.sh` conform Capitolului 2 al cărții LFS.<br>Pasul 2. Se execută `bash version-check.sh` din shell.<br>Pasul 3. Se verifică mesajele afișate pentru fiecare unealtă (Bash, Binutils, Bison, Coreutils, Diffutils, Findutils, Gawk, GCC, G++, Grep, Gzip, M4, Make, Patch, Perl, Python, Sed, Tar, Texinfo, Xz), secțiunea „Aliases” (awk→GNU, yacc→Bison, sh→Bash), „Compiler check” și verificarea kernelului/PTY. |
| Rezultat așteptat | Pentru fiecare unealtă se afișează „OK: \<unealtă\> \<versiune\> >= \<versiune minimă\>”, „OK: g++ works”, „OK: Linux Kernel \<versiune\> >= 5.4” și „OK: Linux Kernel supports UNIX 98 PTY”. Niciun mesaj „ERROR” nu trebuie să apară. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-02

**Titlu:** Verificarea creării partiției, sistemului de fișiere și montării $LFS

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 2.2 - Trebuie creată o partiție dedicată, formatată ext4, montată la punctul indicat de variabila $LFS, cu permisiunile corecte |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 2 - Cerințe de stocare |
| Modalitate de testare | Pasul 1. Se creează partiția cu `fdisk` (partiție principală + swap).<br>Pasul 2. Se formatează cu `mkfs -v -t ext4 /dev/sdb3` și se activează swap cu `mkswap /dev/sdb2`.<br>Pasul 3. Se exportă `$LFS`, se montează cu `mount -v -t ext4 /dev/sdb3 $LFS` și se activează swap cu `/sbin/swapon -v /dev/sdb2`.<br>Pasul 4. Se setează `chown root:root $LFS` și `chmod 755 $LFS`.<br>Pasul 5. Se verifică cu `mount | grep $LFS`, `swapon -s` și `ls -ld $LFS`. |
| Rezultat așteptat | `$LFS` apare montat pe partiția ext4 creată, swap-ul este activ, iar `ls -ld $LFS` arată proprietarul `root:root` și permisiunile `755`, fără erori la niciuna dintre comenzi. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.2. LFS Cap. 3 - Pachete și patch-uri

#### TC-03

**Titlu:** Verificarea descărcării și integrității pachetelor și patch-urilor

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 3.1 - Toate pachetele sursă și patch-urile necesare construirii LFS trebuie descărcate integral în $LFS/sources și verificate ca integritate |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 3 - Cerințe privind pachetele sursă |
| Modalitate de testare | Pasul 1. Se descarcă pachetele și patch-urile cu `wget --input-file=wget-list-systemd --continue --directory-prefix=$LFS/sources`.<br>Pasul 2. Se navighează cu `pushd $LFS/sources`.<br>Pasul 3. Se rulează `md5sum -c md5sums`.<br>Pasul 4. Se numără fișierele descărcate cu `ls $LFS/sources | wc -l` și se compară cu numărul așteptat din lista de pachete a Capitolului 3.<br>Pasul 5. Se revine cu `popd`. |
| Rezultat așteptat | Toate pachetele și patch-urile din listă sunt prezente, iar `md5sum -c md5sums` afișează „OK” pentru fiecare, fără linii „FAILED”. Numărul de fișiere corespunde listei oficiale a cărții. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.3. LFS Cap. 4 - Pregătiri finale

#### TC-04

**Titlu:** Verificarea structurii minime de directoare și a utilizatorului lfs

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 4.1 - Trebuie creată structura minimă de directoare ($LFS/{etc,var,usr/{bin,lib,sbin},tools}) și un utilizator dedicat „lfs”, membru al grupului „lfs”, cu drepturi depline asupra directoarelor de lucru |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 4 - Cerințe privind mediul de build |
| Modalitate de testare | Pasul 1. Se creează structura minimă de directoare conform Capitolului 4 (`mkdir -pv $LFS/{etc,var} $LFS/usr/{bin,lib,sbin}` etc., plus symlink-urile `lib64`/`lib` pe x86_64).<br>Pasul 2. Se creează grupul și utilizatorul cu `groupadd lfs` și `useradd -s /bin/bash -g lfs -m -k /dev/null lfs`.<br>Pasul 3. Se setează parola și permisiunile cu `chown -v lfs $LFS/{usr{,/*},var,etc,tools}` (și `$LFS/lib64` pe x86_64).<br>Pasul 4. Se verifică cu `id lfs` și `ls -l $LFS`. |
| Rezultat așteptat | Structura de directoare există conform specificației, `id lfs` confirmă utilizatorul și grupul, iar directoarele `usr`, `var`, `etc`, `tools` (și `lib64`) sunt deținute de utilizatorul `lfs`. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-05

**Titlu:** Verificarea configurării mediului de lucru al utilizatorului lfs

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 4.2 - Utilizatorul lfs trebuie să dispună de un mediu de shell izolat (`.bash_profile` și `.bashrc`), cu `$PATH` restricționat la `$LFS/tools/bin` și uneltele esențiale ale hostului, și cu `umask` setat la 022 |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 4 - Cerințe privind mediul de build |
| Modalitate de testare | Pasul 1. Se creează fișierele `~/.bash_profile` și `~/.bashrc` pentru utilizatorul `lfs` conform Capitolului 4.<br>Pasul 2. Se autentifică drept `lfs` cu `su - lfs`.<br>Pasul 3. Se verifică `echo $LFS`, `echo $PATH` și `umask`. |
| Rezultat așteptat | `$LFS` este setat corect, `$PATH` conține `$LFS/tools/bin` înaintea directoarelor standard, iar `umask` returnează `0022`. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.4. LFS Cap. 5 - Compilarea toolchain-ului încrucișat

#### TC-06

**Titlu:** Verificarea instalării Binutils Pass 1, GCC Pass 1, header-elor API Linux, Glibc și Libstdc++

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 5.1 - Binutils-2.46.0 (Pass 1), GCC-15.2.0 (Pass 1), header-ele API Linux-6.18.10, Glibc-2.43 și Libstdc++ trebuie construite și instalate în $LFS/tools, conform Capitolului 5 |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 5 - Cerințe privind toolchain-ul |
| Modalitate de testare | Pasul 1. Se construiesc, în ordine, Binutils Pass 1, GCC Pass 1, header-ele API Linux, Glibc și Libstdc++, conform pașilor din Capitolul 5.<br>Pasul 2. Se verifică prezența `$LFS/tools/bin/x86_64-lfs-linux-gnu-as` și `-ld`.<br>Pasul 3. Se verifică `$LFS/tools/include/linux` (header-e kernel).<br>Pasul 4. Se verifică `$LFS/tools/lib/libc.so` și `libstdc++.so`. |
| Rezultat așteptat | Toate cele cinci componente se compilează și instalează fără erori; fișierele și bibliotecile menționate există în `$LFS/tools`. |
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

**Titlu:** Verificarea construirii celor 14 unelte temporare

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 6.1 - Uneltele temporare (M4, Ncurses, Bash, Coreutils, Diffutils, File, Findutils, Gawk, Grep, Gzip, Make, Patch, Sed, Tar, Xz) trebuie construite și instalate în $LFS/tools folosind toolchain-ul Pass 1 |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 6 - Cerințe privind uneltele temporare |
| Modalitate de testare | Pasul 1. Pentru fiecare pachet, se dezarhivează sursa, se configurează cu prefix `$LFS/tools`, se compilează cu `make` și se instalează cu `make install`, conform Capitolului 6.<br>Pasul 2. Se listează conținutul `$LFS/tools/bin`.<br>Pasul 3. Pentru un eșantion (`bash`, `make`, `tar`), se rulează `$LFS/tools/bin/<unealtă> --version`. |
| Rezultat așteptat | Toate cele 14 pachete se compilează și instalează fără erori, `$LFS/tools/bin` conține executabilele corespunzătoare, iar comenzile `--version` returnează cod de ieșire 0. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-09

**Titlu:** Verificarea Binutils Pass 2 și GCC Pass 2

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 6.2 - Binutils-2.46.0 și GCC-15.2.0 trebuie reconstruite (Pass 2), folosind uneltele temporare deja instalate, pentru a finaliza toolchain-ul izolat de sistemul host |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 6 - Cerințe privind uneltele temporare |
| Modalitate de testare | Pasul 1. Se dezarhivează din nou sursele Binutils și GCC în directoare noi de build.<br>Pasul 2. Se configurează, compilează și instalează conform pașilor Pass 2 din Capitolul 6.<br>Pasul 3. Se recompilează programul `test.c` din TC-07 folosind noul compilator Pass 2 și se verifică din nou interpretorul cu `readelf -l`. |
| Rezultat așteptat | Binutils și GCC Pass 2 se instalează fără erori în `$LFS/tools`, iar recompilarea programului de test produce un binar funcțional, cu interpretorul corect. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.6. LFS Cap. 7 - Intrarea în chroot și construirea uneltelor temporare suplimentare

#### TC-10

**Titlu:** Verificarea schimbării proprietarului, montării sistemelor de fișiere virtuale și intrării în chroot

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 7.1 - Proprietarul ierarhiei $LFS trebuie schimbat la root, sistemele de fișiere virtuale ale kernelului (proc, sys, dev) trebuie montate, iar mediul chroot trebuie accesibil, conform Capitolului 7 |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 7 - Cerințe privind mediul chroot |
| Modalitate de testare | Pasul 1. Se schimbă proprietarul cu `chown -R root:root $LFS/{usr,lib,var,etc,bin,sbin,tools}` (și `lib64`).<br>Pasul 2. Se montează sistemele de fișiere virtuale (`mount -v --bind /dev $LFS/dev`, `mount -vt devpts`, `mount -vt proc`, `mount -vt sysfs`, `mount -vt tmpfs`).<br>Pasul 3. Se intră în chroot cu `chroot "$LFS" /usr/bin/env -i ... /bin/bash --login`.<br>Pasul 4. În interiorul chroot, se verifică `whoami`, `echo $PATH` și accesul la `/proc`, `/sys`, `/dev`. |
| Rezultat așteptat | Proprietarul `$LFS` este `root:root`, toate montările reușesc fără erori, iar intrarea în chroot este posibilă; în interior, `whoami` returnează `root`, iar `/proc`, `/sys`, `/dev` sunt accesibile. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-11

**Titlu:** Verificarea construirii uneltelor suplimentare în chroot (Gettext, Bison, Perl, Python, Texinfo, Util-linux)

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 7.2 - În interiorul chroot, trebuie construite directoarele esențiale, fișierele/symlink-urile de bază, apoi pachetele Gettext, Bison, Perl, Python, Texinfo și Util-linux |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 7 - Cerințe privind mediul chroot |
| Modalitate de testare | Pasul 1. În chroot, se creează directoarele esențiale și symlink-urile (`/bin/sh`, `mtab`, etc.) conform Capitolului 7.<br>Pasul 2. Se construiesc, în ordine, Gettext, Bison, Perl, Python, Texinfo, Util-linux.<br>Pasul 3. Se verifică rularea comenzilor `bison --version`, `perl -v`, `python3 --version`, `texi2any --version`, `lsblk --version` din interiorul chroot. |
| Rezultat așteptat | Toate pachetele se compilează și instalează fără erori în chroot, iar comenzile de verificare returnează cod de ieșire 0 cu versiunile corespunzătoare. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.7. LFS Cap. 8 - Instalarea software-ului de bază al sistemului

#### TC-12

**Titlu:** Verificarea reconstruirii Glibc și a toolchain-ului final în chroot

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 8.1 - Glibc-2.43 și GCC-15.2.0 (varianta finală, nu cross-compiler) trebuie reconstruite în chroot, alături de Binutils-2.46.0 final, formând toolchain-ul definitiv al sistemului |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 8 - Cerințe privind software-ul de bază |
| Modalitate de testare | Pasul 1. Se construiesc, în ordinea din Capitolul 8, Man-pages, Glibc, apoi (după celelalte biblioteci intermediare) Binutils final și GCC final.<br>Pasul 2. Se verifică versiunea și funcționarea compilatorului final cu `gcc -dumpspecs`, echivalent din secțiunea „Sanity Check” a Capitolului 8.<br>Pasul 3. Se recompilează programul `test.c` folosind `gcc` (fără prefix cross), din interiorul chroot, și se verifică `readelf -l` pentru interpretorul final `/lib64/ld-linux-x86-64.so.2`. |
| Rezultat așteptat | Glibc și toolchain-ul final se instalează fără erori, testul de sanitate GCC trece, iar compilarea programului de test produce un binar cu interpretorul sistemului final (nu mai `$LFS/tools`). |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-13

**Titlu:** Verificarea instalării și funcționării Systemd și D-Bus

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 8.2 - Systemd-259.1 și D-Bus-1.16.2 trebuie construite și instalate, urmând a fi folosite ca sistem de inițializare (PID 1) al sistemului LFS final |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 8 - Cerințe privind software-ul de bază |
| Modalitate de testare | Pasul 1. Se construiește și instalează D-Bus, apoi Systemd, conform pașilor din Capitolul 8.<br>Pasul 2. Se verifică prezența binarelor `/usr/lib/systemd/systemd`, `/usr/bin/systemctl`.<br>Pasul 3. Se rulează `systemctl --version` din chroot și se verifică lista unităților instalate cu `systemctl list-unit-files` (verificare statică, sistemul nefiind încă pornit efectiv). |
| Rezultat așteptat | Instalarea Systemd și D-Bus se finalizează fără erori, `systemctl --version` returnează versiunea 259, iar unitățile de bază (ex. `multi-user.target`) sunt prezente în listă. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.8. LFS Cap. 9 - Configurarea sistemului

#### TC-14

**Titlu:** Verificarea configurării rețelei, localei și consolei

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 9.1 - Sistemul trebuie configurat cu fișierele de rețea (`/etc/hosts`, `/etc/resolv.conf`), cu locala implicită și cu configurația consolei (tastatură, font), conform Capitolului 9 |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 9 - Cerințe privind configurarea sistemului |
| Modalitate de testare | Pasul 1. Se creează `/etc/hosts`, `/etc/resolv.conf` și fișierele `.network`/`.link` din `systemd-networkd`, conform secțiunii de rețea.<br>Pasul 2. Se creează `/etc/locale.conf` cu locala aleasă (ex. `LANG=en_US.UTF-8`) și `/etc/vconsole.conf`.<br>Pasul 3. Se verifică sintaxa fișierelor create și existența lor cu `cat` și `test -f`. |
| Rezultat așteptat | Toate fișierele de configurare există, au sintaxa corectă (fără erori la parsare) și conțin valorile specificate în DCS/documentația proiectului. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-15

**Titlu:** Verificarea configurării Systemd (hostname, fișiere de configurare)

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 9.2 - Numele de host al sistemului (`/etc/hostname`) și fișierele de configurare Systemd (`/etc/inputrc`, `/etc/shells`) trebuie create conform Capitolului 9 |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 9 - Cerințe privind configurarea sistemului |
| Modalitate de testare | Pasul 1. Se creează `/etc/hostname` cu numele ales pentru sistem.<br>Pasul 2. Se creează `/etc/inputrc` și `/etc/shells` conform pașilor din Capitolul 9.<br>Pasul 3. Se verifică cu `cat /etc/hostname` și se validează prezența `/bin/bash` în `/etc/shells`. |
| Rezultat așteptat | `/etc/hostname` conține numele de host ales, iar `/etc/shells` listează cel puțin `/bin/bash` ca shell valid. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

### 2.9. LFS Cap. 10 - Realizarea sistemului LFS bootabil

#### TC-16

**Titlu:** Verificarea creării /etc/fstab și compilării kernelului Linux

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 10.1 - Trebuie creat fișierul `/etc/fstab` cu partițiile sistemului și trebuie compilat kernelul Linux-6.18.10 cu suportul necesar pentru hardware-ul țintă |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 10 - Cerințe privind boot-ul sistemului |
| Modalitate de testare | Pasul 1. Se creează `/etc/fstab` cu intrările pentru partiția rădăcină și swap, conform Capitolului 10.<br>Pasul 2. Se configurează kernelul (`make menuconfig` sau `make defconfig` + ajustări), se compilează cu `make` și se instalează modulele și imaginea kernelului (`make modules_install`, copierea în `/boot`).<br>Pasul 3. Se verifică existența `/boot/vmlinuz-6.18.10-lfs-...`, `/boot/System.map-...` și `/boot/config-...`. |
| Rezultat așteptat | `/etc/fstab` conține intrările corecte pentru toate partițiile, iar compilarea kernelului se finalizează fără erori, cu fișierele imaginii kernelului, System.map și config prezente în `/boot`. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |

#### TC-17

**Titlu:** Verificarea instalării și configurării GRUB

| Element | Descriere |
|---|---|
| Cerința DCS | Cerința 10.2 - Bootloader-ul GRUB-2.14 trebuie instalat pe disc și configurat cu `/boot/grub/grub.cfg` pentru a permite pornirea sistemului LFS |
| Referință DCS | DCS al proiectului „Construire sistem LFS”, versiunea 1.0, capitolul 10 - Cerințe privind boot-ul sistemului |
| Modalitate de testare | Pasul 1. Se instalează GRUB pe disc cu `grub-install /dev/sdb` (sau discul corespunzător).<br>Pasul 2. Se generează configurația cu `grub-mkconfig -o /boot/grub/grub.cfg`.<br>Pasul 3. Se verifică existența și conținutul `/boot/grub/grub.cfg` (prezența unei intrări de boot pentru kernelul instalat la TC-16). |
| Rezultat așteptat | `grub-install` se finalizează fără erori, iar `/boot/grub/grub.cfg` conține cel puțin o intrare de meniu care pointează către kernelul și `initramfs`-ul instalate. |
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
| Modalitate de testare | Pasul 1. Se iese din chroot și se demontează sistemele de fișiere virtuale și partiția LFS, conform Capitolului 11.<br>Pasul 2. Se repornește mașina (fizică sau virtuală) și se selectează, din meniul GRUB, intrarea corespunzătoare sistemului LFS nou construit.<br>Pasul 3. Se urmărește procesul de boot (mesajele Systemd) până la afișarea unui prompt de autentificare.<br>Pasul 4. Se autentifică cu un utilizator valid și se rulează `uname -a` pentru confirmare. |
| Rezultat așteptat | Sistemul pornește fără erori critice, Systemd ajunge la `multi-user.target` (sau ținta implicită configurată), este afișat un prompt de autentificare funcțional, iar `uname -a` confirmă kernelul 6.18.10 compilat la TC-16, rulând independent de sistemul host. |
| Rezultat obținut | _(se completează în timpul testării)_ |
| Observații | _(se completează în timpul testării)_ |
| Calificativ test | ☐ PASS &nbsp;&nbsp;&nbsp; ☐ FAILED |
