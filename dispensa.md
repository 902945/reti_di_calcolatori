# Dispensa Integrale – Reti di Calcolatori

> **Nota**: questa dispensa è stata generata a partire dagli appunti delle lezioni (file `reti1.pdf` – `reti23.pdf`). Tutti gli errori di battitura, i termini tecnici imprecisi e i concetti espressi in modo approssimativo sono stati corretti e, dove necessario, ampliati.

---

## Indice

1. [Introduzione alle Reti di Calcolatori](#1-introduzione-alle-reti-di-calcolatori)
2. [Livello Fisico (Physical Layer)](#2-livello-fisico-physical-layer)
3. [Livello Data Link (Data Link Layer)](#3-livello-data-link-data-link-layer)
4. [Protocolli di Finestra Scorrevole](#4-protocolli-di-finestra-scorrevole)
5. [Livello di Rete (Network Layer)](#5-livello-di-rete-network-layer)
6. [Topologie LAN e Accesso al Mezzo](#6-topologie-lan-e-accesso-al-mezzo)
7. [Algoritmi di Routing](#7-algoritmi-di-routing)
8. [Livello di Trasporto (Transport Layer)](#8-livello-di-trasporto-transport-layer)
9. [UDP e TCP](#9-udp-e-tcp)
10. [Controllo della Congestione](#10-controllo-della-congestione)
11. [Sicurezza nelle Reti](#11-sicurezza-nelle-reti)
12. [Crittografia Applicata](#12-crittografia-applicata)
13. [IPv4, IPv6 e NAT](#13-ipv4-ipv6-e-nat)
14. [ICMP, OSPF e Routing Intra-dominio](#14-icmp-ospf-e-routing-intra-dominio)
15. [Autonomous System e BGP](#15-autonomous-system-e-bgp)
16. [Livello Applicazione: DNS e HTTP](#16-livello-applicazione-dns-e-http)
17. [Posta Elettronica e Anti-Spam](#17-posta-elettronica-e-anti-spam)
18. [TLS/SSL e Socket di Rete](#18-tlsssl-e-socket-di-rete)
19. [Ethernet](#19-ethernet)
20. [Wi-Fi (IEEE 802.11)](#20-wi-fi-ieee-80211)

---

## 1. Introduzione alle Reti di Calcolatori

### 1.1 Storia e Motivazione

Prima di Internet le comunicazioni erano **analogiche**: esisteva sempre un collegamento fisico dedicato tra i due interlocutori per tutta la durata della chiamata.

Le prime reti erano **reti magliate** (*full-mesh*): ogni nodo era collegato direttamente a tutti gli altri. Il numero di link necessari cresce come `n(n−1)/2`, il che le rende non scalabili. La soluzione successiva fu la **rete gerarchica**: più economica, ma con la criticità di nodi "centrali" il cui guasto poteva interrompere molte comunicazioni simultaneamente.

### 1.2 Reti a Commutazione di Pacchetto vs. di Circuito

| Caratteristica | Commutazione di circuito | Commutazione di pacchetto |
|---|---|---|
| Percorso | Fisso per tutta la sessione | Dinamico, hop-by-hop |
| Tecnologia | Deve essere omogenea lungo tutto il percorso | Ogni link può usare tecnologie diverse |
| Tipi di dato | Originariamente solo voce | Qualsiasi (voce, testo, video) dopo digitalizzazione |
| Resilienza | Bassa: la rottura di un nodo interrompe la connessione | Alta: i router trovano un percorso alternativo |
| Efficienza | Bassa se il canale non è sempre in uso | Alta: le risorse sono condivise tra più flussi |

Internet è una **rete a commutazione di pacchetto**: i dati vengono suddivisi in pacchetti che viaggiano indipendentemente e vengono riassemblati a destinazione. Questo la rende **decentralizzata**, auto-riparante e capace di espandersi senza una gerarchia pianificata.

### 1.3 Architettura a Strati (Layered Architecture)

Per gestire la complessità di un sistema di comunicazione si adotta un **approccio stratificato**: ogni layer (livello) si occupa di una funzione specifica e comunica con il layer adiacente tramite interfacce ben definite. A ogni layer corrisponde uno o più **protocolli** che ne regolano il comportamento.

#### Modello ISO/OSI (7 livelli)

| # | Nome | Funzione |
|---|---|---|
| 7 | Applicazione | Interfaccia con le applicazioni utente (HTTP, SMTP, DNS…) |
| 6 | Presentazione | Formattazione, codifica, compressione dei dati |
| 5 | Sessione | Gestione e sincronizzazione della sessione tra applicazioni |
| 4 | Trasporto | Comunicazione affidabile end-to-end, multiplexing |
| 3 | Rete (Internet) | Indirizzamento e routing dei pacchetti |
| 2 | Data Link | Accesso al mezzo fisico, rilevamento degli errori |
| 1 | Fisico | Trasmissione di bit sul canale fisico |

#### Stack TCP/IP (4 livelli)

Il modello TCP/IP, usato in Internet, collassa i livelli 5-7 del modello ISO/OSI in un unico livello **Applicazione**.

**Principio fondamentale**: ogni layer è autonomo e non deve preoccuparsi del funzionamento degli altri layer. Chi scrive il codice del layer di trasporto, ad esempio, non ha bisogno di conoscere i dettagli del layer fisico.

### 1.4 Incapsulamento (Encapsulation)

Quando un messaggio scende attraverso lo stack, ogni layer aggiunge un **header** contenente i metadati necessari al layer corrispondente del destinatario per elaborare correttamente i dati. Il processo inverso (rimozione degli header) avviene durante la risalita dello stack a destinazione.

```
Applicazione:  [Dati]
Trasporto:     [Header TCP | Dati]                          ← segmento
Rete:          [Header IP | Header TCP | Dati]              ← pacchetto
Data Link:     [Header Eth | Header IP | Header TCP | Dati | FCS]  ← frame
Fisico:        bit
```

---

## 2. Livello Fisico (Physical Layer)

### 2.1 Modulazione del Segnale

La comunicazione fisica avviene tramite la **modulazione** di un segnale portante (*carrier*). Il carrier è tipicamente una sinusoide di frequenza `f_c`:

```
p(t) = A · cos(2π · f_c · t)
```

Il messaggio digitale da trasmettere è il **segnale modulante**. L'idea è quella di modificare una proprietà del carrier (ampiezza, frequenza o fase) per codificare i bit.

- **AM (Amplitude Modulation)**: si varia l'ampiezza `A`
- **FM (Frequency Modulation)**: si varia la frequenza `f_c`
- **PM (Phase Modulation)**: si varia la fase iniziale

Nei sistemi digitali si parla di:
- **ASK** (Amplitude Shift Keying)
- **FSK** (Frequency Shift Keying)
- **PSK** (Phase Shift Keying)

### 2.2 Baud Rate e Bit Rate

- **Simbolo**: unità di informazione trasmessa in un intervallo di tempo prefissato.
- **Baud rate** (Bd): numero di simboli trasmessi al secondo.
- **Bit rate** (bps): numero di bit trasmessi al secondo.

Se ogni simbolo trasporta un singolo bit, allora `bit rate = baud rate`. Tuttavia, con la **codifica multibit** è possibile associare più bit a ogni simbolo usando, ad esempio, frequenze diverse:

| Simbolo | Frequenza | Bit rappresentati |
|---|---|---|
| 0 | `f_c` | `00` |
| 1 | `f_c(1+Δ)` | `01` |
| 2 | `f_c(1+2Δ)` | `10` |
| 3 | `f_c(1+3Δ)` | `11` |

Con `M` livelli distinti: `bit rate = baud rate × log₂(M)`

### 2.3 Teorema di Nyquist

In un canale privo di rumore con banda `B` [Hz] e `M` livelli di segnale distinti, la capacità massima del canale è:

```
C = 2B · log₂(M)   [bit/s]
```

Il teorema è valido in **condizioni ideali** (assenza di rumore) e mostra che la banda permette di aumentare la frequenza di trasmissione in modo lineare.

### 2.4 Teorema di Shannon

Il teorema di Shannon introduce il **rumore** nel modello e fornisce il limite teorico assoluto della capacità di un canale:

```
C = B · log₂(1 + S/N)   [bit/s]
```

dove:
- `B` = banda del canale [Hz]
- `S/N` = rapporto segnale/rumore (SNR, spesso espresso in dB)

Questo limite non dipende dal numero di livelli di modulazione. A differenza di Nyquist, Shannon tiene conto del rumore fisico e fornisce un bound invalicabile: non è possibile trasmettere in modo affidabile a un bit rate superiore alla capacità di Shannon, indipendentemente dalla tecnica di modulazione usata.

---

## 3. Livello Data Link (Data Link Layer)

### 3.1 Responsabilità del Layer

Il data link layer gestisce la comunicazione tra due nodi **direttamente connessi** (hop-by-hop). Le sue responsabilità principali sono:

- **Framing**: delimitazione delle unità di dati (frame)
- **Rilevamento degli errori**: individuazione di bit corrotti durante la trasmissione
- **Controllo del flusso**: evitare che il mittente invii dati più velocemente di quanto il ricevente possa elaborarli

### 3.2 Problemi del Layer Fisico

Il layer fisico non è perfetto. I principali problemi che il data link deve gestire sono:

- **Bit flipping**: un bit viene ricevuto con valore invertito rispetto a quello trasmesso
- **Perdita di bit**: il ricevente riceve meno bit del previsto a causa di disallineamento del clock
- **Bit aggiuntivi**: il ricevente percepisce più bit di quelli trasmessi, sempre a causa di disallineamento del clock

### 3.3 Framing

Il **frame** è l'unità base di informazione scambiata tra due host connessi direttamente. Per delimitare l'inizio e la fine di un frame si usa il **bit stuffing**: viene definito un marcatore speciale che segnala l'inizio e la fine del messaggio.

**Protocol Overhead**: la frazione della capacità del canale impiegata per i meccanismi di controllo (header, delimitatori, ACK) invece che per i dati utente.

**Throughput**: la frazione della capacità del canale effettivamente usata per trasmettere dati utente. Vale la relazione: `Throughput = Capacità totale − Overhead`.

### 3.4 Service Data Unit (SDU)

Il layer data link riceve dal layer superiore (network) una **Service Data Unit (SDU)**, la imbusta in un frame aggiungendo gli header necessari e la trasmette.

### 3.5 Acknowledgment (ACK)

Un **ACK** (*Acknowledgment*) è un frame vuoto di dati che il ricevente invia al mittente per confermare la corretta ricezione di un frame. È necessario distinguere i frame dati dagli ACK tramite campi nell'header.

> ⚠ **Attenzione**: gli ACK introducono overhead aggiuntivo.

**State machine del data link layer**: sia il mittente sia il ricevente sono modellati come macchine a stati finiti. Le transizioni di stato sono causate dagli eventi di ricezione/trasmissione.

### 3.6 Rilevamento degli Errori

#### Bit di Parità

Viene aggiunto un singolo bit calcolato in modo che il numero totale di bit a `1` nel frame sia pari (parità pari) o dispari (parità dispari). Il ricevente ricalcola la parità e segnala un errore se il valore non corrisponde.

> ⚠ **Limitazione**: un numero **pari** di errori non viene rilevato.

#### Checksum

Si aggiunge un campo nell'header calcolato come somma (o variante) dei byte del payload. Il ricevente ricalcola il checksum e lo confronta con quello ricevuto.

> ⚠ **Overhead**: il campo checksum aumenta la dimensione dell'header. Se il payload non è multiplo di byte, può essere necessario aggiungere byte di padding.

#### CRC (Cyclic Redundancy Check)

Tecnica più robusta basata sulla divisione polinomiale. Usata nell'Ethernet moderno, rileva tutti gli errori a burst entro una certa lunghezza.

### 3.7 Gestione degli Errori a Livello Frame

Poiché all'interno di una rete gli errori sono rari, non conviene investire molte risorse per la **correzione** degli errori. È preferibile rilevare il frame corrotto e richiederne la ritrasmissione.

**Meccanismo**: il mittente avvia un **timer** dopo l'invio di ogni frame. Se l'ACK non arriva entro la scadenza del timer, il frame viene ritrasmesso.

> ⚠ **Problema**: se l'ACK va perso, il frame viene ritrasmesso inutilmente, e il ricevente riceverà un duplicato.

### 3.8 Protocollo a Bit Alternante (ABP – Alternating Bit Protocol)

Per distinguere frame nuovi da ritrasmissioni si aggiunge un **numero di sequenza**. Nel caso più semplice il numero di sequenza è un singolo bit che viene alternato (0, 1, 0, 1, …) a ogni nuovo frame. In questo modo il ricevente può capire se si tratta di un nuovo frame o di un duplicato.

**RTT** (*Round Trip Time*): tempo che trascorre tra l'invio di un frame e la ricezione del corrispondente ACK. Il timer del mittente deve essere impostato a un valore leggermente superiore all'RTT.

#### Esempio numerico

- Link Gigabit Ethernet: capacità = `10⁹ bit/s`
- Dimensione frame: 1500 byte = 12000 bit
- Tempo di trasmissione: `12000 / 10⁹ ≈ 0,012 ms`
- Propagation delay: 10 ms (andata)
- RTT ≈ 20 ms

Il mittente termina di inviare il frame a `t = 0,012 ms`, ma riceve l'ACK solo a `t ≈ 20,012 ms`. Nei restanti ~20 ms il canale è **inattivo**: il bit rate effettivo è molto inferiore alla capacità nominale. Questo problema si risolve con il pipelining.

---

## 4. Protocolli di Finestra Scorrevole

### 4.1 Pipelining

Il **pipelining** consente di inviare più frame consecutivi senza attendere l'ACK del precedente. Gli ACK possono arrivare prima della chiamata `DATA.indication` (notifica all'applicazione). In questo modo il canale rimane occupato quasi tutto il tempo, aumentando drasticamente l'efficienza.

### 4.2 Sliding Window (Finestra Scorrevole)

I numeri di sequenza diventano interi. Il mittente A e il ricevente B negoziano la dimensione della **finestra di invio** `W`. Il mittente può inviare fino a `W` frame senza ricevere ACK.

- Quando il frame con il **numero di sequenza più basso** riceve l'ACK, A sposta la finestra a destra di una posizione e può inviare il frame successivo.
- Se il campo del numero di sequenza usa `n` bit, lo spazio dei numeri è `2ⁿ`.

### 4.3 Go-Back-N (GBN)

#### Lato Ricevente (B)
- Accetta **solo** frame in sequenza (nell'ordine atteso).
- Per ogni frame ricevuto correttamente invia un **ACK cumulativo** che indica l'ultimo numero di sequenza ricevuto nell'ordine corretto.
- Non richiede un buffer lato ricevente.

#### Lato Mittente (A)
- Mantiene un **sending buffer** di dimensione pari alla finestra di invio.
- I frame vengono inviati con numeri di sequenza crescenti finché il buffer non si riempie.
- Usa un **singolo timer** avviato all'invio del primo frame della finestra.
- Quando A riceve un ACK: rimuove dal buffer i frame confermati; se non restano frame in attesa, ferma il timer.
- Se il timer scade: ritrasmette **tutti** i frame non ancora confermati e riavvia il timer.

**Variabili di stato**:
- `seq`: ultimo numero di sequenza inviato
- `unack`: primo numero di sequenza senza ACK
- `W`: dimensione della finestra

**Dimensione massima della finestra**: per GBN, la finestra può avere al massimo `2ⁿ − 1` frame (con `n` bit per il numero di sequenza).

### 4.4 Selective Repeat (SR)

Anche B ha ora un **buffer** e accetta frame entro la finestra, anche **fuori ordine**. In ogni ACK, B riporta:
- L'ultimo numero di sequenza consecutivo ricevuto correttamente (come in GBN).
- Gli intervalli di frame ricevuti fuori ordine (SACK – Selective Acknowledgment).

**Lato mittente**:
- A ogni frame inviato corrisponde un **timer dedicato** (un timer per frame).
- Quando arriva un ACK, A cancella i timer dei frame confermati.
- Se la finestra può avanzare, lo fa.
- Se scade un timer, A ritrasmette **solo** il frame corrispondente a quel timer.

**Dimensione massima della finestra**: per SR la finestra può avere al massimo `2ⁿ⁻¹` frame, per evitare ambiguità tra frame nuovi e ritrasmissioni.

### 4.5 Piggybacking e Deferred ACK

**Piggybacking**: nelle connessioni bidirezionali, il numero di ACK viene inserito nell'header del frame dati. In questo modo un ACK può essere "agganciato" (*piggybackato*) a un frame dati e non richiede un frame separato.

**Deferred ACK** (ACK differito): A non invia subito un ACK ma attende di avere un frame dati da spedire. Se entro un breve timeout non ha dati da inviare, genera comunque un frame dedicato solo per l'ACK. In caso di dati in uscita, l'ACK viene piggybackato sul frame dati.

---

## 5. Livello di Rete (Network Layer)

### 5.1 Responsabilità del Layer

Il network layer assume che il data link sia **quasi affidabile**: qualche perdita può avvenire, ma non frequentemente. Al network layer i frame diventano **pacchetti**. Un pacchetto contiene:
- I dati ricevuti dal transport layer
- Un **header IP** con indirizzo sorgente e destinazione, TTL, e altri metadati

Il pacchetto viene incapsulato nel payload del frame del data link.

### 5.2 Rete a Datagrammi

Nella **rete a datagrammi** (Internet):
- Ogni host ha un **indirizzo di rete** (indirizzo IP)
- Per inviare informazioni a un host remoto si crea un pacchetto contenente l'indirizzo di consegna, l'indirizzo di partenza e i dati
- I router effettuano il **forwarding hop-by-hop**: ogni router riceve il pacchetto, consulta la propria **forwarding table** e lo invia all'interfaccia corretta verso la destinazione

### 5.3 Forwarding Table

La forwarding table associa ogni prefisso di rete all'interfaccia di uscita. Deve essere **consistente** lungo il percorso, ma possono verificarsi errori:

- **Black hole**: un router dovrebbe inoltrare un pacchetto verso una destinazione, ma non ha una entry corrispondente nella sua forwarding table. Il pacchetto viene scartato silenziosamente.
- **Routing loop**: il pacchetto gira indefinitamente tra due o più router che si rimandano il pacchetto l'uno all'altro.

### 5.4 Data Plane e Control Plane

- **Data plane**: si occupa dell'inoltro (*forwarding*) dei pacchetti in base alla forwarding table.
- **Control plane**: si occupa della costruzione e dell'aggiornamento delle forwarding table.

Il control plane può essere:
- **Manuale**: un amministratore scrive le tabelle a mano (adatto solo per reti molto piccole)
- **Distribuito**: i router si scambiano informazioni sulla topologia della rete con protocolli di routing
- **Centralizzato**: un controller centrale monitora la rete fisica e popola le tabelle (SDN – Software Defined Networking)

### 5.5 Indirizzamento Piatto vs. Gerarchico

- **Indirizzamento piatto** (*flat*): ogni indirizzo è indipendente. Facile da configurare, ma non scala: con un milione di host, le forwarding table avranno un milione di entry.
- **Indirizzamento gerarchico**: gli indirizzi sono organizzati in prefissi, così una singola entry in tabella può coprire interi blocchi di indirizzi. Il problema è che se un host cambia rete, cambia anche il suo indirizzo.

### 5.6 Diversità Tecnologica e Frammentazione

I diversi data link (Ethernet, Wi-Fi, PPP…) supportano unità di trasmissione massima (**MTU**) diverse. Quando un router deve inoltrare un pacchetto su un link con MTU inferiore alla dimensione del pacchetto, ha tre opzioni:

1. **Scartare** il pacchetto e inviare al mittente un messaggio ICMP "Fragmentation Needed".
2. **Frammentare** il pacchetto: dividerlo in frammenti più piccoli che vengono riassemblati a destinazione.
3. **Path MTU Discovery**: il mittente scopre la MTU minima del percorso e invia pacchetti di dimensione adeguata (approccio usato da IPv6).

> 💡 In IPv4 la frammentazione è permessa sia al mittente sia ai router intermedi; il riassemblaggio avviene solo a destinazione.

---

## 6. Topologie LAN e Accesso al Mezzo

### 6.1 Topologie di Rete Locale

#### Full Mesh
- Ogni nodo è collegato direttamente a tutti gli altri
- Numero di link: `n(n−1)/2`
- Interfacce per nodo: `n−1`
- Pro: massima capacità, resistente ai guasti
- Contro: non scalabile, costo elevato

#### Bus
- Un singolo link fisico condiviso da tutti
- Pro: economica
- Contro: non scalabile, in caso di guasto l'intera rete si spezza

#### Ring (Anello)
- Ogni nodo è connesso al successivo formando un anello chiuso
- Pro: più resistente di un bus (sopporta una singola rottura)
- Contro: non scalabile
- Uso tipico: reti metropolitane con ridondanza quando il cablaggio è costoso

#### Star (Stella)
- Tutti i nodi sono connessi a un nodo centrale (switch/hub)
- Numero di link: `n−1` (uno per host)
- Pro: buon compromesso prestazioni/costo, alta controllabilità
- Contro: il guasto del nodo centrale interrompe tutta la rete
- **Utilizzata nelle reti LAN moderne**

> 💡 Una rete Wi-Fi funziona **idealmente** come una star (tutti collegati all'Access Point), ma fisicamente è un **bus**: il mezzo radio è condiviso da tutti. Il data link layer si occupa dello scheduling dell'accesso al canale.

#### Albero (Tree)
- Estensione della stella: uno switch centrale connette altri switch, ognuno dei quali connette gli host
- Pro: massimo compromesso prestazioni/costo; `n−1` link fisici in totale
- Contro: non resistente ai guasti di connessione (la rottura di un link può isolare un intero sottoalbero)

#### Rete di Data Center
- Usa **edge switch** per connettere i server
- La tolleranza ai guasti è garantita dalla ridondanza dei **core switch**
- Il **fat-tree** usa switch di fascia bassa ma in grande numero, riducendo il costo (un core switch ad alte prestazioni costa molto più di molti edge switch)
- Necessita di **routing multipath** per distribuire il traffico

### 6.2 Collo di Bottiglia (Bottleneck)

In una connessione **multi-hop** (che attraversa più di un link), il throughput complessivo è limitato dal link più lento. Quando più host condividono lo stesso link a valle (downstream), il throughput disponibile si divide tra di loro.

### 6.3 Collisioni

Sulle reti a bus (condivisione del mezzo fisico) due trasmissioni simultanee interferiscono tra loro producendo una **collisione**: entrambi i frame risultano corrotti. È necessario un meccanismo di accesso al mezzo per coordinare le trasmissioni.

Nelle reti a stella con cavi Ethernet moderni (full-duplex), ogni host ha un link dedicato con due circuiti separati per trasmissione e ricezione → **non ci sono collisioni**.

### 6.4 FDMA (Frequency Division Multiple Access)

Si assegna una **frequenza diversa** a ogni comunicazione. I problemi sorgono quando le radio possono usare solo un canale alla volta (come nel Wi-Fi): gli host collegati allo stesso AP devono condividere la stessa frequenza e coordinare le trasmissioni nel tempo.

### 6.5 TDMA (Time Division Multiple Access)

Il tempo è diviso in **slot** di durata fissa. Con `m` terminali, si assegnano `m` slot ripetuti periodicamente; ogni terminale trasmette nel proprio slot.

- Pro: deterministico, facile da implementare, nessuna collisione
- Contro: se un terminale non ha dati da inviare, il suo slot rimane **sprecato**; un terminale che deve trasmettere deve attendere il proprio slot anche se gli altri sono inattivi

### 6.6 ALOHA e S-ALOHA

**ALOHA**: i terminali trasmettono appena hanno dati da inviare, senza coordinazione. Se si verifica una collisione, i frame vengono ritrasmessi.

**Slotted ALOHA (S-ALOHA)**: i terminali sono sincronizzati su slot di tempo. Una trasmissione occupa esattamente uno slot. I dettagli:

- `p`: probabilità che un terminale abbia qualcosa da trasmettere in uno slot (dipende dal traffico di rete)
- Se due terminali trasmettono nello stesso slot → collisione → entrano in stato **backlogged**
- I terminali in stato backlogged ritrasmettono con probabilità `q_b` (parametro di rete)
- I terminali backlogged tornano allo stato normale solo quando riescono a trasmettere con successo

**System load G**: numero medio di frame da inviare per slot da tutti i terminali.

**Efficienza di S-ALOHA**: la probabilità di trasmissione senza collisioni è:

```
P_s = G · e^(−G)
```

Il massimo si raggiunge per `G = 1`, dove `P_s = 1/e ≈ 0,368`. Questo significa che al massimo il **36,8%** degli slot è usato con successo (il rimanente 63,2% è perso in collisioni o slot vuoti).

**Congestion collapse**: quando il carico aumenta oltre la saturazione, il throughput della rete crolla improvvisamente. Questo avviene nei protocolli ad accesso casuale (ALOHA), mentre nel TDMA non si verifica mai (ogni terminale ha garantito il suo slot, anche se piccolo).

> ⚠ **Il data link layer è nativamente inaffidabile**: può scartare frame. I layer superiori devono tenerne conto.

---

## 7. Algoritmi di Routing

### 7.1 Distance Vector Routing (DV)

Il routing a vettori di distanza implementa una variante distribuita dell'algoritmo di **Bellman-Ford**. Il protocollo che lo implementa è il **RIP** (Routing Information Protocol).

**Struttura della routing table**: ogni router `R` mantiene una tabella con un'entry per ogni destinazione `d`:
- `R[d].link`: link di forwarding per raggiungere `d`
- `R[d].cost`: somma dei costi dei link che compongono il percorso più breve verso `d`
- `R[d].time`: timestamp dell'ultimo aggiornamento per `d`

**Funzionamento**:
1. Ogni router inizializza la propria tabella con solo se stesso (distanza 0)
2. Periodicamente, ogni router invia la propria routing table ai vicini diretti (**Distance Vector**, DV)
3. Quando un router riceve un DV da un vicino, aggiorna la propria tabella se trova percorsi più convenienti

```python
def received(V, l):
    # V = vettore di distanza ricevuto dal link l
    for d in V:
        if d not in R:
            R[d].link = l
            R[d].cost = V[d].cost + l.cost
            R[d].time = now()
        else:
            if (V[d].cost + l.cost < R[d].cost) or (R[d].link == l):
                R[d].link = l
                R[d].cost = V[d].cost + l.cost
                R[d].time = now()
```

**Caratteristiche**:
- Efficiente, facile da implementare, leggero
- **Lento a convergere** dopo cambiamenti di topologia

### 7.2 Count-to-Infinity

Il problema del **count-to-infinity** si manifesta quando un link si rompe:

1. Il router D si accorge della rottura e imposta il costo verso B, C, E a ∞
2. Ma prima che D diffonda questa informazione, riceve un DV da A che riporta un percorso verso B attraverso A
3. D aggiorna la sua tabella usando A come next hop, "non sapendo" che A raggiunge B passando per D stesso
4. Questo crea un loop: A e D si aggiornano reciprocamente aumentando la distanza all'infinito

**Cause**:
- Presenza di un loop topologico (anche una singola connessione full-duplex lo crea)
- Cambiamento nella topologia della rete
- Il DV include solo la **distanza**, non l'intero percorso → impossibile rilevare i loop

**Soluzioni parziali**:

**Triggered updates**: quando un link fallisce, il router non aspetta il prossimo ciclo periodico ma invia immediatamente un DV con la distanza impostata a ∞.

**Split Horizon with Poison Reverse**: per ogni router, l'orizzonte è diviso tra i vicini usati come next hop e tutti gli altri. Il router annuncia distanza ∞ ("poison") ai vicini che usa come next hop per una certa destinazione:

```python
for ifc in interfaces:
    v = Vector()
    for d in R:
        if R[d].link != ifc:
            v.add(Pair(d, R[d].cost))
        else:
            v.add(Pair(d, infinity))  # poison reverse
    send(v, ifc)
```

> 💡 L'unico modo per eliminare completamente il count-to-infinity è che i router includano nel DV non solo la distanza, ma **l'intero percorso** verso ogni destinazione. Questo è il principio alla base del routing a **path vector** (usato da BGP).

### 7.3 Link State Routing (LS)

Con il link state routing, ogni router costruisce una **mappa completa della topologia di rete** e usa l'algoritmo di Dijkstra per calcolare i percorsi minimi.

**Peso dei link**: il peso è proporzionale al **ritardo di propagazione** del link; un'altra metrica comune è inversamente proporzionale alla capacità:

```
peso = C / link_capacity
```

**Scoperta dei vicini**: ogni router invia periodicamente messaggi **Hello** a tutte le sue interfacce. I messaggi Hello contengono l'indirizzo del router; in questo modo ogni router scopre i suoi vicini diretti.

**Link State Packet (LSP)**: dopo aver scoperto i vicini, ogni router crea un LSP contenente:
- `LSP.Router`: identificativo (indirizzo) del router mittente
- `LSP.seq`: numero di sequenza del LSP (incrementato a ogni nuovo LSP)
- `LSP.links[]`: array di link pubblicizzati, ognuno con l'ID del vicino e il peso del link

**Flooding**: i LSP vengono distribuiti a tutti i router mediante flooding. Ogni router mantiene un **Link State Database (LSDB)** con i LSP più recenti ricevuti da ogni router:

```python
# Arrivo di un LSP al router R sul link l
if newer(LSP, LSDB[LSP.Router]):
    LSDB.add(LSP)
    for i in links:
        if i != l:
            send(LSP, i)
# altrimenti il LSP è già stato diffuso
```

**Dijkstra**: una volta che il LSDB è completo, ogni router esegue l'algoritmo di Dijkstra sul grafo della topologia per calcolare i percorsi minimi verso tutte le destinazioni e popolare la forwarding table.

**Gestione dei guasti**: quando un link fallisce, i due router adiacenti se ne accorgono per la mancanza degli Hello negli ultimi `k·N` secondi. Generano immediatamente un nuovo LSP (senza il link guasto) e lo diffondono con flooding.

**Scalabilità di LS**:
- Computazionalmente più oneroso di DV (richiede Dijkstra su tutto il grafo)
- Molto più "loquace" di DV: ogni router genera LSP che devono raggiungere tutti i router
- Internet ha centinaia di migliaia di router: usare LS su scala globale richiederebbe hardware molto potente e genererebbe un overhead enorme
- **LS è usato all'interno delle singole reti** (intra-domain routing, es. OSPF); a livello inter-dominio si usa BGP (path vector)

---

## 8. Livello di Trasporto (Transport Layer)

### 8.1 Responsabilità del Layer

Il transport layer assume che il network layer offra un servizio **datagram**: consegna dei pacchetti senza connessione e non affidabile. Le richieste del layer applicazione al transport layer sono:

- I dati devono **arrivare correttamente** (senza corruzione)
- Il ricevente deve poter richiedere **nuovamente** i dati persi
- I **duplicati** devono essere rilevati e scartati

**Multiplexing**: un host gestisce numerose connessioni simultaneamente. Il transport layer deve saper distinguere il flusso di dati appartenente a ogni coppia client-server usando le **porte**.

**Segmento**: l'unità di dati del transport layer (le unità del network layer sono i pacchetti; le unità del data link layer sono i frame).

### 8.2 Servizio Connectionless (Senza Connessione)

Fornisce un trasporto dati non affidabile con:
- **Error detection** (checksum)
- **Multiplexing** degli endpoint (porte)

È senza stato: ogni segmento viaggia indipendentemente. Usato in applicazioni che tollerano la perdita di pacchetti ma richiedono bassa latenza (gaming, streaming, DNS, VoIP → protocollo UDP).

### 8.3 Servizio Connection-Oriented (Con Connessione)

Crea una connessione con uno stato tra i due endpoint. Alcune variabili interne controllano l'evoluzione della connessione. Fornisce:
- Trasporto affidabile
- Consegna in ordine
- Controllo del flusso

### 8.4 Maximum Segment Lifetime (MSL)

L'MSL è il tempo massimo stimato che un pacchetto può rimanere in circolazione nella rete prima di essere scartato. Un valore tipico è **MSL = 120 secondi**.

### 8.5 Initial Sequence Number (ISN) e Identificazione Univoca

Per evitare che una connessione duplicata (stale) venga accettata erroneamente, ogni connessione ha un **Initial Sequence Number (ISN)** univoco, derivato da un **transport clock** (un contatore monotonicamente crescente). Il clock non deve essere sincronizzato tra i due endpoint.

Il server rimanda l'ISN nel messaggio di **Connection Acknowledgment (CA)**. Il client verifica che il numero di sequenza corrisponda.

Condizione necessaria: il timer del transport clock deve avere un periodo di wrap-around molto più grande dell'MSL, così da garantire che non esistano mai due connessioni attive con lo stesso ISN.

### 8.6 Three-Way Handshake

La procedura standard per stabilire una connessione bidirezionale con due numeri di sequenza (uno per direzione):

```
Client                              Server
  |                                   |
  |---[CR: seq=X]------------------->|   1. Client invia Connection Request
  |                                   |      con ISN=X estratto dal transport clock
  |<--[CA: ack=X+1, seq=Y]----------|   2. Server risponde con CA:
  |                                   |      - ack=X+1 conferma ricezione di CR
  |                                   |      - seq=Y è il proprio ISN
  |---[ACK: ack=Y+1]---------------->|   3. Client conferma ricezione di CA
  |                                   |      La connessione è ora stabilita
```

Al termine dell'handshake entrambi gli endpoint conoscono gli ISN dell'altro e la connessione è pronta per trasferire dati.

### 8.7 Trasporto dei Dati e Flow Control

Il ricevente dispone di un **buffer** per i dati ricevuti ma non ancora elaborati dall'applicazione. Il buffer può riempirsi se l'applicazione è lenta a elaborare i dati.

**Window Advertising**: per evitare l'overflow del buffer, il transport layer permette al ricevente di pubblicare la **dimensione corrente della finestra di ricezione** (`rwin`) in ogni ACK. Il mittente non può avere più di `min(swin, rwin)` dati non confermati in transito.

Quando `rwin = 0`, il mittente invia periodicamente piccoli frame di sonda per dare al ricevente l'opportunità di pubblicare una finestra diversa da zero (nel caso un ACK precedente fosse andato perso).

---

## 9. UDP e TCP

### 9.1 UDP (User Datagram Protocol)

Fornisce un servizio di trasporto **non affidabile, senza connessione** su un servizio network layer datagram.

**Caratteristiche**:
- Dimensione massima del payload: 65.467 byte (65.535 − 8 byte di header UDP − 20 byte di header IP)
- Non garantisce la consegna
- Ha error detection (checksum)
- Senza stato: ogni segmento è indipendente

**Header UDP** (8 byte totali):
- 16 bit porta sorgente
- 16 bit porta destinazione
- 16 bit length (lunghezza totale del segmento)
- 16 bit checksum

**Porte**: i 16 bit del campo porta consentono un massimo di 65.535 porte per host. Le porte sono divise in:
- **Well-known ports** (0–1023): riservate a processi con privilegi di amministrazione (es. porta 80 per HTTP, 53 per DNS)
- **Registered ports** (1024–49.151): protocolli che hanno richiesto un numero all'IETF
- **Dynamic/ephemeral ports** (49.152–65.535): usabili da qualsiasi applicazione

**Casi d'uso**: applicazioni che richiedono **bassa latenza** e tollerano la perdita di dati: DNS, QUIC, VoIP, gaming online, streaming video in tempo reale.

### 9.2 TCP (Transmission Control Protocol)

Fornisce un servizio di **bytestream affidabile, orientato alla connessione**.

**Caratteristiche**:
- L'unità di trasporto è il **segmento**; ogni byte ha un numero di sequenza
- Garantisce la consegna (con ritrasmissione)
- Garantisce la consegna in ordine
- Controllo del flusso (flow control)
- Controllo della congestione (congestion control)

### 9.3 Header TCP

Il campo `Data Offset` indica la dimensione dell'header TCP in parole da 32 bit (dimensione minima: 20 byte).

**Flag principali**:
- `SYN`: usato durante il three-way handshake per stabilire la connessione
- `FIN`: usato per terminare la connessione
- `RST`: chiude la connessione forzatamente in caso di problemi
- `ACK`: indica che il campo Acknowledgment Number è valido
- `CWR`, `ECE`: usati per la segnalazione esplicita della congestione (ECN)
- `PSH`: forza un flush del buffer del ricevente, facendo consegnare immediatamente i dati all'applicazione
- `URG`: dati urgenti, da consegnare immediatamente all'applicazione ignorando il numero di sequenza

**Interpretazione degli ACK**: un ACK con valore `X+1` significa "ho ricevuto correttamente tutti i byte fino al byte `X` incluso, e aspetto il byte `X+1`".

### 9.4 Connection Setup in TCP

Il three-way handshake di TCP:

```
Client                              Server
  |                                   |
  |---[SYN: seq=X]------------------>|
  |<--[SYN+ACK: seq=Y, ack=X+1]-----|
  |---[ACK: ack=Y+1]---------------->|
  |                                   |
  | (connessione stabilita)           |
```

**Caso speciale**: se due host avviano simultaneamente una connessione verso l'altro usando le stesse porte (simultaneous open), invece di rimanere in stallo, entrambi inviano `SYN+ACK` che funge da conferma reciproca.

**MSS** (Maximum Segment Size): negoziato durante il SYN/SYN-ACK come estensione. Tipicamente pari alla MTU della rete meno 40 byte (20 byte header IPv4 + 20 byte header TCP). Il minimo è 536 byte.

**TCB** (Transmission Control Block): struttura dati mantenuta da ogni endpoint per tracciare lo stato della connessione.

### 9.5 Connection Release in TCP

**Graceful release** (chiusura elegante): il mittente invia un segmento `FIN` con il numero di sequenza `X`. Il ricevente risponde con un ACK e può continuare a inviare dati fino a quando non è pronto a chiudere la propria direzione. Infine anche il ricevente invia un `FIN`, riceve l'ACK e la connessione è chiusa.

**Abrupt release** (chiusura brusca): invio del flag `RST`. Il TCB viene cancellato immediatamente da entrambi i lati.

**TIME_WAIT**: dopo una graceful release, l'endpoint che ha inviato l'ultimo ACK deve rimanere in stato TIME_WAIT per `2·MSL` (circa 4 minuti) per gestire eventuali ACK persi (in quel caso l'altro endpoint rimanda il FIN).

### 9.6 Transmission Control Block (TCB)

**Contenuti statici**:
- IP locale e remoto, porte (identificano univocamente la connessione)
- Sending buffer: dati inviati ma non ancora confermati
- Receiving buffer: dati ricevuti ma non ancora consegnati all'applicazione

**Contenuti dinamici**:
- Stato corrente della FSM (es. SYN_SENT, SYN_RECEIVED, ESTABLISHED, TIME_WAIT…)
- `MMS`: Maximum Message Size (può cambiare)
- `snd.nxt`: numero di sequenza del prossimo byte da inviare
- `snd.una`: numero di sequenza dell'ultimo byte inviato ma non ancora confermato
- `snd.wnd`: dimensione corrente della finestra di invio (ricevuta dall'altro endpoint)
- `rcv.nxt`: numero di sequenza del prossimo byte atteso dal ricevente

### 9.7 Algoritmo di Nagle

Per evitare il problema dei "silly small segments" (pacchetti con pochissimi dati e molto overhead di header):

```python
# data: byte non ancora inviati (nuovi + nel buffer)
if len(data) >= MSS and unacknowledged_data < MSS:
    send one MSS-sized segment
else:
    if there are unacknowledged data:
        # aspetta l'ACK prima di inviare altri dati piccoli
        buffer the data
    else:
        send one TCP segment containing at most snd.wnd data
```

**Conseguenza**: la maggior parte dei pacchetti è di dimensione MSS (tipicamente 1460 byte per Ethernet con MTU 1500) oppure è un ACK (0 byte di payload). L'algoritmo di Nagle riduce l'overhead ma è **inadatto per il traffico real-time** (es. giochi online, VoIP) dove si preferisce la latenza minima anche a costo di pacchetti piccoli.

### 9.8 Window Scaling e Sequence Number

**Problema con reti veloci**: con numeri di sequenza a 32 bit e rete a 30 Gb/s, il contatore si resetta ogni ~1 secondo, ben al di sotto dell'MSL di 120 secondi → rischio di ricevere un segmento "vecchio" con lo stesso numero di sequenza.

**Soluzione per la dimensione della finestra**: l'header TCP standard limita la finestra a 65.535 byte (`2¹⁶ − 1`). Per connessioni ad alta capacità si usa l'opzione **Window Scale** (negoziata nel SYN/SYN-ACK): la finestra effettiva diventa `rcv.wnd × 2ˢ` con `0 ≤ s ≤ 14`.

**Soluzione per i numeri di sequenza**: l'opzione **Timestamps** (RFC 7323) aggiunge all'header il timestamp corrente e l'ultimo timestamp ricevuto. Permette di:
1. Calcolare facilmente l'RTT
2. Discriminare segmenti vecchi da nuovi (un segmento con timestamp molto vecchio può essere scartato)

### 9.9 RTO e Algoritmo di Van Jacobson

L'**RTO** (Retransmission Timeout) è il tempo dopo il quale il mittente ritrasmette un segmento non confermato. Idealmente dovrebbe essere leggermente superiore all'RTT.

Misurare l'RTT è difficile nelle connessioni con perdite, poiché l'RTT varia nel tempo. L'algoritmo di **Van Jacobson** usa una media mobile esponenziale:

```
rtt   = ultima misurazione dell'RTT
srtt  = RTT stimato (EMA con peso α), inizializzato al primo rtt
rttvar = deviazione stimata dell'RTT, inizializzata a rtt/2
rto   = srtt + 4 · rttvar

# Aggiornamento ad ogni misurazione:
rttvar = (1 − β) · rttvar + β · |srtt − rtt|
srtt   = (1 − α) · srtt + α · rtt
rto    = srtt + 4 · rttvar
```

Con `α ≈ 0,125` e `β ≈ 0,25` (valori tipici RFC 6298).

**RTO exponential backoff**: ad ogni ritrasmissione fallita, l'RTO viene raddoppiato. Dopo una trasmissione riuscita, torna al valore calcolato. Questo evita di bombardare una rete congestionata con ritrasmissioni ravvicinate.

### 9.10 Fast Retransmit

Ricevere tre ACK duplicati (stessa sequenza number) è un forte segnale che il segmento successivo è andato perso. TCP Fast Retransmit ritrasmette il segmento mancante **immediatamente**, prima della scadenza del timer.

### 9.11 Selective ACK (SACK)

Estensione di TCP che permette al ricevente di comunicare al mittente esattamente quali blocchi di dati ha ricevuto fuori ordine, senza richiedere la ritrasmissione di tutti i dati dopo il gap. Ad esempio:

```
ACK number: 850
SACK blocks: [1100-1200], [1250-1500], [1800-1900]
```

Significa: "Ho ricevuto tutti i byte fino a 849; ho anche ricevuto i blocchi 1100-1199, 1250-1499 e 1800-1899. Mi mancano i byte 850-1099, 1200-1249, 1500-1799 e 1900+."

---

## 10. Controllo della Congestione

### 10.1 Cos'è la Congestione di Rete

La **congestione di rete** (*network congestion*) avviene quando i router nel percorso dal client al server perdono pacchetti perché devono instradare troppi pacchetti contemporaneamente. Le loro code di output si riempiono e iniziano a scartare pacchetti.

**Nota**: la congestione di rete è diversa dalla congestione del ricevente. Assumiamo che quando il ricevente riceve un pacchetto risponda subito con un ACK (`snd.wnd = rwnd`).

### 10.2 Congestion Window (cwnd)

Si introduce la **finestra di congestione** `cwnd`. In ogni momento, il mittente non può avere in transito più dati di quelli permessi sia da `snd.wnd` (flow control) sia da `cwnd` (congestion control):

```
sending rate = min(swin, cwnd) / RTT
```

Inizialmente `cwnd` è piccolo (`10 × MSS`) per evitare che una connessione nuova vada subito in congestione. Cresce nel tempo; quando viene rilevata una perdita, si riduce.

**ssthresh** (slow start threshold): parametro che indica la dimensione dell'ultima finestra che non ha causato congestione.

### 10.3 Slow Start

L'algoritmo di **slow start** (proposto da Van Jacobson) fa crescere `cwnd` **esponenzialmente**:
- Per ogni segmento confermato, `cwnd` aumenta di `MSS`
- In pratica, `cwnd` raddoppia ogni RTT (finché `cwnd < ssthresh`)

Quando `cwnd ≥ ssthresh`, si passa alla **fase di congestion avoidance**: `cwnd` aumenta linearmente di `MSS` per RTT.

### 10.4 Rilevamento della Congestione

Due eventi segnalano la congestione:

1. **Mild congestion** (congestione lieve): ricezione di **3 ACK duplicati** → fast retransmit
   - `ssthresh = cwnd / 2`
   - `cwnd = ssthresh` (oppure `ssthresh + 3·MSS` in TCP Reno)
   
2. **Severe congestion** (congestione severa): scadenza del **Retransmission Timeout (RTO)**
   - `ssthresh = cwnd / 2`
   - `cwnd = 10·MSS` (ripartenza da slow start)

### 10.5 Explicit Congestion Notification (ECN)

In alternativa alla perdita di pacchetti, la congestione può essere segnalata **esplicitamente**: i router impostano un bit nell'header IP quando la loro coda si sta riempiendo. Il ricevente riporta questa informazione al mittente tramite i flag ECE e CWR dell'header TCP, permettendo una riduzione della finestra **prima** che si verifichino perdite.

---

## 11. Sicurezza nelle Reti

### 11.1 Definizione e Standard X.800

La sicurezza delle reti è definita come l'insieme di azioni volte a proteggere le reti e i servizi da usi non autorizzati. Deve essere garantita a tutti i livelli, dal fisico alla gestione degli utenti.

Lo standard **X.800** definisce un approccio sistematico alla sicurezza. Un sistema sicuro deve investire in 6 aree:

| Area | Descrizione |
|---|---|
| **Data availability** | Il servizio deve essere sempre disponibile |
| **Data authentication** | Verificare l'identità del mittente dei dati |
| **Data integrity** | I dati non devono essere modificati in transito |
| **Access control** | L'accesso al servizio deve essere ristretto agli autorizzati |
| **Non-repudiation** | Né il mittente né il destinatario possono negare una comunicazione avvenuta |
| **Anonymity** | Possibilità di usare un sistema senza essere identificati |

> 🚨 **Principio fondamentale**: i sistemi non possono essere sicuri al 100%. Un sistema sicuro è un sistema che **resiste a un determinato attaccante** con un determinato modello di minaccia. La sicurezza è sempre un **trade-off** tra valore del sistema, costi di protezione e rischi accettabili.

### 11.2 Meccanismi di Sicurezza

I principali meccanismi crittografici:
- **Encryption** (cifratura): trasforma il plaintext in ciphertext illeggibile senza la chiave
- **Digital signature** (firma digitale): prova l'autenticità e l'integrità di un messaggio
- **Access control**: limita l'accesso alle risorse
- **Integrity mechanisms** (hashing): verifica che i dati non siano stati modificati
- **Authentication protocols**: protocoli per verificare l'identità

### 11.3 Service Availability e DoS

Un servizio deve essere accessibile sempre. Proteggersi completamente da un attacco **DoS** (Denial of Service) è impossibile, poiché esistono sempre risorse fisiche (banda, CPU, memoria) che l'attaccante può saturare. L'obiettivo è rendere la saturazione del sistema **troppo costosa** per l'attaccante.

### 11.4 Data Confidentiality e Authentication

**Confidenzialità**: Ethernet permette agli utenti della stessa rete di catturare ("sniffare") il traffico altrui. La cifratura è necessaria per ottenere confidenzialità.

**Authentication**: si distingue in:
- **Data Origin Authentication**: chi riceve i dati deve essere sicuro che la fonte sia quella dichiarata
- **Peer Entity Authentication**: chi riceve le informazioni deve essere sicuro che la controparte sia quella attesa e non un impostore nella stessa rete

### 11.5 Non-Repudiation

Previene che il mittente o il destinatario possano negare una comunicazione avvenuta. Implementata tramite **firme digitali**: quando un messaggio è inviato, il destinatario ottiene una prova crittografica che il messaggio è stato inviato dal mittente dichiarato.

### 11.6 Anonymity

Capacità di utilizzare un sistema senza che esso sia in grado di identificare il mittente. Esempi di servizi di anonimato: **Tor** (The Onion Router).

### 11.7 Modello del Sistema di Sicurezza

Il flusso per progettare un sistema sicuro:
1. Identificare gli **attori** (mittente, destinatario, attaccanti potenziali)
2. Identificare gli **asset** da proteggere e il loro valore
3. Scegliere i **security services** da offrire
4. Definire un **attacker model** e decidere quali rischi accettare
5. Scegliere i **meccanismi tecnici** necessari
6. **Iterare** continuamente il processo (la sicurezza non è uno stato stático)

---

## 12. Crittografia Applicata

### 12.1 Funzioni di Hash

Una **funzione di hash** è una funzione unidirezionale che prende un input di lunghezza arbitraria e produce un **digest** (impronta) di lunghezza fissa.

```
H : {0,1}* → {0,1}^n     (es. SHA-256: n = 256 bit)
```

**Proprietà richieste**:
- **Preimage resistance**: dato `D`, trovare `X` tale che `H(X) = D` deve essere computazionalmente impossibile
- **Weak collision resistance**: dato `X`, trovare `X'` tale che `H(X') = H(X)` deve essere computazionalmente impossibile. Se l'input è modificato anche di poco, il digest deve cambiare completamente e in modo imprevedibile (*avalanche effect*)
- **Strong collision resistance**: trovare due qualsiasi `X` e `X'` tali che `H(X) = H(X')` deve essere computazionalmente impossibile (richiede ~`2^(n/2)` tentativi per il *birthday paradox*)

> ⚠ **Collisioni**: poiché il dominio (input possibili) è molto più grande del codominio (digest), le collisioni esistono teoricamente. Le proprietà sopra garantiscono che sia impossibile trovarle in pratica.

**Esempio applicativo**: Ubuntu pubblica il digest SHA-256 del file ISO sul proprio sito ufficiale. Chi scarica il file da una fonte non ufficiale può calcolare localmente il digest e verificare che corrisponda → integrità garantita.

### 12.2 HMAC (Hash-based Message Authentication Code)

Il problema di usare un semplice hash per garantire l'integrità è che un attaccante potrebbe modificare il messaggio E ricalcolare il digest. Per ovviare a questo, Alice e Bob concordano una **chiave segreta condivisa** `K`.

```
HMAC(M, K) = H( (K ⊕ opad) || H( (K ⊕ ipad) || M ) )
```

dove `ipad` e `opad` sono costanti di padding standard.

**Pro**: il digest può essere inviato sullo stesso canale del messaggio; garantisce sia integrità sia autenticazione (solo chi conosce `K` può generare un HMAC valido).

**Contro**: Alice e Bob necessitano di un canale sicuro per scambiarsi la chiave segreta inizialmente.

### 12.3 Principio di Kerckhoffs

L'algoritmo di cifratura deve essere **pubblico**. Il segreto risiede nella **chiave**, non nell'algoritmo. Algoritmi "segreti" non sono sicuri a lungo termine; è più sicuro un algoritmo ampiamente analizzato e verificato dalla comunità scientifica.

**Golden rules della crittografia**:
1. Non usare mai algoritmi di cifratura non noti o non pubblicati
2. Non usare funzioni di cifratura closed-source
3. Usa solo le funzioni più diffuse e più testate
4. Usa protocolli standard; non reinventare la ruota
5. Supporta solo le versioni più aggiornate dei protocolli

### 12.4 Cifrari Storici

**Cifrario di Cesare** (sostituzione semplice): ogni lettera è sostituita da un'altra a distanza fissa nell'alfabeto. Facilmente attaccabile con brute force (solo 25 shift possibili) o analisi delle frequenze.

**Cifrario di trasposizione**: le colonne del testo sono permutate secondo una chiave. Preserva le frequenze delle lettere → vulnerabile all'analisi statistica.

**One-Time Pad (OTP)**: chiave binaria casuale lunga quanto il messaggio; `C = M ⊕ K`. Teoricamente perfetto (information-theoretically secure), ma praticamente inutile: per scambiare la chiave serve un canale sicuro, che potrebbe essere usato direttamente per il messaggio.

### 12.5 Cifrari Moderni: Feistel e AES

I cifrari moderni combinano **sostituzione** (S-box) e **trasposizione** (permutazione) in più round per minimizzare la correlazione statistica tra plaintext e ciphertext. L'**algoritmo di Feistel** è alla base di molti cifrari storici (DES). **AES** (Advanced Encryption Standard) è il cifrario a chiave simmetrica più usato oggi.

### 12.6 Protocollo Sicuro con Chiave Simmetrica

Esempio di scambio sicuro, segreto e autenticato:

**Mittente (Alice)**:
1. Cifra il plaintext `M` con AES e chiave `K`: ottiene `C`
2. Calcola `HMAC(C, K)` → digest `D`
3. Invia `(C, D)` a Bob

**Ricevente (Bob)**:
1. Ricalcola `HMAC(C, K)` → `D'`
2. Se `D' = D`: il messaggio non è stato modificato (**integrità e autenticazione**)
3. Decifra `C` con AES e chiave `K`: ottiene `M` (**confidenzialità**)

> ⚠ Usare chiavi separate per HMAC e per la cifratura riduce la superficie di attacco.

### 12.7 Gestione Sicura delle Password

Le password non devono mai essere memorizzate in chiaro. La sequenza di best practice:

1. **Hash semplice** (`H(password)`): vulnerabile a rainbow table attack se il database viene compromesso
2. **Salted hash** (`H(password || salt)`) con salt casuale per ogni utente: annulla le rainbow table precompilate
3. **Slow hash** (es. bcrypt, scrypt, Argon2): funzione di hash iterata migliaia di volte → aumenta il costo computazionale di ogni tentativo di brute force

### 12.8 CRAM-MD5

Protocollo di autenticazione basato su challenge-response:
1. Bob invia una challenge `C` (stringa casuale, spesso un timestamp)
2. Alice calcola `D = HMAC(C, K)` e invia `("Alice", D)` a Bob
3. Bob recupera la chiave di Alice dal database, ricalcola `D'` e verifica che `D = D'`

Vantaggi rispetto all'invio della password in chiaro: la password non viene mai trasmessa. Oggi è **deprecato** perché:
- L'autenticazione non è mutuale (Alice non ha garanzia che stia parlando con il vero Bob)
- MD5 non è più considerato sicuro
- Vulnerabile a brute force offline (Eve intercetta `C` e `D` e può provare password offline)

### 12.9 Diffie-Hellman Key Exchange

DH è un protocollo che permette ad Alice e Bob di concordare una **chiave simmetrica segreta** senza scambiarsi mai informazioni segrete:

1. Alice sceglie pubblicamente un numero primo `n` e una radice primitiva `g` modulo `n`
2. Alice genera un numero segreto `a` e Bob genera un numero segreto `b`
3. Alice manda a Bob: `m = gᵃ mod n`
4. Bob manda ad Alice: `r = gᵇ mod n`
5. Alice calcola: `K = rᵃ mod n = gᵃᵇ mod n`
6. Bob calcola: `K = mᵇ mod n = gᵃᵇ mod n`

La chiave condivisa `K = gᵃᵇ mod n` non viene mai trasmessa. La sicurezza si basa sulla difficoltà computazionale del **logaritmo discreto**: conoscendo `m`, `g` e `n`, è computazionalmente impossibile ricavare `a`.

> ⚠ **Attacco MiTM attivo**: se Eve può modificare il traffico, può intercettare e sostituire sia `m` sia `r`, negoziando chiavi separate con Alice e con Bob. DH garantisce confidenzialità solo se usato insieme a un meccanismo di autenticazione.

### 12.10 Public Key Cryptography (Crittografia Asimmetrica)

Nella **crittografia a chiave pubblica** (PKE, Public Key Encryption), ogni entità ha una coppia di chiavi:
- **Chiave pubblica** (`PubA`): può essere distribuita liberamente
- **Chiave privata** (`PrivA`): deve rimanere segreta

Proprietà fondamentale:
- Ciò che è cifrato con `PubA` può essere decifrato solo con `PrivA`
- Ciò che è cifrato con `PrivA` può essere decifrato con `PubA` (usato per le firme digitali)

**RSA** è l'algoritmo PKE più noto. È computazionalmente oneroso rispetto ad AES, quindi in pratica:
- RSA si usa solo per scambiarsi in modo sicuro una **chiave di sessione simmetrica**
- Tutti i dati successivi sono cifrati con AES usando quella chiave

### 12.11 Firma Digitale

La firma digitale sfrutta la crittografia asimmetrica invertita:

```
Firma:    C = {D}_{PrivA}    (Alice cifra il digest con la sua chiave privata)
Verifica: D' = {C}_{PubA}   (chiunque decifra con la chiave pubblica di Alice)
          D' == H(M) → firma valida → messaggio autenticato e integro
```

Solo Alice conosce `PrivA`, quindi solo lei può generare una firma valida. La firma garantisce:
- **Autenticazione**: il messaggio proviene da Alice
- **Non-repudiation**: Alice non può negare di aver firmato

> ⚠ Anche la firma digitale è vulnerabile a MiTM se Eve può sostituire la chiave pubblica di Alice.

### 12.12 Public Key Infrastructure (PKI) e Certificati X.509

Il problema fondamentale della crittografia asimmetrica: come fidarsi che una chiave pubblica appartiene veramente a chi dice di averla generata?

**Web of Trust**: rete sociale di fiducia in cui i partecipanti certificano l'identità degli altri. Un partecipante firma la chiave pubblica di un altro, attestando la sua identità.

**PKI**: infrastruttura con una o più **Certification Authority (CA)** fidate da tutti. Il processo:

1. Alice genera la coppia `(PubA, PrivA)`
2. Alice crea una **CSR** (Certificate Signing Request): `CSR = ("Alice", {("Alice", PubA)}_{PrivA})`
3. La CA verifica l'identità di Alice e firma il certificato: `Cert = (CSR, metadata, {H(CSR, metadata)}_{PrivCA})`
4. Il certificato viene distribuito in formato standard **X.509**

Per validare un certificato, Bob:
1. Controlla che il certificato sia a nome di Alice
2. Controlla che non sia scaduto
3. Verifica di fidarsi della CA e possiede la sua chiave pubblica
4. Decifra la firma con `PubCA` → ottiene il digest `D`; calcola `H(Data)` → `D'`; verifica che `D = D'`

**Gerarchia delle CA**: le CA sono organizzate ad albero. Ogni CA riceve un certificato dalla CA di livello superiore. Le **Root CA** certificano se stesse e sono poche decine nel mondo (i loro certificati sono hardcoded nei browser e nei sistemi operativi).

**Certificate Revocation List (CRL)**: lista di certificati revocati prima della scadenza (es. per compromissione della chiave privata), firmata dalla CA e pubblicata periodicamente.

> 💡 Il sistema PKI riduce il numero di entità di cui fidarsi: non ogni sito visitato, ma solo un piccolo numero di Root CA.

---

## 13. IPv4, IPv6 e NAT

### 13.1 IPv4

Internet Protocol v4 (IPv4) si trova al network layer. Si appoggia a due tipi di data link:
- **Point-to-point**: comunicazione tra due dispositivi (es. PPPoE su DSL)
- **LAN**: ogni dispositivo ha un indirizzo univoco (MAC address); i frame possono essere unicast o broadcast

**Struttura dell'indirizzo IPv4**:
- 32 bit, rappresentati in **notazione decimale puntata** (es. `192.168.1.1`)
- Diviso in **netid** (identifica la rete) e **hostid** (identifica l'host nella rete)
- La **subnet mask** in notazione CIDR (`/N`) indica quanti bit del prefisso identificano la rete: `2^(32−N)` indirizzi totali

**Indirizzi speciali**:
- `hostid = 0`: indirizzo di rete (non assegnabile a host)
- `hostid = tutti 1`: indirizzo di broadcast della sottorete
- `255.255.255.255`: **Limited Broadcast Address** (broadcast su tutta la rete locale)
- `224.0.0.0/4`: indirizzi **multicast**
- `0.0.0.0/8`: indirizzo sorgente non ancora configurato (usato durante DHCP)
- `127.0.0.0/8`: **loopback** (il pacchetto non esce dall'host)
- `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`: indirizzi **privati**, non routable su Internet pubblico
- `169.254.0.0/16`: **link-local** (usato quando non si ha ancora un IP assegnato da DHCP)

**Header IPv4**:
- **Total Length**: lunghezza totale del pacchetto
- **Identification**: usato per riassemblare i frammenti
- **Flags**: `DF` (Don't Fragment), `MF` (More Fragments)
- **Fragment Offset**: offset del frammento nel pacchetto originale
- **TTL** (Time to Live): decrementato di 1 a ogni hop; quando raggiunge 0, il pacchetto viene scartato e viene inviato un messaggio ICMP "Time Exceeded" al mittente (previene i routing loop)
- **Protocol**: identifica il protocollo del layer superiore (6 = TCP, 17 = UDP, 1 = ICMP)

### 13.2 Frammentazione IPv4

Quando un router deve inoltrare un pacchetto su un link con MTU inferiore:
- Divide il pacchetto in **frammenti** più piccoli
- Ogni frammento ha il suo header IP (20 byte di overhead aggiuntivo)
- Il bit `MF` è `1` per tutti i frammenti tranne l'ultimo
- Il `Fragment Offset` indica la posizione del frammento nel pacchetto originale (in unità da 8 byte)
- Il **riassemblaggio** avviene solo a destinazione

### 13.3 NAT (Network Address Translation)

Con soli ~4 miliardi di indirizzi IPv4 (`2³² ≈ 4,3 × 10⁹`), Internet ha esaurito gli indirizzi pubblici. Il **NAT** è la soluzione a breve termine.

**NAPT** (Network Address Port Translation): il router di bordo traduce gli indirizzi IP privati in un singolo indirizzo IP pubblico, usando le porte TCP/UDP per distinguere le connessioni.

Un **flusso** è identificato da una quintupla:
```
(protocollo, IP_sorgente, porta_sorgente, IP_destinazione, porta_destinazione)
```

Il router NAT mantiene una **tabella di traduzione** che mappa ogni connessione interna (IP privato + porta) a una porta del suo IP pubblico.

**Problema**: se due host interni vogliono connettersi allo stesso server esterno sulla stessa porta, il NAT rimappa la seconda connessione a un'altra porta.

**Limitazioni del NAT**:
- Rompe l'end-to-end connectivity: un host dietro NAT non può ricevere connessioni in entrata (a meno di configurare il port forwarding)
- Deve mantenere stato → problema di scalabilità con migliaia di connessioni
- Incompatibile con molti protocolli di cifratura end-to-end
- Introduce ritardo e complessità

### 13.4 IPv6

IPv6 è l'evoluzione di IPv4, progettata per risolvere il problema della scarsità di indirizzi.

**Principali differenze da IPv4**:
- Indirizzi da **128 bit** (→ ~3,4 × 10³⁸ indirizzi)
- Scritti in formato esadecimale separato da `:` (es. `2001:db8::1`)
- **Header semplificato** (lunghezza fissa 40 byte): campi rimossi o spostati in header estensioni
  - `Next Header` sostituisce il campo Protocol e permette di concatenare header estensioni
  - `Hop Limit` sostituisce TTL
  - **Nessun supporto alla frammentazione nei router** (solo il mittente può frammentare, usando l'header di estensione Fragment)
- **ICMPv6** sostituisce ARP e IGMP, oltre a svolgere le funzioni di ICMP
- Supporto obbligatorio per **IPsec** (encryption)

**Struttura di un indirizzo IPv6**:
- **Global Routing Prefix** (48 bit): identifica l'ISP
- **Subnet ID** (16 bit): identifica un cliente dell'ISP
- **Interface ID** (64 bit): identifica l'interfaccia specifica, spesso derivato dal MAC address

**Indirizzi speciali IPv6**:
- `::1/128`: loopback
- `fc00::/7`: Unique Local Unicast (simile agli indirizzi privati IPv4)
- `ff00::/8`: multicast
- `fe80::/10`: **Link Local Unicast (LLU)** – ogni host può calcolare automaticamente il proprio indirizzo LLU concatenando il prefisso `fe80::` al proprio MAC esteso a 64 bit

**SLAAC** (Stateless Address Autoconfiguration): il router pubblica periodicamente il prefisso di rete tramite Router Advertisement; gli host costruiscono automaticamente il proprio indirizzo globale concatenando il prefisso ricevuto all'Interface ID.

> 💡 Con IPv6 non è più necessario ottimizzare ossessivamente l'assegnazione delle sottoreti: lo spazio di indirizzi è praticamente illimitato.

### 13.5 Esercizi su Subnetting IPv4

**Esempio**: un'organizzazione ha bisogno di 15 host indirizzabili.

Servono almeno `log₂(15 + 2) = 5` bit per gli host → netmask `/27` (32 − 5 = 27), che fornisce `2⁵ = 32` indirizzi totali (30 utilizzabili, sottraendo rete e broadcast).

**Variable Length Subnet Masking (VLSM)**: invece di allocare un'unica sottorete di dimensione uniforme, si possono creare sottoreti di dimensioni diverse per ottimizzare l'uso degli indirizzi.

---

## 14. ICMP, OSPF e Routing Intra-dominio

### 14.1 ICMP (Internet Control Message Protocol)

ICMP è un protocollo di segnalazione che si appoggia direttamente al network layer (IP). Non trasporta dati dell'utente, ma messaggi di errore e di controllo della rete.

**Regola fondamentale**: se un messaggio ICMP genera un errore, non vengono inviati ulteriori messaggi ICMP per evitare loop di messaggi di errore.

**Header ICMP**: contiene il tipo di errore/evento tramite un codice numerico.

**Messaggi ICMP comuni**:
- `Type 3` – Destination Unreachable: la destinazione è irraggiungibile (sottotipi: host unreachable, port unreachable, fragmentation needed…)
- `Type 5` – Redirect: esiste un percorso migliore per raggiungere la destinazione
- `Type 11` – Time Exceeded: il TTL è scaduto (pacchetto in loop o percorso troppo lungo)
- `Type 8/0` – Echo Request/Reply: usato dal comando `ping` per testare la raggiungibilità

**ping**: usa ICMP Echo Request/Reply per misurare latenza e raggiungibilità di un host.

**traceroute**: invia pacchetti con TTL progressivamente crescente (1, 2, 3, …). Ogni router che scarta il pacchetto per TTL esaurito risponde con un ICMP Time Exceeded, permettendo di mappare il percorso.

### 14.2 ICMPv6

In IPv6, ICMPv6 svolge anche le funzioni di:
- **ARP** (risoluzione MAC→IP): tramite il protocollo **NDP** (Neighbor Discovery Protocol)
- **IGMP** (gestione dei gruppi multicast)
- `Packet Too Big`: sostituisce "Fragmentation Needed" di IPv4; permette la **Path MTU Discovery**

### 14.3 OSPF (Open Shortest Path First)

OSPF è il principale protocollo di routing **intra-dominio**, basato su Link State Routing.

**Caratteristiche**:
- Divide la rete in **aree**: ogni area è una porzione contigua di rete
- Due tipi di router:
  - **Internal router**: connesso solo ad altri router all'interno della stessa area
  - **Border router** (ABR): appartiene a più aree contemporaneamente
- **Backbone area** (Area 0): l'area centrale che raccoglie tutti i border router; ogni comunicazione inter-area deve transitare per l'area 0
- All'interno di ogni area non-backbone, i router distribuiscono la topologia scambiandosi **Link State Packet**
- Tra aree diverse, il routing usa il meccanismo DV tra i border router
- Supporta sia IPv4 sia IPv6

### 14.4 ISIS (Intermediate System to Intermediate System)

Altro protocollo di routing intra-dominio basato su link state, alternativo a OSPF. Usato principalmente dai grandi ISP (più scalabile di OSPF per reti molto grandi).

---

## 15. Autonomous System e BGP

### 15.1 Autonomous System (AS)

Un **Autonomous System** (AS) è un insieme di prefissi IP gestiti da uno o più operatori di rete sotto il controllo di una singola entità amministrativa. Ogni AS:
- Riceve un numero univoco (**ASN**) dall'IANA (Internet Assigned Numbers Authority)
- Ha almeno un **PoP** (Point of Presence): punto di accesso fisico alla rete
- È responsabile per specifici prefissi IP
- Usa internamente il proprio protocollo di routing (OSPF, ISIS…)

**Tipi di AS**:
- **Stub AS** (single-homed): connesso a un solo AS; non offre servizio di transito; es. università, aziende
- **Stub AS** (multi-homed): connesso a più AS ma non offre transito; più resiliente
- **Transit AS**: connette altri AS e offre routing dei dati tra di loro; es. ISP

### 15.2 Connessioni tra AS

- **Private peering**: connessione point-to-point tra router di due AS; entrambi pagano l'hardware
- **IXP** (Internet Exchange Point): centri dati dove gli AS installano i propri router e si connettono tra loro tramite un'infrastruttura condivisa; più efficienti in quanto con un singolo PoP un AS si connette a molti altri
- **Transit agreement**: accordo commerciale in cui un AS (il cliente) paga un altro AS (il provider) per connettività verso Internet
- **Peering agreement**: accordo (spesso gratuito) tra due AS per scambiarsi traffico reciprocamente senza costi aggiuntivi; conveniente per AS geograficamente vicini con traffico simmetrico

### 15.3 BGP (Border Gateway Protocol)

BGP è il protocollo di routing **inter-dominio** (tra AS diversi). All'interno di ogni AS si usa il protocollo scelto liberamente; tra AS si usa obbligatoriamente BGP.

**BGP è un protocollo a path vector**:
1. Come DV, ogni router annuncia prefissi raggiungibili
2. Ma include **l'intero percorso AS** (AS path), non solo la distanza → previene i loop
3. Invia aggiornamenti solo quando qualcosa cambia (non periodicamente)
4. Un **UPDATE BGP** contiene solo le modifiche (annunci di nuovi prefissi o ritiro di prefissi esistenti)

**Sessioni BGP**: i router BGP si connettono tramite TCP (porta 179). I tipi di messaggio:
- `OPEN`: stabilisce la sessione BGP
- `UPDATE`: annuncia o ritira prefissi
- `KEEPALIVE`: verifica che il peer sia ancora raggiungibile
- `NOTIFICATION`: segnala errori

**Politiche di export**:
- Nel rapporto cliente→provider: il cliente annuncia i suoi prefissi e le rotte dei suoi clienti
- Nel rapporto provider→cliente: il provider annuncia tutte le rotte che conosce
- Nel peering (costo condiviso): ogni AS annuncia solo i suoi prefissi interni e quelli dei suoi clienti (non le rotte apprese da altri provider)

**IBGP** (Internal BGP): BGP usato all'interno di un singolo AS per distribuire le informazioni BGP tra i router interni.

### 15.4 Path Prepending

Tecnica usata da un AS per influenzare il routing in entrata:
- Un AS con due link (es. 10 Gb/s primario e 1 Gb/s backup) può forzare il traffico verso il link primario annunciando il proprio AS number più volte nel path verso il link di backup
- Gli altri AS scelgono il path con l'AS path più corto → preferiscono il link primario
- In caso di guasto del link primario, il path backup diventa automaticamente il preferito

### 15.5 BGP Anycast

Più AS annunciano lo stesso prefisso IP. Ogni AS verso cui il prefisso viene propagato sceglie il percorso "migliore" (più corto in termini di AS path). In questo modo le richieste vengono automaticamente indirizzate all'istanza più vicina del servizio.

Usato per i **server DNS radice**: i 13 server DNS root (A–M) sono in realtà decine di istanze distribuite nel mondo grazie all'anycast.

> ⚠ BGP anycast non è adatto a servizi con stato (es. TCP): il routing potrebbe cambiare durante una sessione. È invece ideale per servizi request-response come DNS.

---

## 16. Livello Applicazione: DNS e HTTP

### 16.1 Struttura del Livello Applicazione

Un'applicazione è identificata da:
- **Indirizzo IP** del server che la ospita
- **Numero di porta** (transport layer) che la identifica all'interno del server

Quando due applicazioni comunicano, la sessione è identificata univocamente dalla **coppia di porte** e dai **due indirizzi IP**.

### 16.2 Domain Name System (DNS)

Il DNS è il sistema distribuito che traduce i **domain name** (es. `www.unive.it`) in **indirizzi IP**.

**Struttura dei domain name**:
- Organizzazione gerarchica: `www.unive.it` (terzo livello, secondo livello, TLD)
- Il punto finale (`www.unive.it.`) indica il dominio **FQDN** (Fully Qualified Domain Name), che parte dalla radice dell'albero → indirizzo non ambiguo
- **TLD** (Top Level Domain): `.it`, `.com`, `.org`, `.edu`…
  - Ogni stato riconosciuto dall'ONU ha un TLD country code
  - I TLD sono delegati dalla **ICANN** (Internet Corporation for Assigned Names and Numbers) a organizzazioni chiamate **Domain Name Registrar**

**Delegazione e zone**: chi ottiene un dominio (es. `example.com`) è responsabile di tutti i suoi sottodomini. Può delegare sottodomini a terzi. Un **sottoalbero** dell'albero dei domain name è chiamato **zone**.

**Protocollo di trasporto**: UDP, porta 53.

**Risoluzione ricorsiva vs. iterativa**:
- Un resolver locale accetta query ricorsive dai client della propria rete
- Per query provenienti da reti esterne, il DNS risponde iterativamente (fornisce il riferimento al DNS autoritativo successivo)

**Processo di risoluzione** (per `example.com`):
1. Il browser non conosce l'indirizzo IP → contatta il suo resolver locale
2. Il resolver contatta uno dei 13 **root server** (indirizzi hardcoded)
3. Il root server risponde con il DNS autoritativo per `.com`
4. Il DNS autoritativo per `.com` risponde con il DNS autoritativo per `example.com`
5. Il DNS autoritativo per `example.com` risponde con l'indirizzo IP

**DNS Caching**: per evitare di ripetere l'intera catena di query, le risposte vengono memorizzate nella cache del resolver per il tempo indicato dal campo **TTL** del record.

**Messaggio DNS**:
- **Transaction ID**: numero che il client inserisce nel messaggio e il server replica nella risposta (per abbinare risposte a richieste parallele)
- **Flags**: QR (query/response), AA (Authoritative Answer), RD (Recursion Desired), RA (Recursion Available), RCODE (0 = OK, altri valori = errore)
- **Sezioni**: Questions, Answers, Authority, Additional

**Resource Records (RR) comuni**:
| Tipo | Descrizione |
|---|---|
| `A` | IPv4 address |
| `AAAA` | IPv6 address |
| `CNAME` | Canonical name (alias) |
| `MX` | Mail exchanger |
| `NS` | Name server |
| `PTR` | Reverse DNS |
| `TXT` | Testo generico (usato per SPF, DKIM…) |
| `SOA` | Start of Authority |

**Reverse DNS**: per tradurre un IP in nome, si usa il dominio `in-addr.arpa` (IPv4) o `ip6.arpa` (IPv6), registrando record `PTR`. Es: per l'IP `1.2.3.5` si fa una query su `5.3.2.1.in-addr.arpa`.

**Round-robin DNS**: lo stesso servizio può essere ospitato su più server. Il DNS restituisce gli IP in rotazione, distribuendo il carico.

**IP Anycast**: più server condividono lo stesso indirizzo IP; il routing BGP indirizza le richieste alla replica più vicina.

### 16.3 HTTP (HyperText Transfer Protocol)

**URI** (Uniform Resource Identifier): stringa che identifica univocamente una risorsa sul Web.
- **URL** (Uniform Resource Locator): URI che include anche dove recuperare la risorsa
- **URN** (Uniform Resource Name): URI che fornisce solo il nome, non la posizione

**Struttura di un URL**:
```
scheme://[user:password@]host[:port]/path[?query][#fragment]
```
- `scheme`: protocollo da usare (http, https, ftp, mailto…)
- `host`: nome del dominio o IP del server
- `path`: percorso della risorsa sul server (sintassi Unix)
- `query`: parametri aggiuntivi (`?param=value&param2=value2`)
- `fragment`: ancora all'interno del documento (`#section`)

**Metodi HTTP**:
- `GET`: richiede una risorsa
- `HEAD`: come GET ma restituisce solo gli header (utile per controllare se una risorsa è cambiata)
- `POST`: invia dati al server
- `PUT`, `DELETE`, `PATCH` (HTTP/1.1+): operazioni sulle risorse

**Codici di stato HTTP**:
- `1xx`: informational
- `2xx`: success (es. `200 OK`, `201 Created`)
- `3xx`: redirection (es. `301 Moved Permanently`, `304 Not Modified`)
- `4xx`: client error (es. `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`)
- `5xx`: server error (es. `500 Internal Server Error`, `503 Service Unavailable`)

**Connessione persistente** (`Keep-Alive`): con il campo `Connection: keep-alive` nell'header, una singola connessione TCP può essere usata per più richieste HTTP consecutive, riducendo l'overhead del three-way handshake.

> ⚠ HTTP è **stateless**: non mantiene stato tra richieste successive. Ogni richiesta è indipendente.

**Cookie**: meccanismo per mantenere stato tra richieste. Il server genera un cookie e lo invia al client tramite l'header `Set-Cookie`. Il client include il cookie in ogni richiesta successiva tramite l'header `Cookie`. Tipi di uso: session management, personalizzazione, tracking.

> ⚠ **Rischio di sicurezza**: se Eve intercetta il cookie di Alice, può impersonarla. Soluzione: usare HTTPS (TLS) per cifrare la comunicazione.

**Proxy Server**: cache locale nella rete del client che salva le pagine visitate. Il browser si connette al proxy invece che al server originale; se la pagina è in cache e non è scaduta, viene servita dal proxy senza contattare il server. Vantaggi: minore latenza, minore uso di banda.

**Reverse Proxy**: proxy lato server, non visibile al client. Riduce il carico sul server backend (cache delle risposte, bilanciamento del carico, terminazione TLS).

### 16.4 HTTP/2

- Binario invece di testuale: più efficiente da parsare
- **Multiplexing**: più richieste/risposte in parallelo sulla stessa connessione TCP (risolve il problema HoL – Head-of-Line blocking di HTTP/1.1)
- **Server push**: il server può inviare risorse al client prima che le richieda (es. inviare CSS e JavaScript insieme all'HTML)
- **Header compression** (HPACK): riduce l'overhead degli header ridondanti

---

## 17. Posta Elettronica e Anti-Spam

### 17.1 Architettura della Posta Elettronica

**Componenti**:
- **MUA** (Mail User Agent): programma client che formatta e visualizza le email (es. Thunderbird, Outlook)
- **MSA** (Mail Submission Agent): riceve le email dal MUA del mittente e le passa al primo MTA; gestisce l'autenticazione del mittente
- **MTA** (Mail Transfer Agent): server che trasferisce le email da un dominio all'altro; usa il protocollo SMTP
- **MDA** (Mail Delivery Agent): consegna la email nella mailbox del destinatario
- **MDA server**: rende disponibili le email al MUA del destinatario tramite POP3 o IMAP

**Protocolli**:
- **RFC 5322**: formato dei messaggi email (header e body)
- **RFC 2045 (MIME)**: estensioni per messaggi multipart e allegati binari
- **RFC 5321 (SMTP)**: trasferimento email tra MTA
- **RFC 1939 (POP3)**: recupero email dal server
- **RFC 9051 (IMAP4)**: accesso alle email sul server

### 17.2 Struttura di un Messaggio Email

**Header**: campi come `From:`, `To:`, `Subject:`, `Date:`, `Message-ID:`, `In-Reply-To:`, `Received:`
- `Message-ID`: identificatore univoco del messaggio
- `In-Reply-To`: usato nelle risposte, contiene il Message-ID del messaggio a cui si risponde
- `Received:`: aggiunto da ogni MTA che gestisce il messaggio (utile per il debugging)
- `X-...`: campi personalizzati aggiunti da client o server

**MIME** (Multipurpose Internet Mail Extensions): permette di includere contenuti non ASCII nel corpo della email:
- `MIME-Version`: versione dello standard MIME
- `Content-Type`: tipo del contenuto (es. `text/plain`, `text/html`, `multipart/mixed`, `image/jpeg`)
- `Content-Transfer-Encoding`: come è codificato il contenuto (es. `base64` per dati binari, `quoted-printable`)
- Le parti multipart possono essere annidate in una struttura ad albero

### 17.3 SMTP (Simple Mail Transfer Protocol)

Protocollo testuale basato su TCP (porta 25). Il client invia comandi ASCII; il server risponde con codici a tre cifre:
- `2xx`: successo
- `3xx`: successo, ma richiede ulteriore input
- `4xx`: errore temporaneo (riprovare)
- `5xx`: errore permanente (non riprovare)

**Comandi principali**: `EHLO` (estensione di `HELO`), `MAIL FROM:`, `RCPT TO:`, `DATA`, `QUIT`

**Closed relay**: un MTA dovrebbe accettare email solo per il proprio dominio locale o da utenti autenticati. Un **open relay** (MTA che accetta e forwards email per qualsiasi dominio senza autenticazione) è facilmente sfruttabile per inviare spam.

### 17.4 Tecniche Anti-Spam

**SPF** (Sender Policy Framework): il DNS del dominio contiene un record SPF con la lista degli IP autorizzati a inviare email da quel dominio. Il server ricevente controlla che l'IP del mittente sia nella lista SPF del dominio indicato nel `MAIL FROM`.

**DKIM** (DomainKeys Identified Mail): l'MSA del mittente firma il messaggio con la propria chiave privata. La chiave pubblica è pubblicata nel DNS (`selector._domainkey.domain`). Il server ricevente verifica la firma.

**DMARC** (Domain-based Message Authentication, Reporting and Conformance): unifica SPF e DKIM, definendo una policy (pubblicata nel DNS) su cosa fare con i messaggi che non superano i controlli SPF/DKIM.

**Tecniche di rilevamento spam lato IP**:
- **FQDN check**: il dominio dichiarato nell'`HELO` deve corrispondere all'IP di connessione tramite DNS forward e reverse
- **IP blacklisting**: blocco di IP noti per inviare spam
- **Graylisting**: alla prima connessione, il server risponde con un errore temporaneo (4xx). Gli spammer non riprovano; un MTA legittimo riprova dopo alcuni minuti e viene inserito in whitelist

**Sender spoofing**: un attaccante può falsificare il campo `From:` per rendere l'email più credibile. SPF, DKIM e DMARC lavorano insieme per rendere questo attacco più difficile.

---

## 18. TLS/SSL e Socket di Rete

### 18.1 Dove Applicare la Cifratura

| Livello | Pro | Contro |
|---|---|---|
| Applicazione | Semplice da implementare | Lascia scoperti i layer sottostanti |
| Trasporto | Cifra TCP end-to-end | I middlebox (firewall, traffic shaper) devono poter leggere l'header TCP |
| IP (IPsec/VPN) | Cifra tutto il traffico IP | Richiede supporto su tutti i router del percorso |
| Connessione | Massima protezione | Non scalabile |

**Soluzione di compromesso**: **TLS** (Transport Layer Security), un livello sopra TCP che lo rende sicuro senza richiedere modifiche ai layer inferiori.

### 18.2 SSL/TLS

**SSL** (Secure Sockets Layer) è il predecessore di TLS. La versione corrente standard è **TLS 1.3** (RFC 8446).

**Servizi offerti da TLS**:
- **Autenticazione**: tramite certificati X.509
- **Confidenzialità**: tramite cifratura a chiave simmetrica (AES)
- **Integrità**: tramite HMAC

**Protocolli "S"**: HTTPS (porta 443), POP3S, IMAPS, SMTPS sono versioni sicure dei protocolli originali, che usano TLS su porte dedicate.

### 18.3 TLS 1.2 Handshake

```
Client                              Server
  |                                   |
  |---[ClientHello]------------------->|  (versioni supportate, suite crittografiche, random)
  |<--[ServerHello, Certificate,-------|  (suite scelta, certificato X.509 del server)
  |    ServerHelloDone]               |
  |---[ClientKeyExchange,------------>|  (chiave di sessione cifrata con PubServer)
  |    ChangeCipherSpec, Finished]    |
  |<--[ChangeCipherSpec, Finished]----|  (conferma, hash dei messaggi precedenti)
  |                                   |
  | (dati cifrati con AES/HMAC)       |
```

`ChangeCipherSpec` conferma la conclusione dello scambio delle chiavi; `Finished` contiene l'hash di tutti i messaggi precedenti per verificare che non siano stati modificati da un attaccante MiTM.

### 18.4 TLS 1.3 Miglioramenti

- Solo **5 suite crittografiche** sicure (AEAD – authenticated encryption with associated data)
- **Handshake in un solo RTT** (invece di due di TLS 1.2)
- Possibilità di **0-RTT** per riprendere sessioni precedenti
- **Forward Secrecy obbligatoria**: viene usata una chiave effimera Diffie-Hellman per ogni sessione → anche se in futuro la chiave privata del server venisse compromessa, le sessioni passate resterebbero riservate

### 18.5 Network Sockets

Un **socket** è l'interfaccia di programmazione (API) che un'applicazione usa per comunicare tramite la rete.

**Byte Order**: i protocolli Internet usano **big-endian** (byte più significativo per primo). Le funzioni di conversione sono necessarie su macchine little-endian:
- `htons()`, `htonl()`: host-to-network (16 e 32 bit)
- `ntohs()`, `ntohl()`: network-to-host (16 e 32 bit)

**API Socket TCP**:
1. `socket()`: crea il socket (specificando famiglia di indirizzi e protocollo)
2. `bind()`: [server] associa il socket a un IP e una porta
3. `listen()`: [server] marca il socket come passivo e imposta la coda di connessioni
4. `connect()`: [client] avvia il three-way handshake
5. `accept()`: [server] estrae una connessione dalla coda e restituisce un nuovo socket dedicato al client
6. `send()` / `recv()`: invio e ricezione dati
7. `close()`: chiude la connessione

**API Socket UDP** (più semplice, senza connessione):
1. `socket()`: crea il socket
2. `bind()`: [server] necessario per ricevere su una porta specifica
3. `sendto()`: invia dati specificando ogni volta l'indirizzo del destinatario
4. `recvfrom()`: riceve dati e restituisce anche l'indirizzo del mittente

**Server Concorrenti**:
- **Iterative server**: gestisce un client alla volta; gli altri attendono in coda
- **Concurrent server con fork()**: per ogni `accept()` che ritorna, il server padre chiama `fork()`. Il processo figlio gestisce la connessione con quel client e termina; il padre torna immediatamente in ascolto. Questo permette di gestire molti client simultaneamente.
- **Concurrent server con thread o I/O asincrono (select/epoll)**: alternativa a fork(), più efficiente

---

## 19. Ethernet

### 19.1 Livello Fisico

Il supporto fisico più comune per Ethernet è la **coppia di fili di rame intrecciata** (*twisted pair*). La torsione dei fili riduce le interferenze elettromagnetiche. Tipi di protezione:
- **UTP** (Unshielded Twisted Pair): senza schermatura
- **STP** (Shielded Twisted Pair): con schermatura metallica attorno a ogni coppia
- **FTP** (Foiled Twisted Pair): con foglio metallico
- **SFTP**: combinazione delle protezioni precedenti

### 19.2 MAC Address

Un **MAC address** (Media Access Control) identifica univocamente un'interfaccia di rete a livello data link. È composto da 48 bit:
- **1 bit** OUI di tipo: `0` = unicast, `1` = multicast/broadcast
- **1 bit** OUI: `0` = globally unique, `1` = locally administered
- **22 bit** OUI (Organizationally Unique Identifier): assegnato dall'IEEE al produttore
- **24 bit**: assegnato dal produttore alla singola NIC

Un indirizzo MAC è di **unicast** se il primo byte è pari (seconda cifra esadecimale del primo byte è 0, 2, 4, 6, 8, A, C, E).

I MAC address vengono estesi a 64 bit quando usati come Interface ID in IPv6.

### 19.3 Frame Ethernet

**Header** di un frame Ethernet (IEEE 802.3):
- `Preamble` + `SFD` (Start Frame Delimiter): sincronizzazione (7 + 1 byte)
- `Destination MAC address`: 6 byte
- `Source MAC address`: 6 byte
- `EtherType` / `Length`: 2 byte (indica il protocollo del payload, es. `0x0800` = IPv4, `0x86DD` = IPv6)
- `Payload`: 46–1500 byte
- `FCS` (Frame Check Sequence – CRC a 32 bit): 4 byte

Il MAC di destinazione è posizionato all'inizio del frame → la NIC può decidere immediatamente se scartare il frame o elaborarlo. Il CRC è alla fine → la NIC lo verifica durante la lettura del frame.

### 19.4 Switch Ethernet

Uno **switch** è un dispositivo che connette più segmenti Ethernet e fa forwarding selettivo dei frame (a differenza dell'hub, che fa broadcast su tutte le porte).

**Backward Learning Algorithm**:

```python
# Arrivo di frame F sulla porta P al tempo T
src = F.SourceAddress
dst = F.DestinationAddress

# Aggiornamento della tabella
Table[src] = (P, T)

# Forwarding
if isUnicast(dst):
    if dst in Table:
        ForwardFrame(F, Table[dst].port)
        return
    # Destinazione sconosciuta: flood
# Per broadcast, multicast o destinazione sconosciuta:
for port in Ports:
    if port != P:
        ForwardFrame(F, port)
```

Le entry nella tabella vengono rimosse periodicamente se non vengono aggiornate entro un timeout (per gestire lo spostamento dei dispositivi).

### 19.5 Spanning Tree Protocol (STP)

In una rete Ethernet con ridondanza (più percorsi tra due switch), si possono creare **loop di switching** che saturano la rete. L'STP risolve questo problema costruendo un **albero di copertura** (spanning tree) della topologia.

**Funzionamento**:
1. Ogni switch ha un **Bridge ID** (64 bit: 16 bit priorità configurabile + MAC address)
2. Lo switch con il Bridge ID minore diventa la **radice** (root)
3. Ogni switch determina la propria **root port** (la porta con il percorso a costo minimo verso la radice)
4. Per ogni segmento di rete viene eletto uno **designated port** (lo switch con il percorso più economico verso la radice)
5. Tutte le porte che non sono né root né designated vengono **bloccate** (non inoltrano traffico dati)

**BPDU** (Bridge Protocol Data Unit): messaggi di controllo inviati periodicamente da ogni switch a tutte le sue porte verso un indirizzo multicast specifico. Contengono `<Root_ID, Cost, Sender_ID, Port>`.

I BPDUs vengono confrontati con ordinamento lessicografico: vince il BPDU "migliore" (root ID minore; a parità, costo minore; a parità, sender ID minore; a parità, porta minore).

### 19.6 ARP (Address Resolution Protocol)

Quando un host vuole inviare un pacchetto IP a un host sulla stessa rete locale, deve conoscere il suo MAC address. ARP risolve questo problema:

1. **ARP Request**: broadcast su tutta la rete locale (`FF:FF:FF:FF:FF:FF`): "Chi ha l'IP `X.X.X.X`? Sono `Y.Y.Y.Y` con MAC `AA:BB:CC:DD:EE:FF`"
2. **ARP Reply**: l'host con l'IP `X.X.X.X` risponde in unicast direttamente all'indirizzo MAC del richiedente: "Sono io, il mio MAC è `11:22:33:44:55:66`"

Ogni host mantiene una **ARP cache** con le associazioni IP→MAC apprese di recente (le entry scadono dopo alcuni minuti).

**Comunicazione con host remoti**: se il destinatario è su una rete diversa, il pacchetto viene inviato al **gateway** (router). Il MAC di destinazione del frame sarà quello del router, mentre l'IP di destinazione nel pacchetto IP sarà quello dell'host finale.

### 19.7 DHCP (Dynamic Host Configuration Protocol)

DHCP permette a un host di configurare automaticamente la propria rete al momento del boot. Usa UDP (porta 67 per il server, 68 per il client).

**Procedura (DORA)**:

1. **Discover**: il client invia in broadcast (`0.0.0.0` → `255.255.255.255`) un DHCP Discover, includendo il proprio MAC nel campo Client ID
2. **Offer**: il server DHCP risponde con un'offerta contenente: IP proposto, subnet mask, durata del lease, IP del server DHCP
3. **Request**: il client risponde con un DHCP Request (ancora in broadcast, per notificare eventuali altri server DHCP), indicando l'IP del server scelto
4. **Acknowledgment**: il server conferma il lease con un DHCP ACK, che può includere anche: gateway predefinito, indirizzi DNS, durata del lease

---

## 20. Wi-Fi (IEEE 802.11)

### 20.1 Standard e Versioni

| Standard | Nome commerciale | Frequenza | Velocità max |
|---|---|---|---|
| 802.11b/g | Wi-Fi 4 | 2,4 GHz | 54 Mbps |
| 802.11n | Wi-Fi 4 | 2,4 / 5 GHz | 600 Mbps |
| 802.11ac | Wi-Fi 5 | 5 GHz | ~3,5 Gbps |
| 802.11ax | Wi-Fi 6/6E | 2,4 / 5 / 6 GHz | ~9,6 Gbps |
| 802.11be | Wi-Fi 7 | 2,4 / 5 / 6 GHz | ~23 Gbps |

Il Wi-Fi opera su bande **senza licenza** (ISM – Industrial, Scientific and Medical). Non sono esclusive → soffrono di interferenze da altri dispositivi. La potenza di trasmissione massima è limitata per legge.

**Canali**:
- **Banda 2,4 GHz**: 14 canali da 20 MHz, ma solo 3 non si sovrappongono (1, 6, 11) → massimo 3 reti indipendenti nello stesso ambiente
- **Banda 5 GHz**: 30 canali da 20 MHz in totale; in Europa, 19 sono disponibili; i canali possono essere aggregati (40, 80, 160 MHz) per aumentare la velocità
- **Banda 6 GHz** (Wi-Fi 6E/7): 7 canali da 160 MHz, praticamente assenza di congestione

### 20.2 Tipi di Frame 802.11

- **Management**: comunicazione di controllo (autenticazione, associazione, beacon)
- **Control**: coordinazione dell'accesso al canale (ACK, RTS, CTS)
- **Data**: dati utente (cifrati e autenticati)

### 20.3 Topologie Wi-Fi

**BSS** (Basic Service Set): rete Wi-Fi elementare con un singolo **AP** (Access Point) e un insieme di stazioni associate. Tutto il traffico transita attraverso l'AP (topologia a stella). Ogni BSS è identificato da un **BSSID** (48 bit, derivato dal MAC dell'AP).

**SSID** (Service Set Identifier): nome "leggibile" della rete Wi-Fi (stringa fino a 32 caratteri), configurabile dall'amministratore.

**ESS** (Extended Service Set): insieme di BSS collegati da un **Distribution System** (DS, tipicamente una rete Ethernet cablata). Le stazioni di un ESS sono sulla stessa rete IP e possono comunicare tra loro. Condividono lo stesso SSID pur avendo BSSID diversi → roaming seamless tra AP.

### 20.4 Processo di Associazione

Per associarsi a una rete Wi-Fi, una stazione deve:

1. **Scanning** (rilevamento dei BSS disponibili):
   - **Passive scan**: la stazione ascolta ogni canale per un breve periodo, aspettando i **Beacon frame** (inviati periodicamente dall'AP con BSSID, SSID e parametri del BSS)
   - **Active scan**: la stazione invia una **Probe Request** su ogni canale; gli AP rispondono con una **Probe Response**

2. **Autenticazione**: vari metodi (Open Authentication, WPA2-PSK, WPA3, 802.1X/EAP…)

3. **Associazione**: la stazione si associa formalmente all'AP scelto; l'AP assegna un **Association ID** (AID)

### 20.5 MAC Header per Frame Dati 802.11

Contiene:
- **Frame Control**: tipo di frame, flag retry, bit From-DS e To-DS
- **Duration/ID**: campo usato per il NAV (Network Allocation Vector)
- **3 indirizzi MAC**: l'interpretazione dipende dai bit From-DS e To-DS (sorgente, destinazione, AP)
- **Sequence Control**: numero di sequenza per il controllo delle ritrasmissioni
- **CRC** (checksum)

Un pacchetto IP viene incapsulato in un frame 802.11 con un header **LLC** (Logical Link Control) che specifica il tipo di protocollo.

### 20.6 Accesso al Mezzo: CSMA/CA

Poiché il mezzo radio è condiviso, 802.11 usa **CSMA/CA** (Carrier Sense Multiple Access / Collision Avoidance).

**DCF** (Distributed Coordination Function): modalità di accesso distribuita.

**Procedura**:
1. Prima di trasmettere, la stazione ascolta il canale (**carrier sensing**)
2. Se il canale è **IDLE** (libero) per un intervallo **DIFS** (DCF InterFrame Space), la stazione trasmette
3. Se il canale è **BUSY**, la stazione aspetta che diventi idle, poi attende ancora DIFS prima di entrare nella fase di backoff
4. La stazione ricevente, se il frame è corretto (CRC ok), attende un intervallo **SIFS** (Short IFS, più breve di DIFS) e invia un **ACK**

**NAV** (Network Allocation Vector): ogni frame include nel campo Duration/ID la stima del tempo necessario per completare lo scambio frame+ACK. Le stazioni che sentono il frame aggiornano il proprio NAV e differiscono la trasmissione per quel tempo → evita collisioni da stazioni che non vedono la stazione ricevente.

### 20.7 Backoff e Collisioni

Se più stazioni terminano il DIFS contemporaneamente e iniziano a trasmettere → **collisione** → l'AP non invia l'ACK.

Per ridurre la probabilità di collisioni sincronizzate, ogni stazione sceglie un **tempo di attesa casuale** (*backoff*) nell'intervallo `[0, CW]` (CW = Contention Window). La stazione decrementa un contatore a ogni slot idle e trasmette quando raggiunge 0.

**Exponential Backoff**: dopo ogni collisione, CW viene raddoppiato: `CW_i = 2 × CW_{i−1}` (fino a `CW_max`). Dopo una trasmissione riuscita, CW torna al minimo (`CW_min`). Dopo un numero massimo di ritrasmissioni fallite, il frame viene scartato.

### 20.8 Hidden Node e RTS/CTS

**Problema del nodo nascosto**: se A e C non si "sentono" reciprocamente (sono fuori range l'uno dell'altro) ma entrambi comunicano con B, CSMA/CA non funziona → collisioni a B.

**Soluzione: RTS/CTS** (Request-To-Send / Clear-To-Send):
1. A invia un frame **RTS** all'AP (contiene il NAV per l'intera transazione)
2. L'AP risponde con un **CTS** (inviato in broadcast; contiene il NAV)
3. Tutte le stazioni in range dell'AP ricevono il CTS e aggiornano il NAV → non trasmettono per la durata indicata
4. A trasmette; l'AP invia l'ACK

Il meccanismo RTS/CTS introduce overhead aggiuntivo e viene usato solo per frame di grandi dimensioni (dove il canale rimarrebbe occupato a lungo).

### 20.9 PCF (Point Coordination Function)

Modalità centralizzata: l'AP invia alle stazioni un piano di trasmissione, allocando slot temporali a ciascuna (simile a TDMA). Non è il meccanismo principale (più rigido di DCF).

### 20.10 Modulazione Adattiva (MCS)

La velocità di trasmissione Wi-Fi non è fissa ma si adatta alla qualità del canale tramite l'**Adaptive Coding and Modulation**:

- **MCS** (Modulation and Coding Scheme): coppia (modulazione, rate di codifica) che definisce le prestazioni attese
- Con segnale forte → QAM-256 o QAM-1024 → più bit per simbolo → velocità più alta
- Con segnale debole → BPSK o QPSK → meno bit per simbolo, ma più robusto al rumore

Ogni produttore implementa un algoritmo proprietario per selezionare il MCS ottimale in base alle condizioni del canale.

### 20.11 Clear Channel Assignment (Multi-channel)

Quando una stazione vuole trasmettere su più canali aggregati:
1. Invia un **RTS** su ciascuno dei canali che vuole usare
2. L'AP risponde con un **CTS** per ogni canale disponibile

Questo meccanismo funziona anche tra reti diverse che condividono la stessa banda: ciascuna rete usa il canale in momenti diversi, prevenendo le collisioni.

---

## Glossario dei Termini Principali

| Termine | Definizione |
|---|---|
| **ACK** | Acknowledgment – conferma di ricezione |
| **AP** | Access Point – punto di accesso Wi-Fi |
| **ARP** | Address Resolution Protocol – traduce IP in MAC |
| **AS** | Autonomous System – dominio di routing autonomo |
| **BGP** | Border Gateway Protocol – routing inter-dominio |
| **BSS** | Basic Service Set – rete Wi-Fi elementare |
| **CA** | Certification Authority – autorità di certificazione |
| **CRC** | Cyclic Redundancy Check – checksum robusto |
| **CSMA/CA** | Carrier Sense Multiple Access / Collision Avoidance |
| **DHCP** | Dynamic Host Configuration Protocol |
| **DKIM** | DomainKeys Identified Mail – autenticazione email |
| **DMARC** | Domain-based Message Authentication, Reporting and Conformance |
| **DNS** | Domain Name System |
| **DoS** | Denial of Service – attacco di negazione del servizio |
| **DV** | Distance Vector – tipo di algoritmo di routing |
| **ECN** | Explicit Congestion Notification |
| **ESS** | Extended Service Set – insieme di BSS |
| **FCS** | Frame Check Sequence – checksum del frame |
| **FQDN** | Fully Qualified Domain Name |
| **GBN** | Go-Back-N – protocollo di sliding window |
| **HMAC** | Hash-based Message Authentication Code |
| **HTTP** | HyperText Transfer Protocol |
| **IXP** | Internet Exchange Point |
| **LSDB** | Link State Database |
| **LSP** | Link State Packet |
| **MAC** | Media Access Control – indirizzo fisico di rete |
| **MiTM** | Man in the Middle – tipo di attacco |
| **MSL** | Maximum Segment Lifetime |
| **MSS** | Maximum Segment Size |
| **MTU** | Maximum Transmission Unit |
| **MUA** | Mail User Agent |
| **MTA** | Mail Transfer Agent |
| **NAT** | Network Address Translation |
| **OSPF** | Open Shortest Path First – protocollo di routing intra-dominio |
| **OTP** | One-Time Pad – cifrario perfetto ma impraticabile |
| **PKI** | Public Key Infrastructure |
| **RIP** | Routing Information Protocol – protocollo DV |
| **RTO** | Retransmission Timeout |
| **RTT** | Round-Trip Time |
| **SDU** | Service Data Unit – unità di dati tra layer |
| **SNR** | Signal-to-Noise Ratio – rapporto segnale/rumore |
| **SPF** | Sender Policy Framework – anti-spam |
| **SR** | Selective Repeat – protocollo di sliding window |
| **STP** | Spanning Tree Protocol – prevenzione loop Ethernet |
| **TCP** | Transmission Control Protocol |
| **TCB** | Transmission Control Block – stato di una connessione TCP |
| **TLS** | Transport Layer Security |
| **TTL** | Time to Live – campo nei pacchetti IP |
| **UDP** | User Datagram Protocol |
| **URI** | Uniform Resource Identifier |
| **URL** | Uniform Resource Locator |
| **VPN** | Virtual Private Network |
| **WPA** | Wi-Fi Protected Access – standard di sicurezza Wi-Fi |

---

*Fine della dispensa. Tutti gli argomenti coperti dai file `reti1.pdf` – `reti23.pdf` sono stati integrati, corretti e ampliati.*
