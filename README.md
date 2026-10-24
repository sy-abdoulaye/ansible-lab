# Ansible IOS-XR

Playbooks Ansible de collecte sur des routeurs Cisco IOS-XR :

- `playbooks/iosxr_facts.yaml` : facts (hostname, version, modèle) et
  configuration des interfaces via `cisco.iosxr.iosxr_facts` ;
- `playbooks/pyATS.yaml` : `show version` et `show bgp summary` parsés par
  pyATS/Genie, via le rôle `ansible-pyats`.

## Installation

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
ansible-galaxy install -r requirements.yaml   # Rôle ansible-pyats (déjà présent dans playbooks/roles)
```

## Inventaire

L'inventaire réel n'est pas versionné. Partez de l'exemple :

```bash
cp inventory/hosts.example.yaml inventory/hosts.yaml   # hosts.yaml est ignoré par git
vi inventory/hosts.yaml                                 # Renseigner les routeurs
```

## Identifiants

Aucun identifiant n'est stocké dans le dépôt. `inventory/group_vars/routers.yaml`
lit le login et le mot de passe dans les variables d'environnement
`NET_USERNAME` et `NET_PASSWORD` :

```bash
cp .env.example .env          # .env est ignoré par git
vi .env                       # Renseigner NET_USERNAME et NET_PASSWORD
set -a; source .env; set +a
```

Si `NET_USERNAME` ou `NET_PASSWORD` sont vides, la connexion SSH échoue à
l'authentification.

## Utilisation

Les commandes se lancent depuis la racine du dépôt, pour que `ansible.cfg`
soit pris en compte (inventaire et chemin des rôles).

```bash
ansible-inventory --graph                          # Vérifier l'inventaire
ansible-playbook playbooks/iosxr_facts.yaml
ansible-playbook playbooks/pyATS.yaml --limit R1
```

## Structure

```text
ansible.cfg                       # Inventaire par défaut, roles_path, timeouts
inventory/
├── hosts.example.yaml            # Exemple d'inventaire (groupe « routers »)
├── hosts.yaml                    # Inventaire réel, ignoré par git
└── group_vars/routers.yaml       # Connexion network_cli et identifiants (variables d'environnement)
playbooks/
├── iosxr_facts.yaml
├── pyATS.yaml
└── roles/ansible-pyats/          # Rôle CiscoDevNet/ansible-pyats
```

## Crédits

Le rôle `playbooks/roles/ansible-pyats` provient de
[CiscoDevNet/ansible-pyats](https://github.com/CiscoDevNet/ansible-pyats) et
reste sous sa propre licence (`playbooks/roles/ansible-pyats/LICENSE`).
