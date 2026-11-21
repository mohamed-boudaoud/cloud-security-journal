# Jour 1 — Les bases du réseau

## Notions
- IP privée (10.x, 172.16-31.x, 192.168.x) : ne fonctionne que
  dans un réseau local. IP publique : unique sur Internet
- Passerelle par défaut : la porte de sortie du réseau local
- NAT : la box remplace mon IP privée par son IP publique
- DNS : traduit un nom en adresse IP
- Client (navigateur) / serveur (site web), protocole, paquet

## Commandes
| Commande | Rôle |
|---|---|
| `ipconfig` | Voir mon IP et ma passerelle |
| `ping <cible>` | Tester si une machine répond |
| `nslookup <nom>` | Demander une IP au DNS |
| `tracert <cible>` | Voir le chemin jusqu'à une machine |

## Résultats
- IP locale : 192.168.1.196 / passerelle (box) : 192.168.1.254
- Ping box : 12 ms en moyenne (max 39 ms)
- Ping 8.8.8.8 : 42 ms en moyenne (max 86 ms)
- google.com → 142.251.209.142 (IPv4) et 2a00:1450:4007:816::200e (IPv6)
- tracert 8.8.8.8 : 9 sauts (box → FAI → transit Level3 → réseau Google)

## Observations
- Latence variable vers la box : connexion en Wi-Fi.
  Le tracert montre ~9 ms jusqu'à Google : la variation vient du Wi-Fi
- Windows préfère l'IPv6 quand il est disponible (`ping -4` pour forcer l'IPv4)
- Le DNS utilisé est la box ; réponse « ne faisant pas autorité » = réponse relayée ou en cache
- Les `*` dans un tracert = routeurs qui ne répondent pas, pas une panne

## Diagnostic
Si `ping 8.8.8.8` marche mais pas `ping google.com` → panne DNS.

## Ce que j'ai retenu
Ce qui m'a surpris: je pensais qu'une adresse IP privée était une adresse "cachée"
Ce que je sais faire: Appliquer la méthode de base de diagnostic réseau

Quiz : 7/10, puis 10/10 au rattrapage.
