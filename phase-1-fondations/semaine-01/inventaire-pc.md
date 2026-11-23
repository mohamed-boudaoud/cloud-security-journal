# Inventaire de mon PC hôte

Le PC qui fait tourner le homelab VirtualBox.

## Ressources

| Ressource | Valeur |
| --- | --- |
| Processeur | Intel Core i5-1145G7 à 2,6 GHz : 4 cœurs, 8 processeurs logiques |
| RAM | 16 Go |
| Stockage | SSD NVMe |
| Carte réseau | Intel Wi-Fi 6 AX201 |
| Virtualisation | Activée |
| Hyperviseur | VirtualBox (déjà installé) |

Commandes utilisées :

```powershell
Get-CimInstance Win32_Processor | Select Name, NumberOfCores, NumberOfLogicalProcessors
Get-NetAdapter
```

## Cartes réseau détectées

| Carte | Adresse MAC | Remarque |
| --- | --- | --- |
| Wi-Fi (Intel AX201) | 08-8E-90-XX-XX-XX | Préfixe 08-8E-90 = Intel |
| Bluetooth | 08-8E-90-XX-XX-XX | Même fabricant |
| VirtualBox Host-Only | 0A-00-27-XX-XX-XX | Carte virtuelle créée par VirtualBox |

Les 3 premiers octets d'une adresse MAC identifient le fabricant.

## Répartition du homelab

| Machine | RAM | vCPU |
| --- | --- | --- |
| Windows (hôte) | 4 Go | 2 threads gardés |
| CLIENT01 (Windows 11) | 4 Go | 2 |
| DC01 (Windows Server) | 3 Go | 2 |
| pfSense | 1 Go | 1 |
| WEB01 (Ubuntu) | 1 Go | 1 |
| Libre | 3 Go | — |
| Total | 16 Go | 8 threads |


Conclusion : tout le homelab tient en même temps, avec 3 Go de marge. En pratique, je n'allume que les VM utiles à chaque étape.

## Points d'attention

- Espace disque : prévoir au moins 80 à 100 Go libres (Windows 11 demande 64 Go minimum, Windows Server environ 32 Go).
- Wi-Fi : pas de problème avec le réseau interne et le NAT, mais le mode « accès par pont » fonctionne mal en Wi-Fi. Je ne l'utilise pas.
- Ne jamais donner tous les threads aux VM : l'hôte en a besoin.

## Les modes réseau de VirtualBox

| Mode | La VM parle avec… | Usage dans le homelab |
| --- | --- | --- |
| NAT | Internet uniquement, via le PC | WAN de pfSense, mises à jour |
| Réseau interne | Les autres VM uniquement | LAN et DMZ |
| Réseau privé hôte | Le PC uniquement | Peu utilisé |
| Accès par pont | Tout le réseau, comme un vrai PC | À éviter |
