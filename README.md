# Analyseur de Paquets Réseau (Sniffer)

Un analyseur de paquets réseau développé en C sous Linux, permettant de capturer et d'analyser le trafic réseau en temps réel selon le modèle OSI.

**Auteur** : Junior Koudogbo  
**Type** : Projet personnel  
**Langage** : C  
**Plateforme** : Linux

---

## Description

Ce projet est un analyseur de paquets réseau (sniffer) qui permet de capturer et d'analyser le trafic réseau en temps réel ou depuis un fichier de capture. L'architecture du projet suit le modèle OSI, avec une séparation modulaire des différentes couches réseau.

### Fonctionnalités principales

- Capture de paquets en temps réel sur une interface réseau
- Analyse complète selon le modèle OSI (couches 2, 3, 4, 7)
- Support de nombreux protocoles réseau et applicatifs
- Filtrage BPF (Berkeley Packet Filter)
- Analyse de fichiers de capture (.pcap)
- Affichage structuré et coloré des informations

---

## Architecture

Le projet est organisé de manière modulaire selon les couches OSI :

```
sniffer_reseau/
├── main.c              # Point d'entrée, gestion de la capture
├── data_link.c/h       # Couche 2 - Analyse des trames Ethernet
├── network.c/h          # Couche 3 - Analyse IP et ARP
├── transport.c/h        # Couche 4 - Analyse TCP et UDP
└── application.c/h     # Couche 7 - Analyse des protocoles applicatifs
```

### Protocoles supportés

**Couche Liaison (L2)** :
- Ethernet (IPv4, IPv6, ARP)

**Couche Réseau (L3)** :
- IPv4 (avec options)
- ARP/RARP

**Couche Transport (L4)** :
- TCP (avec options détaillées)
- UDP

**Couche Application (L7)** :
- HTTP (port 80)
- DNS (port 53)
- FTP (ports 20, 21)
- SMTP (port 25)
- POP3 (port 110)
- IMAP (port 143)
- TELNET (port 23)
- BOOTP/DHCP (ports 67, 68)

---

## Prérequis

### Système d'exploitation
- Linux (testé sur distributions basées sur Debian/Ubuntu)

### Dépendances
```bash
sudo apt install libpcap-dev build-essential
```

---

## Installation

1. **Cloner le dépôt** (ou télécharger les fichiers)
```bash
git clone <url-du-repo>
cd sniffer_reseau
```

2. **Compiler le projet**
```bash
make
```

Pour une compilation avec tous les warnings activés :
```bash
make complete
```

3. **Nettoyer les fichiers compilés** (optionnel)
```bash
make clean
```

---

## Utilisation

### Syntaxe de base

```bash
sudo ./sniffer [-i <interface>] [-o <fichier>] [-f <filtre BPF>]
```

**Note** : Les privilèges root (sudo) sont nécessaires pour capturer le trafic réseau.

### Options disponibles

- `-i <interface>` : Spécifier l'interface réseau (ex: eth0, wlan0, lo)
- `-o <fichier>` : Analyser un fichier .pcap au lieu de capturer en temps réel
- `-f <filtre>` : Appliquer un filtre BPF (Berkeley Packet Filter)
- `-h` : Afficher l'aide

### Exemples d'utilisation

**Mode par défaut** (interface par défaut) :
```bash
sudo ./sniffer
```

**Capture sur une interface spécifique** :
```bash
sudo ./sniffer -i eth0
sudo ./sniffer -i wlan0
sudo ./sniffer -i lo  # Interface loopback
```

**Filtrage du trafic** :
```bash
# Capturer uniquement le trafic HTTP
sudo ./sniffer -f "tcp and dst port 80"

# Capturer uniquement le trafic TCP
sudo ./sniffer -f "tcp"

# Capturer le trafic vers une IP spécifique
sudo ./sniffer -f "host 192.168.1.1"

# Combinaison de filtres
sudo ./sniffer -f "tcp and port 443 and host 8.8.8.8"
```

**Analyse d'un fichier de capture** :
```bash
sudo ./sniffer -o capture.pcap
```

