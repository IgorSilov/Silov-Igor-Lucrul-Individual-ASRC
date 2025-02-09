# Lab.2: Securizarea routerului pentru acces administrativ

## Cuprins

1. [Partea 1: Configurare de bază a dispozitivului de rețea](#partea-1-configurare-de-bază-a-dispozitivului-de-rețea)
2. [Partea 2: Control acces administrativ pentru routere](#partea-2-control-acces-administrativ-pentru-routere)
3. [Partea 3: Configurarea rolurilor administrative](#partea-3-configurarea-rolurilor-administrative)
4. [Partea 4: Configurarea raportărilor de reziliență și management Cisco IOS](#partea-4-configurarea-raportărilor-de-reziliență-și-management-cisco-ios)
5. [Partea 5: Configurarea funcțiilor de securitate automatizate](#partea-5-configurarea-funcțiilor-de-securitate-automatizate)
6. [Concluzii](#concluzii)
7. [Resurse și Bibliografie](#resurse-și-bibliografie)

---

## Partea 1: Configurare de bază a dispozitivului de rețea

### Obiective

- Cablați rețeaua conform topologiei furnizate (utilizați cabluri seriale, Ethernet și rollover conform instrucțiunilor laboratorului).
- Configurați adresarea IP de bază pentru routere și computere.
- Configurați rutarea statică (inclusiv rutele implicite).
- Verificați conectivitatea între gazde și routere.

### Pași și comenzi exemplu

1. **Cablare**  
   Asigurați-vă că toate cablurile sunt conectate conform diagramei de rețea:
    - **Serial**: Conectați routerele între ele (ex.: R1 ↔ R2, R2 ↔ R3).
    - **Ethernet**: Conectați routerele la switch-uri și PC-urile la switch-uri.

2. **Configurarea adresării IP de bază**

   **Pe fiecare router**, intrați în modul de configurare globală și configurați interfețele relevante. Exemplu pentru R1:

   ```cisco
   R1> enable
   R1# configure terminal
   R1(config)# hostname R1
   R1(config)# interface FastEthernet0/0
   R1(config-if)# ip address 192.168.1.1 255.255.255.0
   R1(config-if)# no shutdown
   R1(config-if)# exit
   ```

   Procedați similar și pentru celelalte routere (R2, R3) și asigurați-vă că PC-urile au configurate adrese IP în aceeași rețea ca interfețele la care sunt conectate.

3. **Configurarea rutării statice și a rutei implicite**

   De exemplu, pe R1, dacă rețeaua 192.168.2.0/24 este accesibilă prin R2 (cu IP-ul 192.168.1.2 pe interfața legată de R1), configurați:

   ```cisco
   R1(config)# ip route 192.168.2.0 255.255.255.0 192.168.1.2
   ```

   Pentru o rută implicită (default route), de exemplu pe R3:

   ```cisco
   R3(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.1
   ```

4. **Verificarea conectivității**

   După configurare, folosiți comanda `ping` pentru a verifica conectivitatea:

   ```cisco
   R1# ping 192.168.1.2
   R1# ping 192.168.2.1
   ```

   De asemenea, puteți utiliza comanda `traceroute` pentru a urmări calea între dispozitive.

---

## Partea 2: Control acces administrativ pentru routere

### Obiective

- Configurați și criptați toate parolele.
- Configurați un banner de avertizare la conectare.
- Implementați măsuri de securitate îmbunătățită pentru parolele de utilizator și accesul VTY.
- Configurați un server SSH pe un router și verificați conectivitatea de la un client SSH.

### Configurarea parolelor și a bannerului

1. **Criptarea parolelor de configurare**

   Se activează criptarea parolelor introduse în configurație:

   ```cisco
   R1(config)# service password-encryption
   ```

2. **Setarea parolei de acces la modul privilegiat**

   ```cisco
   R1(config)# enable secret S3cr3tEn@bled
   ```

3. **Configurarea accesului la consola**

   ```cisco
   R1(config)# line console 0
   R1(config-line)# password C0nS0leP@ss
   R1(config-line)# login
   R1(config-line)# exit
   ```

4. **Configurarea accesului VTY (pentru acces remote)**

   ```cisco
   R1(config)# line vty 0 4
   R1(config-line)# password VTYP@ss
   R1(config-line)# login
   R1(config-line)# transport input ssh
   R1(config-line)# exit
   ```

5. **Setarea unui banner de avertizare**

   ```cisco
   R1(config)# banner motd #
   Avertisment: Accesul neautorizat este strict interzis!
   # 
   ```

### Configurarea autentificării și a accesului SSH

1. **Configurarea unui utilizator local cu privilegii**

   ```cisco
   R1(config)# username admin secret Adm1nP@ss
   ```

2. **Setarea unui nume de domeniu (necesar pentru generarea cheilor SSH)**

   ```cisco
   R1(config)# ip domain-name exemplu.com
   ```

3. **Generarea cheilor RSA pentru SSH**

   ```cisco
   R1(config)# crypto key generate rsa
   ```
   Veți primi un prompt pentru mărimea cheii; de exemplu, introduceți `1024`.

4. **Verificarea configurației SSH**

   Puteți verifica setările SSH cu:

   ```cisco
   R1# show ip ssh
   ```

5. **Testarea conectivității SSH**

   Utilizați un client SSH (ex. PuTTY) de pe PC-A sau PC-C pentru a vă conecta la adresa IP a routerului:

    - **Host Name/IP:** *[IP-ul interfeței configurate pentru management]*
    - **Port:** 22  
      Logați-vă cu utilizatorul `admin` și parola `Adm1nP@ss`.

---

## Partea 3: Configurarea rolurilor administrative

### Obiective

- Creați vizualizări (views) pentru roluri multiple cu privilegii diferite.
- Verificați și contrastați nivelurile de acces oferite de fiecare view.

### Configurarea vizualizărilor de roluri (RBAC)

Cisco IOS permite configurarea accesului pe bază de roluri prin intermediul funcționalității _parser view_.

1. **Crearea unui view pentru utilizatori cu acces restricționat (ex.: vizualizare doar a unor comenzi de monitorizare)**

   ```cisco
   R1(config)# parser view VIEW_MONITOR
   R1(config-view)# secret 5 $1$exMP$abc123def456ghi789
   R1(config-view)# commands exec include show ip interface brief
   R1(config-view)# commands exec include show version
   R1(config-view)# exit
   ```

2. **Crearea unui view pentru utilizatori cu acces avansat (ex.: configurare parțială)**

   ```cisco
   R1(config)# parser view VIEW_ADVANCED
   R1(config-view)# secret 5 $1$an0th3r$xyz987lmn654opq321
   R1(config-view)# commands exec include configure terminal
   R1(config-view)# commands exec include show running-config
   R1(config-view)# commands exec include debug
   R1(config-view)# exit
   ```

3. **Testarea și verificarea vizualizărilor**

   Pentru a accesa un anumit view, utilizați comanda:

   ```cisco
   R1# enable view VIEW_MONITOR
   ```

   Comparați comenzile disponibile în fiecare view și asigurați-vă că utilizatorii au acces doar la comenzile permise.

---

## Partea 4: Configurarea raportărilor de reziliență și management Cisco IOS

### Obiective

- Asigurați backup-ul fișierelor de imagine și configurație Cisco IOS.
- Configurați routerul ca sursă de timp (NTP) pentru sincronizarea altor dispozitive.
- Configurați suportul pentru Syslog și SNMP pentru raportarea evenimentelor.

### Configurarea backup-ului imaginii și configurației IOS

1. **Salvarea configurației curente în memoria de pornire**

   ```cisco
   R1# copy running-config startup-config
   ```

2. **Backup-ul imaginii IOS**  
   Transferați fișierul de imagine către un server TFTP folosind:

   ```cisco
   R1# copy flash:c1841-advipservicesk9-mz.124-20.T.bin tftp:
   ```
   Urmați instrucțiunile pentru a specifica adresa IP a serverului TFTP și calea de destinație.

### Configurarea NTP

1. **Setarea routerului ca server NTP (master)**

   ```cisco
   R1(config)# ntp master 3
   ```
   (Nivelul NTP poate fi ajustat după preferințe.)

2. **Verificarea stării NTP**

   ```cisco
   R1# show ntp status
   ```

### Configurarea Syslog

1. **Activarea logging-ului în buffer și setarea nivelului de detaliu**

   ```cisco
   R1(config)# logging buffered 64000 debugging
   ```

2. **Configurarea unui host Syslog**

   Presupunând că serverul Syslog (ex. Kiwi sau Tftpd32) are IP-ul 192.168.1.100:

   ```cisco
   R1(config)# logging host 192.168.1.100
   ```

3. **Testarea: Efectuați o modificare (ex.: activați/ dezactivați o interfață) și monitorizați mesajele pe serverul Syslog.**

### Configurarea SNMP pentru raportarea capcanelor

1. **Setarea comunității SNMP (ex. read-only)**

   ```cisco
   R1(config)# snmp-server community public RO
   ```

2. **Activarea capcanelor SNMP**

   ```cisco
   R1(config)# snmp-server enable traps
   ```

3. **Verificarea funcționării SNMP**  
   Folosiți un manager SNMP pentru a monitoriza capcanele generate la modificările de configurare.

---

## Partea 5: Configurarea funcțiilor de securitate automatizate

### Obiective

- Blocarea automată a accesului pe router folosind AutoSecure.
- Utilizarea instrumentului Audit de securitate SDM pentru identificarea vulnerabilităților și blocarea serviciilor ne-necesare.
- Compararea configurației generate de AutoSecure cu cea generată prin SDM.

### Utilizarea AutoSecure

1. **Executarea comenzii AutoSecure**

   ```cisco
   R1(config)# auto secure
   ```
   Veți primi o serie de întrebări (ex.: configurarea liniilor console/VTY, setarea parolelor, blocarea serviciilor neutilizate etc.). Răspundeți conform politicii de securitate a organizației sau după recomandările laboratorului.

2. **Verificarea configurației generate**

   ```cisco
   R1# show running-config
   ```
   Analizați modificările aduse de AutoSecure (de exemplu, configurarea de ACL-uri, dezactivarea unor protocoale neutilizate etc.).

### Utilizarea SDM (Security Device Manager)

1. **Lansarea SDM**  
   Accesați SDM pe routerul R1 (prin interfața web sau aplicația dedicată, conform cerințelor laboratorului).

2. **Auditul de securitate**  
   Utilizați funcția „Security Audit” din SDM pentru a identifica vulnerabilitățile curente și serviciile neutilizate care pot fi dezactivate.

3. **Compararea configurațiilor**
    - Notați configurația generată de AutoSecure.
    - Comparați cu setările recomandate de SDM.
    - Identificați diferențele (ex.: ACL-uri, configurări suplimentare pentru liniile de acces, logging, etc.) și discutați avantajele fiecărei metode.

---
