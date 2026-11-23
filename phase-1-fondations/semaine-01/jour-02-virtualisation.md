# Jour 2 — Le serveur, l'hyperviseur et la VM

Semaine 1 · Bases réseau, virtualisation et Git

## Ce que j'ai appris

### Le serveur physique
Un serveur est un ordinateur qui rend un service à d'autres machines (site web, annuaire, base de données). Il est conçu pour tourner 24 h/24 : double alimentation, disques en RAID, rangé en baie dans un datacenter.

Ses quatre ressources, les mêmes que celles qu'on loue dans Azure :

| Ressource | Rôle | À retenir |
| --- | --- | --- |
| CPU | Exécute les instructions | 1 cœur = 1 unité de calcul ; avec l'hyperthreading, 1 cœur = 2 threads |
| RAM | Mémoire de travail des programmes en cours | Rapide mais volatile ; pleine, elle déborde sur le disque (swap) et tout rame |
| Stockage | Conserve les données | HDD < SSD < NVMe ; pour une base de données, on regarde les IOPS |
| Carte réseau | Relie la machine au réseau | Identifiée par une adresse MAC unique |

### La virtualisation
Faire tourner plusieurs machines virtuelles, chacune avec son propre système, sur un seul serveur physique.
- Consolidation : un serveur bien rempli au lieu de dix sous-utilisés
- Isolation : une VM qui plante ou qui est piratée n'affecte pas les autres
- Snapshots : revenir en arrière en cas d'erreur

### L'hyperviseur
Le logiciel qui crée les VM, leur partage le matériel de l'hôte et les isole les unes des autres.

| Type | Où il s'installe | Exemples | Usage |
| --- | --- | --- | --- |
| Type 1 (bare metal) | Directement sur le matériel | ESXi, Hyper-V, KVM | Datacenters, cloud (Azure = Hyper-V) |
| Type 2 (hébergé) | Sur un système existant | VirtualBox, VMware Workstation | Tests sur son PC |

Le type 1 est plus performant et plus sûr : il n'y a pas de système hôte entre lui et le matériel, donc une couche et une surface d'attaque en moins.

### La VM et le vCPU
- Une VM = un fichier de disque virtuel (.vdi, .vhdx) + une configuration (vCPU, RAM, cartes réseau).
- Un vCPU n'est pas un processeur réservé : c'est du temps de calcul que l'hyperviseur donne à la VM sur un thread physique, à tour de rôle avec les autres VM.
- On peut promettre plus de vCPU qu'il n'y a de threads (surallocation). La RAM, elle, se suralloue mal.

### Le lien avec Azure
- Une VM B2s = 2 vCPU et 4 Go de RAM réservés sur un serveur Hyper-V de Microsoft, facturés à l'heure.
- Responsabilité partagée (IaaS) : Microsoft gère le matériel et l'hyperviseur ; moi, tout ce qui est dans la VM (système, mises à jour, pare-feu, applications, données).

## Vocabulaire

| Terme | Définition |
| --- | --- |
| Hôte | La machine physique qui héberge les VM et leur fournit son matériel |
| Invité | Le système d'exploitation installé dans une VM |
| Hyperviseur | Le logiciel qui crée les VM, leur partage le matériel et les isole |
| vCPU | Un processeur virtuel, qui tourne sur un thread physique |
| Surallocation | Promettre aux VM plus de ressources que le serveur n'en a |
| Snapshot | Un point de restauration d'une VM (pas une vraie sauvegarde) |

Appliqué à mon PC :

| Rôle | Chez moi |
| --- | --- |
| Hôte | Mon PC Windows (i5-1145G7, 16 Go) |
| Hyperviseur | VirtualBox |
| Invité | Ubuntu, dans la VM du jour 5 |

## Questions : mes réponses et les corrections

**1. VirtualBox, type 1 ou type 2 ?**
Ma réponse : type 2, car c'est un logiciel qui s'installe sur Windows. Azure ne l'utilise pas car Hyper-V est installé directement sur le serveur.
Correction : juste. Le pourquoi : passer par un système hôte ajoute une couche, ce qui coûte en performances et augmente la surface d'attaque.

**2. 4 VM de 10 Go sur un serveur de 32 Go ?**
Ma réponse : on sature la RAM, ça va ramer.
Correction : 4 × 10 = 40 Go pour 32 Go, soit 8 Go de trop, sans compter la RAM de l'hôte. Hyper-V (mémoire fixe) refuse de démarrer la VM qui ne tient plus ; VirtualBox la laisse démarrer, l'hôte swappe et tout rame. Solution : 6 Go par VM, ou mémoire dynamique.

**3. Cœur, thread, vCPU ?**
Ma réponse : cœur, je ne savais pas ; thread = processeur logique ; vCPU = le CPU d'une machine virtuelle.
Correction : un cœur est une unité de calcul physique dans le processeur. Un thread est un processeur logique (2 par cœur avec l'hyperthreading). Un vCPU est du temps de calcul donné à une VM sur un thread, pas un processeur réservé.

**4. Le serveur Azure qui héberge ma VM tombe en panne ?**
Ma réponse : il est remplacé par un autre serveur, c'est la haute disponibilité.
Correction : la VM s'arrête, puis Azure la redémarre sur un autre serveur, avec une coupure de quelques minutes. Ce n'est pas de la haute disponibilité : la haute disponibilité, c'est au moins deux VM dans des zones différentes, pour que le service continue. Microsoft répare le matériel et redémarre la VM ; concevoir la haute disponibilité et sauvegarder les données, c'est à moi.

**5. Pourquoi un snapshot avant d'installer Active Directory ?**
Ma réponse : pour garder une sorte de backup si l'installation se passe mal.
Correction : juste. Mais un snapshot n'est pas une vraie sauvegarde : il est stocké avec la VM, donc si le disque meurt, on perd les deux.

## Challenge

- Hôte, hyperviseur, invité : voir le vocabulaire ci-dessus. Pour retenir : l'hôte reçoit, l'invité est reçu, l'hyperviseur organise l'accueil.
- Une VM D2s_v5 a 2 vCPU et 8 Go de RAM.

Lire un nom de VM Azure :
- D = usage général, performance constante
- B = performance « par à-coups » (crédits accumulés au repos), moins cher, idéal pour un lab
- s = compatible SSD premium
- v5 = génération du matériel

## FinOps
En cloud, on paie des vCPU et de la RAM à l'heure. Une VM trop grosse coûte cher pour rien, une VM trop petite rame : trouver la bonne taille, c'est le rightsizing.
