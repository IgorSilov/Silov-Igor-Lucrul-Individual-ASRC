# LAB. 5: Configurarea unui sistem de prevenire a intruziunilor (IPS) folosind CLI și SDM

## Cuprins

1. [Introducere](#introducere)
2. [Partea 1: Configurare de bază a routerului](#partea-1-configurare-de-bază-a-routerului)
3. [Partea 2: Configurarea unui sistem de prevenire a intruziunilor IOS (IPS) folosind CLI](#partea-2-configurarea-unui-sistem-de-prevenire-a-intruziunilor-ios-ips-folosind-cli)
4. [Partea 3: Configurarea unui sistem de prevenire a intruziunilor (IPS) folosind SDM](#partea-3-configurarea-unui-sistem-de-prevenire-a-intruziunilor-ips-folosind-sdm)
5. [Concluzii](#concluzii)
6. [Resurse și Bibliografie](#resurse-și-bibliografie)

---

## Introducere

În acest laborator vei configura sistemul de prevenire a intruziunilor Cisco IOS (IPS), parte integrantă din funcționalitățile de securitate ale Cisco IOS Firewall. IPS monitorizează traficul rețelei pentru a identifica anumite tipare de atac, avertizează administratorul și/sau atenuează atacurile. Deși IPS nu transformă singur routerul într-un firewall complet, atunci când este combinat cu alte caracteristici de securitate poate constitui o apărare puternică.

În cadrul laboratorului vei:
- Configura elementele de bază ale routerului (nume de gazdă, interfețe IP, parole și rutare statică).
- Configura IPS folosind interfața CLI pe unul dintre routere (ex. R1).
- Configura IPS folosind interfața grafică SDM pe alt router (ex. R3).
- Încărca pachetul de semnături IPS dintr-un server TFTP și configura parametrii de criptare (cheia publică crypto).
- Verifica funcționalitatea IPS prin simularea unui atac (cu un instrument de scanare, ex. SuperScan) și monitorizarea mesajelor, inclusiv redirecționarea acestora către un server Syslog.

---

## Partea 1: Configurare de bază a routerului

### Obiective

- Configurarea numelui de gazdă, a adreselor IP pentru interfețe și a parolelor de acces.
- Configurarea rutării statice.

### Pași și comenzi exemplu

1. **Setarea numelui de gazdă și a parolelor**

   Conectează-te la router prin consolă și intră în modul de configurare globală:

   ```cisco
   Router> enable
   Router# configure terminal
   Router(config)# hostname R1
   ```

   Setați parola de acces în modul privilegiat:

   ```cisco
   R1(config)# enable secret S3cr3tEn@ble
   ```

2. **Configurarea interfeței cu adresare IP**

   Exemplu de configurare pentru interfața FastEthernet0/0:

   ```cisco
   R1(config)# interface FastEthernet0/0
   R1(config-if)# ip address 192.168.10.1 255.255.255.0
   R1(config-if)# no shutdown
   R1(config-if)# exit
   ```

3. **Configurarea liniilor de acces (console și VTY)**

   Pentru linia console:

   ```cisco
   R1(config)# line console 0
   R1(config-line)# password C0nS0le!
   R1(config-line)# login
   R1(config-line)# exit
   ```

   Pentru liniile VTY:

   ```cisco
   R1(config)# line vty 0 4
   R1(config-line)# password VTYP@ss
   R1(config-line)# login
   R1(config-line)# exit
   ```

4. **Configurarea rutării statice**

   De exemplu, dacă rețeaua 192.168.20.0/24 este accesibilă printr-un alt router cu IP-ul 192.168.10.2:

   ```cisco
   R1(config)# ip route 192.168.20.0 255.255.255.0 192.168.10.2
   ```

---

## Partea 2: Configurarea unui sistem de prevenire a intruziunilor IOS (IPS) folosind CLI

### Obiective

- Configurarea IPS folosind CLI.
- Încărcarea pachetului de semnături IPS dintr-un server TFTP.
- Modificarea semnăturilor IPS.
- Examinarea configurației IPS și verificarea funcționalității.
- Redirecționarea mesajelor IPS către un server Syslog.

### Pași și comenzi exemplu

1. **Încărcarea pachetului de semnături IPS**

   Presupunând că serverul TFTP are adresa IP `192.168.10.100` și fișierul de semnături se numește `ips-signatures.pkg`, se configurează locația pachetului:

   ```cisco
   R1(config)# ip ips config location tftp://192.168.10.100/ips-signatures.pkg
   ```

2. **Crearea și activarea motorului IPS**

   Se definește un nume pentru motorul IPS și se activează:

   ```cisco
   R1(config)# ip ips name IPS_ENG
   ```

3. **Aplicarea IPS pe o interfață**

   Pentru a proteja traficul pe interfața FastEthernet0/0, se aplică IPS în modul inline:

   ```cisco
   R1(config)# interface FastEthernet0/0
   R1(config-if)# ip ips inline
   R1(config-if)# exit
   ```

4. **Modificarea semnăturilor IPS**

   Pentru a ajusta comportamentul motorului IPS, de exemplu, pentru a seta o anumită semnătură să declanșeze acțiunea "drop" (blocare), se poate utiliza:

   ```cisco
   R1(config)# ip ips signature modify 100001 drop
   ```

   *Notă:* Numărul semnăturii (ex. 100001) este doar un exemplu; consultați documentația specifică pentru semnăturile disponibile și acțiunile recomandate.

5. **Verificarea configurației IPS**

   Pentru a vizualiza configurația și statisticile IPS:

   ```cisco
   R1# show ip ips configuration
   R1# show ip ips statistics
   ```

6. **Configurarea redirecționării mesajelor IPS către un server Syslog**

   Dacă serverul Syslog se află la adresa `192.168.10.101`, configurați:

   ```cisco
   R1(config)# logging host 192.168.10.101
   R1(config)# logging trap debugging
   ```

7. **Testarea funcționalității IPS**

    - Simulează un atac (folosind, de exemplu, SuperScan sau alt instrument de scanare) de pe un PC conectat la rețea.
    - Monitorizează mesajele IPS prin comanda de mai sus și pe serverul Syslog pentru a verifica dacă semnăturile sunt declanșate și dacă acțiunile corespunzătoare (alertare/blocare) sunt aplicate.

---

## Partea 3: Configurarea unui sistem de prevenire a intruziunilor (IPS) folosind SDM

### Obiective

- Configurarea IPS utilizând interfața grafică SDM.
- Încărcarea pachetului de semnături IPS și modificarea acestora prin SDM.
- Examinarea configurației IPS și simularea unui atac.
- Utilizarea monitorului SDM pentru a verifica funcționalitatea IPS.

### Pași și indicații

1. **Accesul la SDM**

    - Pornește SDM de pe PC-C și conectează-te la routerul dedicat IPS (ex. R3) folosind browserul web sau aplicația SDM.
    - Asigură-te că routerul R3 îndeplinește cerințele de memorie (minim 192 MB DRAM) și că rulează Cisco IOS Release 12.4 (20) T sau o versiune ulterioară.

2. **Încărcarea pachetului de semnături IPS**

    - În SDM, navighează la secțiunea dedicată **Intrusion Prevention System (IPS)**.
    - Selectează opțiunea de „Load IPS Signature Package” și introdu adresa serverului TFTP (ex.: `192.168.10.100`) și numele fișierului (`ips-signatures.pkg`).
    - Confirmă încărcarea și așteaptă finalizarea procesului.

3. **Configurarea și modificarea semnăturilor IPS**

    - Utilizând SDM, accesează interfața de configurare IPS.
    - Poți modifica semnăturile IPS (de exemplu, setarea acțiunii pentru o anumită semnătură din „Alert” în „Drop”) prin opțiunea „Edit Signature”.
    - Salvează modificările.

4. **Aplicarea IPS pe interfață**

    - În SDM, configurează IPS pe interfața de protejat (ex.: FastEthernet0/0) prin opțiunea dedicată configurării IPS inline.
    - Asigură-te că parametrii de inspecție (cum ar fi tipurile de trafic inspectat) sunt setați conform politicii de securitate.

5. **Simularea unui atac și testarea IPS**

    - Folosește un instrument de scanare (de exemplu, SuperScan) de pe PC-A sau PC-C pentru a simula un atac asupra routerului.
    - Monitorizează alertele și evenimentele IPS din SDM Monitor pentru a verifica dacă semnăturile modificate sunt declanșate și acțiunile corespunzătoare sunt executate.
    - Verifică statisticile și logurile IPS din SDM pentru a evalua performanța sistemului.

6. **Verificarea funcționalității IPS**

    - Folosește SDM Monitor pentru a vizualiza grafice, statistici și evenimente în timp real.
    - Asigură-te că mesajele IPS sunt, de asemenea, redirecționate către serverul Syslog (dacă această opțiune a fost configurată și în SDM).

---