**Arrêt du programme** :
- Appuyez sur `Ctrl+C` pour arrêter la capture proprement

---

## Exemple de sortie

Le programme affiche les informations de chaque paquet de manière hiérarchique :

```
Paquet #1 -- Sat 2024-12-28 23:45:12 CET | longueur 74 octets

▭▭▭ Ethernet ▭▭▭
Destination: aa:bb:cc:dd:ee:ff
Source: 11:22:33:44:55:66
Type: IPv4 (0x0800)

    ▭▭▭ IPv4 ▭▭▭
    Version: 4
    IHL: 5 (20 bytes)
    Source: 192.168.1.1
    Destination: 192.168.1.100
    Protocol: TCP (0x06)

        ▭▭▭ TCP ▭▭▭
        Source port: 443
        Destination port: 54321
        Flags: SYN, ACK
        ...

            ▭▭▭ HTTP ▭▭▭
            ...
```

---

## Technologies utilisées

- **Langage** : C (standard GNU99)
- **Bibliothèque** : libpcap (Packet Capture Library)
- **Compilateur** : GCC
- **Build system** : Make

---

## Compétences acquises à travers ce projet:

- Compréhension approfondie du modèle OSI
- Maîtrise des protocoles réseau (Ethernet, IP, TCP, UDP, ARP)
- Programmation système Linux
- Manipulation de la mémoire et des pointeurs
- Utilisation de bibliothèques externes (libpcap)
- Architecture modulaire et organisation du code
- Gestion des erreurs et des signaux système
- Filtrage de paquets avec BPF

---

## Structure du code

### Fonctionnement

Le programme fonctionne selon un principe de **décapsulation en cascade** :

1. Capture d'un paquet via libpcap
2. Analyse de la couche Ethernet → identification du type de protocole
3. Décapsulation et analyse de la couche réseau (IP/ARP)
4. Décapsulation et analyse de la couche transport (TCP/UDP)
5. Décapsulation et analyse de la couche application

Chaque couche identifie le protocole de la couche supérieure et appelle la fonction d'analyse appropriée.

### Points techniques importants

- Utilisation de structures système standard (`struct ether_header`, `struct ip`, `struct tcphdr`, etc.)
- Conversion endianness avec `ntohs()` et `ntohl()`
- Accès direct aux en-têtes via casting de pointeurs
- Calcul des offsets pour naviguer entre les couches

---

## Pistes d'amélioration

- [ ] Support complet d'IPv6
- [ ] Interface graphique (GTK, Qt, ou web)
- [ ] Export des résultats (JSON, CSV, XML)
- [ ] Statistiques avancées (bande passante, protocoles les plus utilisés)
- [ ] Reconstruction de sessions TCP
- [ ] Détection d'anomalies basique
- [ ] Support de plus de protocoles (HTTPS/TLS, SSH, etc.)
- [ ] Threading pour améliorer les performances
- [ ] Reconstruction de paquets IP fragmentés

---

## Notes

- Le projet nécessite des privilèges root pour fonctionner (accès aux interfaces réseau)
- Testé sur Linux (Debian/Ubuntu)
- Le code suit une architecture modulaire facilitant l'ajout de nouveaux protocoles

---

## Licence

Ce projet est réalisé à titre personnel par Junior Koudogbo. Vous êtes libre de l'utiliser, le modifier et le distribuer selon vos besoins.

---

## Auteur

**Junior Koudogbo**

Projet réalisé dans le cadre d'un apprentissage personnel des réseaux informatiques et de la programmation système.

---

## Remerciements

- Bibliothèque [libpcap](https://www.tcpdump.org/) pour la capture de paquets
- Documentation des RFC pour les spécifications des protocoles

---

## Contact

Pour toute question ou suggestion concernant ce projet, n'hésitez pas à ouvrir une issue sur le dépôt GitHub.

---

**Note** : Ce projet est à des fins éducatives et de démonstration. Assurez-vous d'avoir l'autorisation appropriée avant de capturer du trafic réseau sur des systèmes qui ne vous appartiennent pas.

