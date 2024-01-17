# Progetto rete scolastica

Quest'anno vogliamo simulare una rete scolastica sia utilizzando Packet Tracer che usando delle Raspberry PI ed altri dispositivi di rete fisici. Questo esempio si può adattare facilmente ad altri tipi di rete da ufficio.

## Analisi dei requisiti
Per prima cosa, come in ogni progetto dobbiamo identificare:
1. gli utenti che andranno a usare il servizio
1. quali dispositivi usano
1. come si connettono alla rete
1. quali sono le loro esigenze

Individuiamo due tipologie di utenti:
- docenti
- studenti

Entrambi accederanno alla rete con il proprio dispositivo, nel modello BYOD (Bring Your Own Device); i dispositivi previsti sono quindi, principalmente:

- smartphone, sia Android che Apple
- tablet, sia Android che Apple
- laptop, sia Android che Apple che Linux

Tutti i dispositivi devono connettersi alla rete tramite WiFi.

Le esigenze specifiche sono:

- docenti: devono avere il massimo della banda disponibile, deve essere una rete logicamente separata da quella degli studenti, la loro qualità di connessione non deve essere influenzata negativamente dall'accesso di molti studenti in contemporanea
- studenti: l'accesso può avvenire anche in modo controllato dai docenti

## Soluzione proposta

Nel nostro caso semplificato, possiamo immaginare che docenti e studenti accedano ad access point separati, che affluiscono allo stesso switch. Le porte dello switch sono configurate in modo che il traffico in entrata dagli access point dei docenti finisca sulla rete VLAN dei docenti, e quello in entrata dagli access point degli studenti finisca nella VLAN degli studenti.

> Nella realtà, oggi non è necessario avere access point fisicamente diversi per poter realizzare questa soluzione. Gli AP moderni possono essere configurati in modo da generare SSID diversi e instradare il traffico di un certo SSID direttamente in una VLAN specificata.

Ipotizziamo quindi di avere due sottoreti:

- 192.168.1.0/24 per i docenti
- 192.168.2.0/24 per gli studenti







