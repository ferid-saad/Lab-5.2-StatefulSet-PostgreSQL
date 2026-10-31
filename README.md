# Lab 5.2 : Base de données avec StatefulSet

**Auteur :** Ferid Saad
**Cours :** Kubernetes avec k3s, ISET Tozeur (Master DevOps & Cloud)
**Cluster :** k3s-vbox (master, worker1, worker2)
**Namespace :** tp-k8s

## Objectifs

- Déployer PostgreSQL avec un StatefulSet, un Service headless et un volume par Pod
- Injecter la configuration avec un ConfigMap et un Secret
- Démontrer que les données survivent à la suppression du Pod
- Observer les noms DNS d'un Service headless
- Constater que deux réplicas d'un StatefulSet ne partagent pas leurs données

## Architecture

ConfigMap `app-config` + Secret `db-credentials` -> StatefulSet `db` (Pod `db-0`, PostgreSQL 16) -> PVC `data-db-0` (local-path), exposé par le Service headless `db`.

## Étape 1 : Préparer la configuration

Création du namespace `tp-k8s`, du ConfigMap `app-config` (DB_HOST, DB_PORT, DB_NAME) et du Secret `db-credentials` (POSTGRES_USER, POSTGRES_PASSWORD).

```bash
k create namespace tp-k8s
k config set-context --current --namespace=tp-k8s
k apply -f app-config.yaml
k create secret generic db-credentials \
  --from-literal=POSTGRES_USER=appuser \
  --from-literal=POSTGRES_PASSWORD='ChangeMe-123'
k get configmap,secret
```

**Point de contrôle :**
- [x] Le ConfigMap `app-config` et le Secret `db-credentials` existent

![configmap-secret-existent](captures/etape1-configmap-secret.png)

