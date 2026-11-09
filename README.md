# Vannel Kaptouom

**Ingénieur réseaux, systèmes et cloud** — OpenStack · Kubernetes · Ansible · automatisation réseau · sécurité

Je conçois et j'exploite seul une infrastructure de niveau PME, en parallèle de mon poste :
deux serveurs physiques, un cloud privé OpenStack, des clusters Kubernetes, un SIEM et la chaîne
d'automatisation qui les tient. Cette page présente ce qu'elle contient et ce que j'y ai appris.

Le code, les configurations et la documentation d'exploitation sont dans des dépôts **privés** :
ils décrivent une infrastructure réelle, exposée à l'Internet. Je les montre volontiers en entretien.

---

## L'infrastructure en bref

| Domaine | Ce qui tourne |
|---|---|
| Virtualisation | 2 serveurs physiques (HPE DL360 Gen9, Dell T320) sous XCP-ng, Xen Orchestra, sauvegardes quotidiennes recopiées d'un serveur à l'autre |
| Cloud privé | OpenStack 2024.2 en haute disponibilité sur 12 VM : Kolla-Ansible, Ceph, Galera, HAProxy et Keepalived |
| Conteneurs | Kubernetes (kubeadm, Calico), K3s, Rancher multi-cluster, préproduction par Kustomize |
| Automatisation | Ansible piloté par GitLab CI puis Rundeck : branche protégée, six contrôles avant tout déploiement, détection de dérive chaque soir |
| Secrets | OpenBao en grappe de trois nœuds, accès SSH par certificats de 30 minutes |
| Sécurité | FortiGate, Cloudflare, WAF BunkerWeb, SSO Authentik, certificats Let's Encrypt renouvelés et posés automatiquement |
| SIEM / SOC | Elastic 9.4 (journaux normalisés ECS, 18 règles de détection versionnées, MITRE ATT&CK), DFIR-IRIS |
| Supervision | Prometheus, Grafana, Alertmanager, Zabbix 7.0 |

## Projets

### Aruba Manager — gestion centralisée des switchs Aruba
Application web (FastAPI) qui pilote un parc de switchs **AOS-S et AOS-CX** depuis une seule console :
inventaire par site et par groupe, sauvegardes de configuration avec différences entre versions,
configuration en masse par modèles Jinja2 (avec simulation et sauvegarde préalable), mises à jour
d'OS par vagues, actions rapides sur les VLAN et les ports. Rôles et journal d'audit, import
Excel, mode démo sans matériel. Tests automatisés et chaîne GitLab CI.
`Python` `FastAPI` `Netmiko` `API REST AOS-CX` `Docker`

### Cloud OpenStack haute disponibilité
Déploiement par Kolla-Ansible sur 12 VM, stockage Ceph, base Galera sur trois contrôleurs.
Bascule des adresses virtuelles d'un répartiteur de charge à l'autre validée en direct, sans
requête perdue. Le cloud a ensuite été déplacé d'un serveur physique à l'autre. Une sonde
crée toutes les heures un réseau jetable pour prouver que le cloud sait encore créer.

### Application web sur Kubernetes
Cluster monté avec kubeadm, puis migration de l'application depuis l'ancien cluster, volumes
persistants compris. Chaîne GitLab CI en six étapes : lint, tests sur une vraie base PostgreSQL,
construction des images étiquetées par commit, scan Trivy bloquant, déploiement précédé d'une
sauvegarde, publication de version manuelle.

### SIEM et SOC
SIEM Elastic sur une machine dédiée, migré de version en version sans perte de données.
Journaux normalisés ECS (SSH, sudo, comptes, pare-feu, WAF), règles de détection écrites en
code et corrélées (« force brute puis succès », « source muette »), traitement des incidents
dans DFIR-IRIS avec délais mesurés.

## Quelques dépannages

- **Onze paniques de l'hyperviseur en 21 heures.** Les vidages mémoire désignaient la gestion
  mémoire de la virtualisation imbriquée. Trois plantages n'avaient laissé aucune trace : le chien
  de garde matériel avait redémarré la machine avant que Xen puisse écrire.
- **48 Go de RAM disparus.** L'iLO affichait toutes les barrettes en bon état. `dmidecode` montrait
  deux barrettes écartées au démarrage : la cause était l'apprentissage mémoire rapide du BIOS,
  pas une barrette défectueuse.
- **Un cluster Kubernetes qui tombait sans raison apparente.** Deux équipements répondaient à
  l'ARP pour la même adresse en DMZ. Une table ARP ne montre que le gagnant : c'est `arping` qui a
  révélé les deux réponses.
- **Des certificats qui ne se renouvelaient plus depuis deux semaines.** Une mise à jour Helm
  avait effacé en silence les réglages DNS de cert-manager ; ils sont désormais inscrits dans
  la configuration versionnée.

## Organisation du code

| Forge | Rangement |
|---|---|
| GitLab | `kasy-uk/infrastructure` (documentation, Ansible, SIEM) · `kasy-uk/applications` (applications) |
| GitHub | miroirs des projets applicatifs, et cette page |

Branches principales protégées, tout changement par demande de fusion, contrôles automatiques
avant fusion, journal des versions dans chaque projet.

## Contact

[LinkedIn](https://www.linkedin.com/in/vannel-kaptouom)
