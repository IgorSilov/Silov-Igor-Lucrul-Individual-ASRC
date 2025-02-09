# LAB. 4: Configurarea firewallurilor bazate pe CBAC și zonă

## Cuprins

1. [Introducere](#introducere)
2. [Partea 1: Configurare de bază a routerului](#partea-1-configurare-de-bază-a-routerului)
    - [Setarea parametrilor de bază](#setarea-parametrilor-de-bază)
    - [Configurarea protocolului de rutare dinamic EIGRP](#configurarea-protocolului-de-rutare-dinamic-eigrp)
    - [Testarea vulnerabilităților cu Nmap](#testarea-vulnerabilităților-cu-nmap)
3. [Partea 2: Configurarea unui firewall CBAC folosind AutoSecure](#partea-2-configurarea-unui-firewall-cbac-folosind-autosecure)
    - [Executarea AutoSecure și generarea configurației CBAC](#executarea-autosecure-și-generarea-configurației-cbac)
    - [Examinarea configurației CBAC](#examinarea-configurației-cbac)
    - [Verificarea funcționalității firewall-ului](#verificarea-funcționalității-firewall-ului)
4. [Partea 3: Configurarea unui firewall de politică bazat pe zonă folosind SDM](#partea-3-configurarea-unui-firewall-de-politică-bazat-pe-zonă-folosind-sdm)
    - [Configurarea firewallului de politică bazat pe zonă](#configurarea-firewallului-de-politică-bazat-pe-zonă)
    - [Examinarea configurației generate](#examinarea-configurației-generate)
    - [Verificarea prin SDM Monitor](#verificarea-prin-sdm-monitor)
5. [Concluzii](#concluzii)
6. [Resurse și Bibliografie](#resurse-și-bibliografie)

---

## Introducere

Acest laborator are ca scop configurarea unui firewall pe un router Cisco folosind două metode:
- **CBAC (Context-Based Access Control)** – o metodă bazată pe inspecția stării pachetelor (Stateful Packet Inspection) care poate fi generată automat prin comanda **AutoSecure**.
- **Firewall bazat pe politică pe bază de zonă (Zone-Based Firewall – ZBF)** – metoda modernă recomandată pentru configurații cu mai multe interfețe sau cerințe variabile, ce poate fi configurată prin interfața SDM.

În plus, se va realiza configurarea de bază a routerului, inclusiv setarea numelui, adreselor IP, parolelor de acces și a protocolului de rutare dinamic EIGRP. Se va utiliza, de asemenea, Nmap pentru a testa eventualele vulnerabilități ale routerului.

---

## Partea 1: Configurare de bază a routerului

### Setarea parametrilor de bază

1. **Acces la router și configurarea numelui gazdei:**  
   Conectați-vă la router prin consolă și intrați în modul de configurare globală:

   ```cisco
   Router> enable
   Router# configure terminal
   Router(config)# hostname R1
   ```

2. **Configurarea interfeței cu adresare IP și activare:**

   De exemplu, pe interfața FastEthernet0/0:

   ```cisco
   R1(config)# interface FastEthernet0/0
   R1(config-if)# ip address 192.168.10.1 255.255.255.0
   R1(config-if)# no shutdown
   R1(config-if)# exit
   ```

3. **Setarea parolelor de acces (console, VTY, enable):**

    - Pentru accesul în modul privilegiat:

      ```cisco
      R1(config)# enable secret S3cr3tEn@ble
      ```

    - Pentru linia console:

      ```cisco
      R1(config)# line console 0
      R1(config-line)# password C0nS0le!
      R1(config-line)# login
      R1(config-line)# exit
      ```

    - Pentru liniile VTY:

      ```cisco
      R1(config)# line vty 0 4
      R1(config-line)# password VTYP@ss
      R1(config-line)# login
      R1(config-line)# exit
      ```

### Configurarea protocolului de rutare dinamic EIGRP

1. **Activarea EIGRP (exemplu pentru AS-ul 100):**

   ```cisco
   R1(config)# router eigrp 100
   R1(config-router)# network 192.168.10.0 0.0.0.255
   R1(config-router)# no auto-summary
   R1(config-router)# exit
   ```

   *Notă:* Configurați și pe celelalte routere din rețea, asigurându-vă că rețelele interconectate sunt incluse în declarația `network`.

### Testarea vulnerabilităților cu Nmap

1. **Utilizarea Nmap pe un PC (ex. PC-A sau PC-C):**

   De pe un PC cu Nmap instalat, efectuați o scanare de tip SYN pentru a identifica porturile deschise și eventuale vulnerabilități ale routerului R1:

   ```bash
   nmap -sS 192.168.10.1
   ```

2. **Interpretarea rezultatelor:**
    - Verificați dacă porturile neautorizate sunt deschise.
    - Identificați serviciile care rulează pe router și evaluați dacă acestea pot fi exploatate.

---

## Partea 2: Configurarea unui firewall CBAC folosind AutoSecure

### Executarea AutoSecure și generarea configurației CBAC

1. **Rularea comenzii AutoSecure:**

   În modul de configurare globală, executați:

   ```cisco
   R1(config)# auto secure
   ```

2. **Răspunsul la întrebările interactive:**  
   Comanda **AutoSecure** va solicita răspunsuri pentru configurarea liniilor console/VTY, pentru criptarea parolelor și pentru blocarea serviciilor neutilizate. Răspundeți conform politicii de securitate (de ex. acceptați să configurați un firewall CBAC care să inspecteze traficul de la/spre interfețele externe și să permită traficul de la rețeaua internă).

### Examinarea configurației CBAC

1. **Vizualizarea configurației generate:**

   După rularea comenzii, utilizați:

   ```cisco
   R1# show running-config
   ```

    - Verificați secțiunile referitoare la ACL-uri, comenzi de inspect și configurările AutoSecure.
    - Veți observa, de exemplu, ACL-uri aplicate pe interfața de ieșire pentru a inspecta traficul.

### Verificarea funcționalității firewall-ului

1. **Testare:**
    - Efectuați conexiuni de la un host extern și monitorizați dacă traficul este inspectat și controlat conform politicii definite.
    - Puteți utiliza Nmap pentru a verifica dacă anumite porturi sau servicii sunt blocate de firewall-ul CBAC.

---

## Partea 3: Configurarea unui firewall de politică bazat pe zonă folosind SDM

### Configurarea firewallului de politică bazat pe zonă

1. **Accesul la SDM:**  
   Conectați-vă la router prin SDM (Security Device Manager) de pe PC-A sau PC-C, folosind browserul web sau aplicația dedicată.

2. **Configurarea zonelor:**  
   În SDM, navigați la secțiunea dedicată **Firewall / Zone-Based Policy** și creați următoarele zone:
    - **Zone "INSIDE"** – pentru interfețele interne.
    - **Zone "OUTSIDE"** – pentru interfețele externe.

3. **Asocierea interfețelor cu zonele:**

   De exemplu:
    - Interfața FastEthernet0/1: membră a zonei **INSIDE**
    - Interfața FastEthernet0/0: membră a zonei **OUTSIDE**

4. **Configurarea politicii de inspecție:**
    - Creați un **zone-pair** care să asocieze zona **INSIDE** ca sursă și **OUTSIDE** ca destinație.
    - Definește o **policy map** (de tip inspect) în care să specificați ce tipuri de trafic (ex. HTTP, FTP, etc.) vor fi inspectate.
    - Aplicați politica pe interfața corespunzătoare.

   *Exemplu de configurație (generată de SDM):*

   ```cisco
   zone security INSIDE
   zone security OUTSIDE

   zone-pair security ZP_IN_OUT source INSIDE destination OUTSIDE
    service-policy type inspect ZBF_POLICY

   class-map type inspect match-any HTTP-TRAFFIC
    match protocol http

   policy-map type inspect ZBF_POLICY
    class type inspect HTTP-TRAFFIC
     inspect

   interface FastEthernet0/1
    zone-member security INSIDE

   interface FastEthernet0/0
    zone-member security OUTSIDE
   ```

### Examinarea configurației generate

1. **Verificarea configurației:**  
   După ce ați aplicat politica prin SDM, examinați configurația rulată cu:

   ```cisco
   R1# show running-config
   ```

    - Căutați secțiunile referitoare la „zone security”, „zone-pair” și „policy-map” pentru a vă asigura că totul este configurat conform politicii de securitate dorite.

### Verificarea prin SDM Monitor

1. **Utilizarea SDM Monitor:**  
   În interfața SDM, accesați secțiunea de **Monitor** pentru firewall:
    - Verificați statisticile traficului, evenimentele de inspecție și eventualele alerte generate.
    - Asigurați-vă că traficul dintre zonele **INSIDE** și **OUTSIDE** este procesat conform regulilor definite.

---