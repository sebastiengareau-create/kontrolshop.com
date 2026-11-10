# Site www.kontrolshop.com

Site de vente du logiciel kontrolshop : une seule page statique (HTML, CSS et
un peu de JavaScript, sans dépendance ni compilation). À part de l'appli
(dépôt `sgwebsite`) : il ne lit aucune base et ne touche à aucun client.

```
index.html     la page (styles et script inclus)
img/           logos métal des déclinaisons (WebP), logo, icônes
robots.txt, sitemap.xml
.github/workflows/publier.yml   publication chez WHC
.htaccess      HTTPS et www obligatoires, cache (hébergement Apache de WHC)
```

## Voir le site sur son ordinateur

Ouvrir `index.html` dans le navigateur, tout simplement.

## Modifier

- **Textes** : directement dans `index.html` (sections `#declinaisons`,
  `#points-forts`, `#exemple`, `#questions`, `#contact`).
- **Couleurs des déclinaisons** : variables `--garage`, `--ferme`, `--copro`,
  `--premium` en haut du `<style>` — garder celles de `lib/marque.js`.
- **Logos** : générés depuis `public/marque/kontrolshop-metal-<secteur>.png`
  du dépôt `sgwebsite` :
  `convert … -resize 820x -strip -quality 82 img/metal-<secteur>.webp`.
- **Courriel de contact** : `info@sgwebsite.online` (3 endroits dans
  `index.html`). Le formulaire ouvre le logiciel de courriel du visiteur,
  déjà rempli — pas de serveur à entretenir.

## Mise en ligne : hébergement WHC (cPanel)

Le domaine est chez WHC. Le site y est **publié automatiquement** par GitHub
(`.github/workflows/publier.yml`) : chaque modification de ce
dépôt arrivée dans `main` est envoyée par FTP sécurisé, seulement les
fichiers changés. On peut aussi relancer à la main : GitHub → Actions →
« Publier kontrolshop.com » → Run workflow.

### À faire une seule fois

1. **cPanel → Comptes FTP** : créer un compte (ex. `deploiement`) dont le
   **Répertoire** est le dossier du site `www.kontrolshop.com`
   (`public_html` si c'est le domaine principal du compte cPanel — voir
   cPanel → Domaines, colonne « Racine du document »). Noter le serveur FTP
   affiché par « Configurer le client FTP » (souvent `ftp.kontrolshop.com`).
2. **GitHub → ce dépôt → Settings → Secrets and variables → Actions →
   New repository secret**, trois secrets :
   - `FTP_SERVEUR` : le nom d'hôte du serveur (cPanel, colonne de droite,
     ex. `xxxx.whc.ca`) — `ftp.kontrolshop.com` ne marche qu'une fois les DNS
     propagés
   - `FTP_UTILISATEUR` : le nom complet du compte (ex. `deploiement@kontrolshop.com`)
   - `FTP_MOT_DE_PASSE` : son mot de passe
3. Le site est envoyé dans `public_html/` du compte FTP (qui s'ouvre dans
   `/home/<compte cPanel>/`). Si `kontrolshop.com` a une autre « Racine du
   document » (cPanel → Domaines), ajouter dans l'onglet **Variables** :
   `FTP_DOSSIER` = ce chemin vu depuis le compte FTP, terminé par `/`.
4. **cPanel → SSL/TLS Status** : lancer AutoSSL pour `kontrolshop.com` et
   `www.kontrolshop.com` (certificat gratuit). `.htaccess` envoie tout vers
   `https://www.kontrolshop.com`.
5. GitHub → Actions → « Publier kontrolshop.com » → **Run workflow**.

Une branche `claude/…` fait seulement un **essai** : connexion FTP et liste
de ce qui serait envoyé, sans rien changer sur l'hébergement.

Tant que les secrets ne sont pas réglés, le workflow ne publie rien et
l'indique par un avertissement — il n'échoue pas.
