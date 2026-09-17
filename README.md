# Vannel Kaptouom

Ingénieur réseaux, systèmes et cloud. Je conçois et j'exploite seul une infrastructure de niveau PME,
en parallèle de mon poste. Cette page présente ce qu'elle contient et ce que j'y ai appris.

Le code, les configurations et la documentation d'exploitation restent dans des dépôts privés :
ils décrivent une infrastructure réelle, exposée à Internet. Je peux en montrer des extraits en entretien.

## L'infrastructure en bref

| Brique | Ce qui tourne |
|---|---|
| Virtualisation | 2 serveurs physiques (HPE DL360 Gen9, Dell T320) sous XCP-ng, Xen Orchestra |
| Cloud privé | OpenStack 2024.2 haute disponibilité sur 12 VM (Kolla-Ansible, Ceph, Galera, HAProxy/Keepalived) |
| Conteneurs | Kubernetes (kubeadm, Calico), K3s, Rancher multi-cluster |
| CI/CD | GitLab CI : tests, scan Trivy, sauvegarde avant déploiement, préproduction Kustomize |
| Sécurité | FortiGate, Cloudflare, WAF BunkerWeb, SSO Authentik, certificats Let's Encrypt |
| SIEM / SOC | Elastic 9.1 (24 sources, 13 règles de détection), DFIR-IRIS |
| Supervision | Prometheus, Grafana, Alertmanager |
| Automatisation | Ansible, Rundeck, Git |

## Projets

### Cloud OpenStack haute disponibilité
Déploiement par Kolla-Ansible sur 12 VM, stockage Ceph, base Galera sur trois contrôleurs.
Bascule des adresses virtuelles d'un load-balancer à l'autre validée en direct, sans requête perdue.
Le cloud a ensuite été déplacé d'un serveur physique à l'autre.

### Application web sur Kubernetes
Cluster monté avec kubeadm, puis migration de l'application depuis l'ancien cluster, volumes
persistants compris. Chaîne GitLab CI en six étapes : lint, tests sur une vraie base PostgreSQL,
build des images étiquetées par commit, scan Trivy bloquant, déploiement précédé d'une sauvegarde,
publication de version manuelle. Préproduction par surcharge Kustomize, pour qu'elle ne puisse
pas diverger de la production.

### SIEM et SOC
Migration du SIEM Elastic en 9.1 vers une machine dédiée, sans perte de données. Collecte de
24 sources (Filebeat, syslog), 13 règles de détection calibrées sur des événements rares plutôt
que volumineux, et un pont vers DFIR-IRIS pour le traitement des incidents.

## Quelques dépannages

- **Onze paniques de l'hyperviseur en 21 heures.** Les crash dumps désignaient la gestion mémoire
  de la virtualisation imbriquée. Trois plantages n'avaient laissé aucune trace : le chien de garde
  matériel avait redémarré la machine avant que Xen puisse écrire.
- **48 Go de RAM disparus.** L'interface iLO affichait toutes les barrettes en bon état. `dmidecode`
  montrait deux barrettes écartées au démarrage ; la cause était l'apprentissage mémoire rapide du BIOS,
  pas une barrette défectueuse.
- **Un cluster Kubernetes qui tombait sans raison apparente.** Deux équipements répondaient à l'ARP
  pour la même adresse en DMZ. Une table ARP ne montre que le gagnant : c'est `arping` qui a révélé
  les deux réponses.
- **Un registre d'images à l'arrêt depuis 15 heures sans alerte.** Docker démarrait avant containerd.

## Contact

[LinkedIn](https://www.linkedin.com/in/vannel-kaptouom)
