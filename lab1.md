# LAB.1: Cercetarea atacurilor din rețea și instrumente de audit de securitate

## Cuprins

- [Introducere](#introducere)
- [Partea 1: Cercetarea atacurilor din rețea](#partea-1-cercetarea-atacurilor-din-rețea)
    - [Atac selectat: DDoS (Distributed Denial of Service)](#atac-selectat-ddos-distributed-denial-of-service)
    - [Descrierea atacului](#descrierea-atacului)
    - [Impactul atacului](#impactul-atacului)
    - [Tehnici de atenuare](#tehnici-de-atenuare)
    - [Concluzii - Partea 1](#concluzii---partea-1)
- [Partea 2: Cercetarea instrumentelor de audit de securitate](#partea-2-cercetarea-instrumentelor-de-audit-de-securitate)
    - [Instrument selectat: Nmap](#instrument-selectat-nmap)
    - [Descriere și caracteristici](#descriere-și-caracteristici)
    - [Utilizări și exemple](#utilizări-și-exemple)
    - [Avantaje și limitări](#avantaje-și-limitări)
    - [Concluzii și recomandări - Partea 2](#concluzii-și-recomandări---partea-2)
- [Bibliografie](#bibliografie)

---

## Introducere

Acest laborator este împărțit în două secțiuni principale:

1. **Cercetarea atacurilor din rețea:** Se analizează diverse atacuri de rețea care au avut loc, se selectează un atac specific și se elaborează un raport care descrie modul de atac, impactul și posibilele măsuri de atenuare.
2. **Cercetarea instrumentelor de audit de securitate:** Se analizează instrumentele folosite pentru auditul securității rețelei, se selectează un instrument și se pregătește un rezumat (one-page summary) cu informații relevante despre acesta, pentru a fi prezentat la laborator.

---

## Partea 1: Cercetarea atacurilor din rețea

### Atac selectat: DDoS (Distributed Denial of Service)

Pentru analiza din această secțiune, vom alege atacul de tip **DDoS (Distributed Denial of Service)**. Acest tip de atac este unul dintre cele mai întâlnite și poate avea consecințe majore asupra operațiunilor unei rețele.

### Descrierea atacului

- **Ce este un atac DDoS?**  
  Atacul DDoS implică trimiterea unui volum mare de trafic de la mai multe surse compromise către o țintă (de exemplu, un server, o rețea sau un site web) cu scopul de a suprasolicita resursele sistemului și de a-l face inaccesibil utilizatorilor legitimi.

- **Modul de desfășurare:**
    - **Recrutarea botnet-ului:** Atacatorii compromit o serie de dispozitive (calculatoare, IoT, etc.) folosind diverse metode (exploatarea vulnerabilităților, phishing, etc.) pentru a le transforma în „zombi” ce vor forma un botnet.
    - **Coordonarea atacului:** Prin intermediul unor comenzi centralizate, atacatorii direcționează botnet-ul să trimită un volum masiv de cereri către țintă.
    - **Suprasolicitarea țintei:** Fluxul de trafic excesiv cauzează blocarea resurselor serverului sau rețelei, ducând la întreruperi semnificative ale serviciilor.

- **Exemplu notoriu:**  
  Atacul asupra serviciului DNS al companiei Dyn în 2016, alimentat de botnet-ul Mirai, a dus la întreruperea accesului la site-uri importante precum Twitter, Reddit, și Spotify.

### Impactul atacului

- **Pierderea de date sensibile:**  
  Deși atacurile DDoS nu sunt în mod tradițional orientate spre furtul de date, ele pot distrage atenția de la alte activități rău intenționate, oferind un prilej atacatorilor să exploateze alte vulnerabilități.

- **Întârzieri și întreruperi ale serviciilor:**
    - **Perioade extinse de indisponibilitate:** Utilizatorii legitimi nu pot accesa resursele rețelei.
    - **Pierderea productivității:** În mediile organizaționale, întreruperea accesului la resurse poate conduce la pierderi financiare semnificative și scăderea încrederii clienților.

- **Costuri ridicate:**  
  Investiții suplimentare în infrastructura de securitate și remedieri post-atac.

### Tehnici de atenuare

Pentru a preveni sau a diminua impactul unui atac DDoS, se pot implementa următoarele măsuri:

- **Monitorizarea traficului:**  
  Utilizarea sistemelor de detecție a anomaliilor care pot alerta administratorii de rețea la activități neobișnuite.

- **Filtrarea traficului:**  
  Configurarea firewall-urilor și a sistemelor de prevenire a intruziunilor (IPS) pentru a bloca pachetele de date suspecte.

- **Rate limiting:**  
  Limitarea numărului de cereri pe care un server le poate procesa într-o anumită perioadă de timp.

- **Utilizarea serviciilor de mitigare DDoS:**  
  Colaborarea cu furnizori specializați care oferă soluții pentru absorbția și redistribuirea traficului malițios.

- **Redundanță și scalare:**  
  Implementarea arhitecturilor distribuite, cum ar fi rețelele de livrare a conținutului (CDN), care pot dispersa traficul și pot reduce impactul asupra unui singur punct de eșec.

### Concluzii - Partea 1

Atacurile de tip DDoS reprezintă o amenințare semnificativă pentru infrastructura rețelelor, având capacitatea de a cauza întreruperi majore și pierderi economice. Implementarea unui plan robust de monitorizare, filtrare și colaborarea cu experți în mitigarea atacurilor sunt măsuri esențiale pentru protejarea rețelelor. Educația continuă și testele periodice de penetrare pot ajuta la identificarea și remedierea vulnerabilităților înainte ca acestea să fie exploatate.

---

## Partea 2: Cercetarea instrumentelor de audit de securitate

### Instrument selectat: Nmap

Pentru această parte a laboratorului, am ales să investigăm **Nmap (Network Mapper)**, un instrument de audit și scanare a rețelelor, folosit pe scară largă pentru identificarea gazdelor, porturilor deschise și serviciilor disponibile.

### Descriere și caracteristici

- **Ce este Nmap?**  
  Nmap este un instrument open-source care permite scanarea rețelelor pentru a descoperi dispozitive active, porturi deschise, servicii care rulează pe acestea și, uneori, versiuni de software.

- **Caracteristici principale:**
    - **Scanare de porturi:** Identifică porturile deschise pe o țintă.
    - **Detecția sistemului de operare:** Estimează ce sistem de operare rulează pe gazdă.
    - **Scanare de rețea:** Descoperă host-uri active într-o rețea dată.
    - **Scripting Engine (NSE):** Permite rularea de scripturi pentru a identifica vulnerabilități specifice sau pentru a aduna informații suplimentare.
    - **Scanări avansate:** Suportă diverse tehnici de scanare (SYN, TCP, UDP, etc.) pentru a evita detecția și pentru a adapta scanarea la nevoile specifice de securitate.

### Utilizări și exemple

- **Utilizări practice:**
    - **Auditul securității rețelei:** Administratori de rețea folosesc Nmap pentru a identifica dispozitive neautorizate sau vulnerabile.
    - **Planificarea de penetrare:** Penetrarea testelor (penetration testing) folosesc Nmap pentru a cartografia rețeaua țintă.
    - **Monitorizarea continuă:** Scanări periodice ajută la verificarea integrității rețelei și la detectarea modificărilor neautorizate.

- **Exemplu de comandă de scanare:**

  ```bash
  nmap -sS -O 192.168.1.0/24
  ```

  *Explicație:*
    - `-sS`: Utilizează o scanare SYN (scanare "half-open") pentru a identifica porturile deschise.
    - `-O`: Activează detecția sistemului de operare.
    - `192.168.1.0/24`: Specifică gama de adrese IP care vor fi scanate.

### Avantaje și limitări

- **Avantaje:**
    - **Open-source și gratuit:** Permite utilizarea și personalizarea fără costuri suplimentare.
    - **Comunitate activă:** Documentație extinsă și suport din partea comunității.
    - **Flexibilitate:** Suportă o varietate mare de opțiuni de scanare și scripturi pentru teste specifice.

- **Limitări:**
    - **Detectabilitate:** Scanările pot fi detectate de sistemele de monitorizare și pot fi interpretate ca activități malițioase.
    - **Dependența de permisiuni:** Scanările pe rețele externe pot fi ilegale fără autorizație; utilizarea trebuie să se facă în medii controlate sau cu acordul părților implicate.
    - **Fals pozitive/negatives:** În funcție de configurarea rețelei și de firewall-uri, unele scanări pot să nu fie 100% exacte.

---