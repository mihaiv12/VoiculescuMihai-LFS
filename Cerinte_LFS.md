# Plan Cerințe LFS

## 1. Descrierea proiectului

Proiectul își propune construirea de la zero a unui sistem de operare Linux funcțional, folosind documentația "Linux From Scratch" (LFS). Acesta presupune compilarea manuală, pas cu pas, a tuturor componentelor esențiale ale sistemului — nucleul Linux, biblioteca C standard, compilatorul, utilitarele de bază și shell-ul — pornind exclusiv de la codul sursă, în interiorul chroot.

Proiectul este structurat în următoarele etape principale:

- Pregatirea sistemului
- Creeare Cross ToolChain si Temporary Tools
- Construirea sistemului LFS
- Configurarea sistemului
- Sistemul LFS este făcut bootabil

## 2. Cerințe funcționale

Cerințele funcționale descriu capabilitățile concrete pe care trebuie să le îndeplinească sistemul rezultat și procesul de construcție al acestuia.

| Cod | Descriere |
| --- | --- |
| **CF01** | Sistemul trebuie să permită crearea și partiționarea unui spațiu de stocare dedicat (disc/partiție virtuală) pentru instalarea LFS. |
| **CF02** | Sistemul trebuie să descarce, verifice (checksum) și extragă pachetele sursă necesare (Binutils, GCC, Glibc, Linux Kernel etc.). |
| **CF03** | Sistemul trebuie să construiască un toolchain temporar, izolat de bibliotecile sistemului gazdă, capabil să compileze componentele finale. |
| **CF04** | Sistemul trebuie să permită intrarea într-un mediu chroot izolat de sistemul gazdă, pentru finalizarea construcției. |
| **CF05** | Sistemul trebuie să compileze și instaleze utilitarele de bază (coreutils, bash, grep, sed, gawk, findutils, tar, gzip, xz etc.). |
| **CF06** | Sistemul trebuie să configureze scripturile de inițializare (init scripts) și gestionarea rețelei de bază. |
| **CF07** | Sistemul trebuie să compileze și să configureze nucleul Linux (kernel) cu suport pentru hardware-ul/mediul de virtualizare țintă. |
| **CF08** | Sistemul trebuie să instaleze și configureze un bootloader (GRUB) capabil să pornească noul sistem. |
| **CF09** | Sistemul rezultat trebuie să permită autentificarea unui utilizator și accesul la un shell funcțional (Bash) după pornire. |

## 3. Cerințe nefuncționale

Cerințele nefuncționale definesc constrângerile generale ale sistemului și ale procesului de dezvoltare, fără a descrie funcționalități directe.

| Cod | Descriere |
| --- | --- |
| **CNF01** | Performanță: procesul complet de compilare (toolchain + sistem final + kernel) trebuie să se finalizeze într-un interval de timp rezonabil, în funcție de resursele mașinii gazdă/virtuale alocate. |
| **CNF02** | Portabilitate: instrucțiunile și scripturile de construcție trebuie să fie compatibile cu arhitectura x86_64. |
| **CNF03** | Fiabilitate: fiecare etapă de compilare trebuie verificată (teste incluse în pachete, unde este posibil) înainte de a trece la etapa următoare. |
| **CNF04** | Izolare: mediul de construcție (chroot) trebuie să fie complet izolat de sistemul gazdă, pentru a evita contaminarea cu biblioteci sau binare externe. |
| **CNF05** | Reproductibilitate: procesul trebuie documentat astfel încât să poată fi reprodus identic pe o altă mașină, folosind aceleași versiuni de pachete. |
| **CNF06** | Mentenabilitate: structura de directoare și scripturile utilizate trebuie organizate clar, cu denumiri sugestive, pentru a facilita depanarea și extinderea ulterioară. |
| **CNF07** | Resurse: procesul de construcție trebuie să funcționeze cu un consum rezonabil de spațiu de stocare (~10GB) și memorie RAM. |
| **CNF08** | Documentare: fiecare etapă a proiectului trebuie însoțită de documentație tehnică (comenzi utilizate, decizii de configurare, erori întâlnite și rezolvări). |

## 4. Concluzie

Prin parcurgerea etapelor descrise, proiectul oferă o înțelegere practică și aprofundată a arhitecturii unui sistem de operare, pornind de la compilarea toolchain-ului și până la un sistem funcțional, capabil de boot independent.
