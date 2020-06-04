https://www.danielwerner.dev/set-up-ci-cd-for-your-laravel-app-with-github-travis-and-deployer/
https://web-nancy.fr/deployer-laravel-5-avec-ssh-git-et-composer-sur-un-hebergement-mutualise-ovh-de-a-a-z/
https://oncletom.io/2016/travis-ssh-deploy/

1. Installer Travis.
    ```sh
    gem install travis
    ```

2. Se connecter à travis (en utilisant tes identifiants GitHub).
    ```sh
    travis login --com
    ```

3. Générer les clés.
    ```sh
    ssh-keygen -t rsa -b 4096 -C 'build@travis-ci.org' -f ./deploy_rsa
    ```

4. Encrypter la clé privée et l'ajouter à Travis.
    ```sh
    travis encrypt-file --com deploy_rsa --add
    ```
    Ceci va mettre à jour le fichier `.travis.yml`. Ajouter le fichier `deploy_rsa.env` au git repository, mais assure-toi de NE PAS ajouter `deploy_rsa`!

5. Se connecter au serveur via SSH et ouvrir le fichier `~/.ssh/authorized_keys`.
    ```sh
    nano /home/deploy/.ssh/authorized_keys
    ```

6. Copier le contenu de la clé publique `deploy_rsa.pub` dans `~/.ssh/authorized_keys` sur le serveur puis appuyer sur Ctrl+X puis O puis Enter pour sauvegarder. On peut entrer la commande `cat ~/.ssh/authorized_keys` pour s'assurer que la clé a bien été enregistrée.

7. Supprimer les clés.
    ```sh
    rm -f deploy_rsa deploy_rsa.pub
    ``` 

8. [Nécessaire? On peut juste les installer dans le worker puis exporter le tout]
    ```sh
    cd ~
    curl -sS https://getcomposer.org/installer | php
    ```
    À ce stade, composer est sous la forme d'un fichier `composer.phar`. Pour utiliser la commande `composer`, on peut ajouter un alias `alias composer="php ~/composer.phar"` dans `~/.bash_profile`. On peut se passer de cela et utiler la commande `php ~/composer.phar` à la place de `composer`.

9. Editer `.travis.yml`. Normalement, Travis a automatiquement ajouté la commande `openssl`; pas grand chose à changer à part, vers la fin, exporter le décryptage dans le dossier temporaire: `-out deploy_rsa` devient `-out /tmp/deploy_rsa`. On fait ça pour éviter de déployer la clé. Puis il y a d'autres lignes à ajouter. Voir le fichier `.travis.yml`.

10. 