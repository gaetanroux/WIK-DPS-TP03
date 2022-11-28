# WIK-DPS-TP03

---

- Créer un docker-compose avec pour seul service un container basé sur le Dockerfile créé dans le TP WIK-DPS-TP02

- Dans le **`docker-compose.yml`** :

```
# Version de l'API Docker Compose à utiliser pour lire ce fichier
version: "3.8"

# Déclaration des services
services:
  # Un service nommée api_typescript
  api_typescript:
    # Définition du Dockerfile à build et à utiliser
    build:
      context: .
      dockerfile: dockerfile-multistage
```

---

- Augmenter le nombre de réplicas à 4 pour ce service

- Dans le **`docker-compose.yml`** :

```
    # Nombre de réplicas souhaités du même container
    deploy:
      replicas: 4 # Ici on déploie 4 fois le container au sein du service api_typescript
```

---

- Modifier le docker-compose pour ajouter un reverse-proxy (nginx), seule le reverse-proxy doit être exposé sur votre hôte sur le port 8080

- Dans le **`docker-compose.yml`** :

```
 proxy:
    # L'image Docker a utilisé pour le service
    image: nginx:latest

    # Monter un fichier du répertoire courant de l'hôte
    # sur le service
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro

    # Configurer les ports exposés sur le réseau et
    # le port-forwarding entre l'hôte et le service
    ports:
      - 8081:80

    # Définir une relation de dépendance
    # Si api_typescript n'est pas prêt alors pas de proxy
    depends_on:
      - api_typescript

    # Configure le réseau du service
    # Il est accessible seulement via le réseau nommé
    # front-network
    networks:
      - front-network
```

---

- Configurer nginx (nginx.conf) pour loadbalancer les requêtes vers le service basé sur votre image

- Dans **`nginx.conf`** :

```
events {

}
http {
server {
    listen 80;
    location / {
        proxy_pass http://api_typescript:8080;
    }
    }
}
```

---

- Modifier le code de votre API pour afficher le hostname dans les logs à chaque requête sur /ping, lancer votre docker-compose.yaml et observer l'effet du l'équilibrage de charge

- Dans **`index.ts`** :

```
import os from "os"; <<<

// Method used to handle incoming requests
const requestListener = function (req: IncomingMessage, res: ServerResponse) {
  try {
    // Only send JSON if HTTP verb is GET and url is /ping
    if (req.method === "GET" && req.url === "/ping") {
      res.setHeader("Content-Type", "application/json");
      res.write(JSON.stringify(req.headers));
> > > console.log(os.hostname());
      res.end();
```

---

### Pour lancer le projet :

Cloner le dossier dans un repertoire de votre choix : https://github.com/gaetanroux/WIK-DPS-TP03.git

Lancer Vscode.
Tapez ces commandes :

- `docker compose -up --build`
- puis rdv dans `http://localhost:8081/ping`

---
