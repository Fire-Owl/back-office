# Projet Portfolio - *Back-Office* et Interface Visiteur

## Contexte

Dans le cadre du développement d'un Portfolio de professionnel du Web, le besoin porte sur la conception et le développement d'une interface publique « visiteur » permettant de consulter la fiche descriptive des projets publiés, et d'un *back-office* privé permettant d'administrer la publication de projets. Ce portfolio a vocation à permettre de trouver un stage puis un emploi dans le secteur numérique.

## L'interface visiteur du portfolio

Un portfolio requiert un travail de conception absolument original, il exprime la personnalité de son auteur autant qu'il met en valeur ses compétences. Il n'y a donc pas de limites à l'imagination dans la conception de l'interface visiteur, toutefois ATTENTION ! Un certain nombre d'éléments sont attendus :

- le portfolio peut simplement se structurer autour d'un *template* de *landing page* et d'un *template* de *single page* car il doit *a minima* lister des projets et permettre d'accéder à la fiche individuelle de présentation de chaque projet, toutefois il peut aussi se composer de plusieurs autres pages originales, la page listant les projets devient alors une page « projets » ou « réalisations ». 
    - la page listant les projets doit se composer :
        - de *cards* (ce terme ne doit pas être compris en un sens restreint, l'originalité est totalement permise) résumant le projet (avec une illustration) ;
        - d'un système de filtres pour trier les projets en fonction de critères, par exemple le technologies utilisées ;
    - la page présentant individuellement chaque projet doit présenter :
        - le titre du projet, 
        - un « *thumbnail* » du projet (l'image mise-en-avant, qui illustre la *card* sur la page précédente),
        - les technologies utilisées pour réaliser ce projet,
        - la période de réalisation,
        - le contexte,
        - le cahier des charges : 
            - les spécifications fonctionnelles,
            - les spécifications techniques, 
        - le lien de téléchargement du dossier de conception ;
        - le lien vers la page *repository* sur GitHub ;
        - le lien vers le projet hébergé sur un serveur ;
        - une galerie de *screenshots* relatifs au projet ;

- le portfolio doit contenir une section ou même une page « *about* » qui permet : 
    - de présenter le profil de l'auteur et son projet professionnel sous forme d'un paragraphe rédigé ;
    - de télécharger le CV de son auteur au format PDF ;
    - d'accéder à son profil sur des sites externes (GitHub et LinkedIn en priorité + une plateforme pour partager des créations + toutes autres plateformes pertinentes témoignant d'une activité sur le wev) ;
    - de présenter d'autres informations :
        - formations et expériences, par exemple sous forme de *timeline* ;
        - les technologies manipulées ;

- enfin, le portfolio permet de prendre contact avec son auteur via un formulaire et d'autres informations de contact ;

- bonus : il peut aussi contenir une page « articles » ou « blog » où les articles originaux rédigées sur des sujets en rapport avec la conception et le développement web sont listés et consultables ;

- le portfolio peut contenir d'autres informations mais attention, il doit éviter de multiplier les informations sur des sujets sans rapport avec le métier visé.

## Le *back-office* du portfolio

Le *back-office* permet de créer les projets consultables via l'interface visiteur. 

L'accès au *back-office* est conditionnée par une authentification, il se compose donc tout d'abord d'un formulaire de connexion et de son corollaire : un formulaire d'inscription. Comme tout le monde n'a pas vocation à accéder au *back-office*, on peut rendre le formulaire d'inscription inaccessible pour les visiteurs, par exemple en ne faisant figurer nulle part un lien permettant d'y accéder. On peut alors intégrer le formulaire d'inscription au *back-office* : seul un administrateur peut créer de nouveaux utilisateurs.

On peut aussi imaginer un système de rôles : tout nouvel utilisateur créé est par défaut « invité » (il peut consulter mais ne peut rien éditer), et les administrateurs peuvent faire un « *upgrade* » du rôle de l'utilisateur pour qu'il devienne contributeur ou administrateur.
### Conception de la base de données 
#### Structure de la base de données

Cette base de données se compose de 6 tables. Les tables « utilisateur » et « projet » ne sont pas forcément liées, tout utilisateur qui a le rôle d'administrateur peut utiliser les fonctions d'édition du back-office. 

Par contre, si il y a bien un champs « image mise en avant » pour les projets, il est possible de constituer une galerie de captures d'écran du projet via le *back-office*, ce qui implique de lier la table « projet » à la table « capture d'écran » via une clé étrangère. 

On monte encore d'un niveau avec les étiquettes : il faut bien sûr une table dédiée aux étiquettes, mais comme un projet peut avoir plusieurs étiquettes, qu'une étiquette peut être attribuée à de nombreux projets... il va falloir une table intermédiaire.

- la table « users » :
    - id (pk)
    - username
    - email
    - password
    - role (visitor / contributor / administrator)

- la table « projects » :
    - id (pk)
    - title
    - begining_date
    - ending_date
    - context
    - technical_specifications
    - functional_specifications
    - thumbnail
    - coworkers
    - technologies
    - project_link
    - github_link
    - design_folder_link
    - visibility (hide / visible)

- la table « screenshots »
    - id (pk)
    - project_id (sk)
    - screenshot_name
    - screenshot_alt_text

- la table « tags »
    - id (pk)
    - tag_name (sk)

- la table « projects_tags »
    - id (pk)
    - project_id (sk)
    - tag_id (sk)


#### Modélisation de la base de données

Pour qu'un dossier de conception soit complet, il faut formaliser le Modèle Conceptuel de Données (puis le Modèle Logique de Données et le Modèle Physique de Données). [MySQL Workbench](https://www.mysql.com/fr/products/workbench/) est préconisé pour modéliser la base de données.



### Développement de l'interface du *back-office*

#### La page de connexion

Il s'agît de la page « index.php ». Elle se compose d'un formulaire de connexion permettant de renseigner un nom d'utilisateur et un mot de passe. Le script vérifie si ce nom d'utilisateur et ce mot de passe sont enregistrés dans la base de données, si c'est le cas, on accède à la page « home.php ». Dans le cas contraire, on reste sur cette page, qui propose aussi un lien vers la page « register.php » permettant de soumettre une demande d'inscription, et un lien de récupération du mot de passe.

#### La page d'inscription d'un nouvel utilisateur

Il s'agît de la page « register.php ». Elle permet de soumettre une demande d'inscription via un formulaire. Le visiteur doit renseigner un nom d'utilisateur, un email et un mot de passe, qu'il doit re-taper par sécurité dans un second champs de type *password* (bonus : gérer en JavaScript l'impossibilité de coller du texte dans ce champs). 

Lorsqu'un utilisateur soumet une inscription, le programme vérifie la conformité des deux mots de passe saisie puis crypte le mot de passe avant de l'insérer dans la base de données.

Par défaut, le rôle attribué sera celui de visiteur : l'utilisateur enregistré pourra voir la liste des projets (uniquement ceux qui ont le statut visible), mais ne pourra utiliser aucune des fonctions d'édition, de mise-à-jour ou de suppression du *back-office*. 

L'inscription envoie automatiquement un mail à l'administrateur du *back-office* l'informant de l'existence du nouvel utilisateur, l'administrateur pourra alors paramétrer les droits de cet utilisateur.

#### La page de récupération du mot de passe

Il s'agît de la page « recovery.php » : un simple formulaire permettant à l'utilisateur de renseigner son adresse mail pour recevoir un nouveau mot de passe. Elle vérifie si l'adresse mail renseignée existe, si c'est le cas, elle génère un nouveau mot de passe et l'envoie à cette adresse mail. Sinon, un message d'erreur s'affiche. 

#### La page listant les projets

Il s'agît de la page « home.php ». Cette page se connecte à la base de données et affiche le titre de tous les projets contenus dans la table « projects », ainsi que leurs technologies (enregistrées sous formes de *tags* dans la base de données). 

En cliquant sur un titre, on accède à la page « details.php ».

Un système de filtre permet de n'afficher que les projets ayant eu recours à une technologie en particulier (bonus :  un bouton permet d'ajouter un second, puis un troisième champs filtres).

Un bouton « ajouter un projet » permet d'accéder à la page « add.php ». 

Depuis cette page, on peut aussi modifier ou supprimer un projet existant et changer sa visibilité, ou encore se déconnecter du *back-office*.

Pour prévenir d'un *missclick*, la suppression doit se faire en deux étapes : au clic sur le bouton, une fenêtre modale demandant la confirmation doit apparaître. (Bonus : via cette page qui liste les articles, on peux imaginer un système de suppression simultanée de plusieurs articles : un bouton « supprimer des articles » ferait apparaître des *checkbox* à côté de chaque titre, et la confirmation de la suppression supprimerait tous les articles sélectionnés par exemple).

Dernier élément de cette page, un lien vers la page « tags_manager.php », qui permet de créer des étiquettes qui seront attribuées aux projets. 

> La requête pour récupérer les informations affichées sur cette page sera la même que celle à utiliser sur la page listant les projets de l'interface visiteur du portfolio

#### La page d'administration des étiquettes

Il s'agît de la page « tags_manager.php ». Cette page liste les étiquettes existante et permet d'en créer de nouvelles (mais pas de suppression ou de modification de celles qui existent déjà).

#### La page de création d'un projet

Il s'agît de la page « add.php ». C'est un formulaire permettant de renseigner toutes les informations concernant un nouveau projet : son titre, sa période de réalisation, ses « étiquettes » (le technologies utilisées pour sa réalisation), la description du projet, sa visibilité par défaut (masquée ou affichée), son image mise-en-avant et les liens vers le projet en ligne et son github. Cette page permet aussi de charger des captures d'écran du site ou de l'application web réalisée, pour constituer une gallerie d'image. 

#### La page affichant un projet

Il s'agît de la page « details.php ». Elle affiche toutes les informations concernant un projet en fonction de son id. À côté de chaque information, une icône indique la possibilité de modifier l'information : via cette page, il est en effet possible de modifier une par une chaque information contenue dans la base de données concernant le projet (bonus : utiliser l'Ajax pour n'actualiser que la zone modifiée et ne pas avoir à recharger toute la page). 

La page permet aussi d'ajouter de nouvelles captures d'écran au projet (la galerie des *screenshots*).

Comme sur la page listant les projets, la fonction supprimer est présente. Attention, ici aussi, la suppression se fait en deux étapes : une modale demandant confirmation doit s'ouvrir pour prévenir toute mauvaise manipulation. 

> La requête pour récupérer les informations affichées sur cette page sera la même que celle à utiliser sur la page d'un projet individuel de l'interface visiteur du portfolio


#### La page listant les utilisateurs du *back-office*

Cette page permet d'ajouter ou de supprimer des utilisateurs, et de modifier leur rôle.
