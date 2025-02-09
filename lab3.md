# LAB. 3: Asigurarea accesului administrativ folosind AAA și RADIUS

## Cuprins

1. [Introducere](#introducere)
2. [Partea 1: Configurare de bază a dispozitivului de rețea](#partea-1-configurare-de-bază-a-dispozitivului-de-rețea)
3. [Partea 2: Configurați autentificarea locală](#partea-2-configurați-autentificarea-locală)
4. [Partea 3: Configurarea autentificării locale folosind AAA](#partea-3-configurarea-autentificării-locale-folosind-aaa)
5. [Partea 4: Configurarea autentificării centralizate folosind AAA și RADIUS](#partea-4-configurarea-autentificării-centralizate-folosind-aaa-și-radius)
6. [Concluzii](#concluzii)
7. [Resurse și Bibliografie](#resurse-și-bibliografie)

---

## Introducere

Acest laborator are ca scop securizarea accesului administrativ la routere folosind:
- Configurări de bază (nume de gazdă, adresare IP, parole, rutare statică),
- Autentificare locală pe console, VTY și linia AUX,
- Implementarea AAA (Authentication, Authorization, Accounting) cu baza de date locală și, ulterior,
- Utilizarea unui server RADIUS pentru autentificare centralizată.

Această abordare asigură un control mai granular și scalabil al accesului administrativ în rețelele mari, eliminând necesitatea de a configura manual fiecare router cu aceleași setări locale.

---

## Partea 1: Configurare de bază a dispozitivului de rețea

### Obiective

- Configurarea setărilor de bază: numele gazdei, adresarea IP pe interfețe, parolele de acces.
- Configurarea rutării statice.

### Pași și comenzi exemplu

1. **Setarea numelui gazdei și configurarea interfețelor**

   Conectați-vă la router prin consolă și intrați în modul de configurare globală:

   ```cisco
   Router> enable
   Router# configure terminal
   Router(config)# hostname R1
   ```

   Configurați o interfață (ex.: FastEthernet0/0) cu o adresă IP și activați-o:

   ```cisco
   R1(config)# interface FastEthernet0/0
   R1(config-if)# ip address 192.168.1.1 255.255.255.0
   R1(config-if)# no shutdown
   R1(config-if)# exit
   ```

2. **Configurarea parolelor de acces la modul privilegiat**

   Setați o parolă secretă pentru modul privilegiat:

   ```cisco
   R1(config)# enable secret S3cr3tPrivil3ge
   ```

3. **Configurarea rutării statice**

   Dacă, de exemplu, rețeaua 192.168.2.0/24 se află în spatele unui alt router (ex.: cu IP-ul 192.168.1.2 pe interfața conectată la R1), configurați o rută statică:

   ```cisco
   R1(config)# ip route 192.168.2.0 255.255.255.0 192.168.1.2
   ```

4. **Verificarea conectivității**

   Folosiți comanda `ping` pentru a testa conectivitatea:

   ```cisco
   R1# ping 192.168.1.2
   ```

---

## Partea 2: Configurați autentificarea locală

### Obiective

- Crearea unei baze de date locale de utilizatori.
- Configurarea autentificării locale pentru liniile console, VTY și AUX.
- Testarea autentificării locale.

### Configurarea bazei de date locale și a liniilor de acces

1. **Configurarea unui utilizator în baza de date locală**

   De exemplu, se creează un utilizator numit `admin`:

   ```cisco
   R1(config)# username admin password cisco123
   ```

2. **Configurarea liniilor de consolă, VTY și AUX pentru a utiliza autentificarea locală**

   **Pentru linia console:**

   ```cisco
   R1(config)# line console 0
   R1(config-line)# login local
   R1(config-line)# exit
   ```

   **Pentru liniile VTY:**

   ```cisco
   R1(config)# line vty 0 4
   R1(config-line)# login local
   R1(config-line)# exit
   ```

   **Pentru linia AUX (dacă este prezentă):**

   ```cisco
   R1(config)# line aux 0
   R1(config-line)# login local
   R1(config-line)# exit
   ```

3. **Testarea configurației**

   Deconectați-vă și reconectați-vă la router. Ar trebui să vi se solicite un nume de utilizator și o parolă. Introduceți `admin` și `cisco123` pentru a verifica funcționarea autentificării locale.

---

## Partea 3: Configurarea autentificării locale folosind AAA

### Obiective

- Activarea AAA pe router.
- Configurarea autentificării locale prin intermediul AAA folosind baza de date locală.
- Configurarea opțională cu SDM (Security Device Manager) pentru AAA.

### Pași și comenzi

1. **Activarea modelului AAA**

   Se activează modelul AAA pe router:

   ```cisco
   R1(config)# aaa new-model
   ```

2. **Configurarea autentificării AAA pentru accesul la router**

   Configurați metoda implicită de autentificare pentru login să folosească baza de date locală:

   ```cisco
   R1(config)# aaa authentication login default local
   ```

   *Notă:* Această comandă spune routerului să utilizeze în primul rând baza de date locală pentru autentificare. În cazul în care se utilizează SDM, această setare poate fi realizată prin interfața grafică conform specificațiilor laboratorului.

3. **Testarea configurației AAA**

   Ieșiți din sesiune și reconectați-vă la router. Vi se va solicita, la fel ca înainte, un nume de utilizator și o parolă. Introduceți `admin` și `cisco123` pentru a verifica că autentificarea prin AAA (bazată pe baza locală) funcționează corect.

---

## Partea 4: Configurarea autentificării centralizate folosind AAA și RADIUS

### Obiective

- Instalarea și configurarea unui server RADIUS pe un computer extern (PC-A).
- Configurarea utilizatorilor pe serverul RADIUS.
- Configurarea routerului pentru a utiliza serverul RADIUS în procesul de autentificare, atât prin CLI, cât și prin SDM.
- Testarea funcționării AAA cu RADIUS.

### Pași și comenzi

1. **Instalarea serverului RADIUS**

    - **Pe PC-A:** Instalați și configurați software-ul de server RADIUS (de exemplu, FreeRADIUS, Microsoft NPS sau altă soluție similară).
    - Configurați utilizatorii pe serverul RADIUS (ex.: creați un cont de utilizator cu numele `radiususer` și o parolă, de exemplu, `radpass123`).

2. **Configurarea routerului pentru a utiliza serverul RADIUS**

   Să presupunem că adresa IP a serverului RADIUS este `192.168.1.100` și secretul comun este `radius123`.

   a. **Activarea AAA (dacă nu a fost deja activată)**

   ```cisco
   R1(config)# aaa new-model
   ```

   b. **Configurarea unui grup de servere RADIUS**

   Pentru versiuni Cisco IOS moderne, se poate folosi comanda „aaa group server radius”:

   ```cisco
   R1(config)# aaa group server radius RADSRV
   R1(config-group)# server 192.168.1.100 auth-port 1812 acct-port 1813
   R1(config-group)# exit
   ```

   *Alternativ*, pentru unele versiuni IOS, se poate utiliza și comanda:

   ```cisco
   R1(config)# radius-server host 192.168.1.100 key radius123
   ```

   c. **Setarea metodei de autentificare AAA pentru login**

   Configurați autentificarea astfel încât routerul să utilizeze mai întâi serverul RADIUS, iar în caz de eșec, să cadă pe baza de date locală:

   ```cisco
   R1(config)# aaa authentication login default group radius local
   ```

3. **Configurarea AAA prin SDM**

   Dacă preferați să utilizați interfața grafică SDM:
    - Accesați SDM pe router.
    - Navigați la secțiunea pentru configurarea AAA.
    - Specificați serverul RADIUS (`192.168.1.100`), porturile de autentificare și contabilitate (1812/1813) și introduceți secretul `radius123`.
    - Configurați metoda de autentificare pentru liniile de acces să folosească „RADIUS (cu fallback la local)” și salvați configurația.

4. **Testarea configurației AAA RADIUS**

    - Deconectați-vă și reconectați-vă la router.
    - În momentul autentificării, introduceți numele de utilizator și parola definite pe serverul RADIUS (ex.: `radiususer` / `radpass123`).
    - Dacă autentificarea reușește, aceasta indică o configurare corectă a integrării AAA cu serverul RADIUS.
    - În cazul în care serverul RADIUS nu este accesibil, routerul ar trebui să ceară autentificare prin baza de date locală (dacă aceasta a fost configurată ca fallback).

---