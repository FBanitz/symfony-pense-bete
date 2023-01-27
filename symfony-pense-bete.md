# SYMFONY
Basé sur le tutoriel de [@davidHurtrel](https://github.com/davidHurtrel/)
## COMPOSER

- aller sur Getcomposer.org, lien "download" sur la page d'accueil
- Windows : télécharger l'exécutable et le lancer
- Mac : ouvrir un terminal et suivre les instructions
- pas besoin de réinstaller à chaque nouveau projet Symfony

## NOUVEAU PROJET SYMFONY

- dans un nouveau terminal :
```
composer create-project symfony/website-skeleton project_name
```

## GIT

- créer un dépôt distant sur GitHub
- dans un nouveau terminal :
```
git init
```
```
git remote add origin https://github.com/GitHub_user/repository_name.git
```
```
git add *
```
```
git commit -m "commit_name"
```
```
git pull origin master
```
```
git push origin master
```
- voir la liste des commits (flèche du bas pour naviguer, q pour quitter) :
```
git log
```

## RÉCUPÉRER UN PROJET
- créer un nouveau dossier dans www (ou htdocs)
- ouvrir un terminal puis accéder au dossier :
```
cd le_chemin_du_dossier_créé
```
```
git init
```
```
git remote add origin le_lien_du_depot_github
```
```
git pull origin master
```
- créer un nouveau fichier .env dans le dossier créé
- regénérer les dossiers var et vendor :
```
composer update
```

## SERVEUR LOCAL
- démarrer un serveur local :
```
symfony server:start
```
- accéder au site :
```
symfony open:local
```

## APACHE-PACK

- barre de débug / routing / .htaccess
- dans le terminal :
```
composer require symfony/apache-pack
```

## ASSETS
- installer yarn : https://classic.yarnpkg.com/lang/en/docs/install/
- installer Encore : 
```
yarn encore dev
```
- dans les fichier twigs, pour faire réfférence au path d'un asset:

```
{{ asset('chemin/du.fichier') }}
```


## CONTROLLER

- créer un contrôleur :
```
php bin/console make:controller
```

## BASE DE DONNÉES

- .env, ligne 31 : (dépend du serveur utilisé : WAMP, MAMP, XAMPP)
```
DATABASE_URL="mysql://root@127.0.0.1:3306/immobilier?serverVersion=5.7"
```
- créer la base de données :
```
php bin/console doctrine:database:create
```
- créer une entité (table) :
```
php bin/console make:entity
```
- migration (vérifier le fichier de version généré avant la deuxième commande) :
```
php bin/console make:migration
```
```
php bin/console doctrine:migrations:migrate
```

## FIXTURES

- installer le bundle :
```
composer require --dev orm-fixtures
```
- compléter le fichier src/DataFixtures/AppFixtures.php
- persist()
- flush()
- envoyer en bdd (en écrasant) :
```
php bin/console doctrine:fixtures:load
```
- envoyer en bdd (en ajoutant à la suite) :
```
php bin/console doctrine:fixtures:load --append
```
- utiliser un générateur de données :
```
composer require fakerphp/faker
```

## FORMULAIRE

- créer un nouveau formulaire :
```
php bin/console make:form
```
- ajouter un bouton de validation du formaulaire (dans le Form) :
```
->add('valider', SubmitType::class)
```

## LOGIN

- créer l'entité User :
```
php bin/console make:user
```
- migration
- créer "l'authentification" :
```
php bin/console make:auth
```
- 1
- Authenticator
- SecurityController
- yes
- dans config/packages/security.yaml, décommenter :
```
- { path: ^/admin, roles: ROLE_ADMIN }
```

## REGISTER

- créer le formulaire d'inscription :
```
php bin/console make:registration-form
```
- rediriger vers accueil quand on se connecte (src/Security/Authenticator.php, vers ligne 100) :
```
return new RedirectResponse($this->urlGenerator->generate('home'));
```
- installer rollerworks :
```
composer require rollerworks/password-strength-bundle
```
- src/Form/RegistrationFormType.php :
```
use Rollerworks\Component\PasswordStrength\Validator\Constraints\PasswordStrength;
```
```
new PasswordStrength();
```
- y ajouter les contraintes souhaitées (minLength, minStrength, messages...)

## ROUTER DEBUG

- voir toutes les routes :
```
php bin/console debug:router
```
- vérifier si une route existe (et obtenir ses infos) :
```
php bin/console router:match /le_chemin_de_la_route
```

## VARIABLES GLOBALES

- config/packages/twig.yaml :
```
twig:
    ...
    globals:
        copyright: '%app.copyright%'
```
- config/setvices.yaml :
```
parameters:
    ...
    app.copyright: 'Copyright &copy; 2021 - David HURTREL'
```
- templates :
```
{{ copyright|raw }}
```

## DROITS - RÔLES

- config/packages/security.yaml :
```
access_control:
    - { path: ^/admin/user, roles: ROLE_SUPER_ADMIN }
    ...
role_hierarchy:
    ROLE_ADMIN: ROLE_USER
    ROLE_SUPER_ADMIN: ROLE_ADMIN
```

## MESSAGES FLASH

- dans le controller :
```
$this->addFlash(
    'success',
    'La maison a bien été modifiée'
);
```
- où on veut afficher les messages :
```
{% for label, messages in app.flashes(['success', 'danger']) %}
    {% for message in messages %}
        <div class="flash-{{ label }}">
            {{ message }}
        </div>
    {% endfor %}
{% endfor %}
```

## EMAILS

- installer SwiftMailer :
```
composer require symfony/swiftmailer-bundle
```
- .env :
```
MAILER_URL=gmail://username:password@localhost
```
- créer le formaulaire de contact :
```
php bin/console make:form
```
- créer le controller associé
- afficher le formulaire dans une page / vue
- créer le template de mail (contact/emailContact.html.twig)
- désactiver l'envoi de mail (config/packages/swiftmailer.yaml) :
```
swiftmailer:
    disable_delivery: true
```
- choisir les adresses des destinataires pendant les tests (config/packages/swiftmailer.yaml) :
```
swiftmailer:
    delivery_addresses: ['david.hurtrel@gmail.com', ...]
```
- voir les mails dans la toolbar (config/packages/dev/web_profiler.yaml) :
```
intercept_redirect: true
```
(un message apparaît ensuite dans la toolbar)

## PAGES D'ERREUR

- si nécessaire :
```
composer require symfony/twig-pack
```
- 1xx : information
- 2xx : succès
- 3xx : redirection
- 4xx : client web
    - 401 : accès refusé
    - 403 : interdit
    - 404 : non trouvé
- 5xx : serveur
    - 500 : erreur interne
    - 503 : service indisponible

- créer l'arborescence templates/bundles/TwigBundle/Exception
- y créer les fichiers d'erreur avec l'écriture error404.html.twig
- error.html.twig pour les autres erreurs
- tester l'erreur 404 : taper une url non existante
- tester l'erreur 500 : {% {% extends 'base.html.twig' %} %} dans error404.html.twig puis taper une ur non existante

## TRADUCTIONS

- si nécessaire :
```
composer require symfony/translation
```
- config/packages/translation.yaml :
```
default_locale: fr
translator:
    ...
    fallbacks:
        - fr
```
- config/services.yaml :
```
parameters:
    ...
    app.langues: 'fr, en, de, nl'
```
- config/packages/twig.yaml :
```
globals:
    ...
    langues: [fr, en, de, nl]
```
- templates :
```
{% trans %} {% endtrans %}
```
- LangueController.php : enregistrer la langue en session
- LocaleSubscriber.php : enregistrer la langue en session
- base.html.twig : liens
- voir les messages à traduire :
```
php bin/console translation:update --dump-messages fr
```
- générer les fichiers de traduction :
```
php bin/console translation:update --force fr
```
