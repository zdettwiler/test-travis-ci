https://www.danielwerner.dev/set-up-ci-cd-for-your-laravel-app-with-github-travis-and-deployer/
https://web-nancy.fr/deployer-laravel-5-avec-ssh-git-et-composer-sur-un-hebergement-mutualise-ovh-de-a-a-z/
https://oncletom.io/2016/travis-ssh-deploy/

1. Installer Travis
    ```sh
    gem install travis
    ```

2. Se connecter à travis (en utilisant tes identifiants GitHub)
    ```sh
    travis login --org
    ```

3. Générer les clés
    ```sh
    ssh-keygen -t rsa -b 4096 -C 'build@travis-ci.org' -f ./deploy_rsa
    ```

4. Encrypter la clé privée et l'ajouter à Travis
    ```sh
    travis encrypt-file deploy_rsa --add
    ```
    Ceci va mettre à jour le fichier `.travis.yml`. Ajoute le ficier `deploy_rsa.env` au git repository, mais assure-toi de NE PAS ajouter `deploy_rsa`!

5. Supprimer la clé privée
    ```sh
    rm deploy_rsa
    ```

6. Connecte-toi au serveur via SSH et ouvre le fichier `~/.ssh/authorized_keys`
    `nano /home/deploy/.ssh/authorized_keys`

7. Copie le contenu de la clé publique `deploy_rsa.pub` dans `~/.ssh/authorized_keys` sur le serveur puis appuie sur Ctrl+X puis O puis Enter pour sauvegarder. Tu peux entrer la commande `cat ~/.ssh/authorized_keys` pour t'assure que la clé a bien été enregistrée.

8. Supprime la clé publique localement, on n'en a plus besoin.
    ```sh
    rm deploy_rsa.pub
    ```

9. [Nécessaire? On peut juste les installer dans le worker puis exporter le tout]
    ```sh
    cd ~
    curl -sS https://getcomposer.org/installer | php
    ```
    À ce stade, composer est sous la forme d'un fichier `composer.phar`. Pour utiliser la commande `composer`, on peut ajouter un alias `alias composer="php ~/composer.phar"` dans `~/.bash_profile`. On peut se passer de cela et utiler la commande `php ~/composer.phar` à la place de `composer`.

10. Editer `.travis.yml`. Normalement, travis a automatiquement ajouté la commande `openssl`; pas grand chose à changer à part, vers la fin, exporter le décryptage dans le dossier temporaire: `-out deploy_rsa` devient `-out /tmp/deploy_rsa`. On fait ça pour éviter de déployer la clé. Puis il y a d'autres lignes à ajouter. Voir le fichier.

11. 