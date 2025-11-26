Boutique Diayma

Application ASP.NET Core permettant de consulter des produits, gérer un panier et passer commande.

# Quels sont les projets de la solution ?
 La solution a un seul projet qui est Diayma.csproj qui se trouve dans le repertoire P2FixAnAppDotNetCode  

# Quelle est la version SDK .NET utilis�e par ces projets ?
 Le projet utilise le SDK .NET Core 2.0 

# Installez le SDK

Pour cloner, construire et exécuter le projet Boutique Diayma, il faut installés sur votre machine le SDK:
    -Premierement: le projet utilise le framework netcoreapp2.0. Vous devez installer le SDK .NET Core de la série 2.x. c'est a dire 2.0 ou le 2.1
    -Deuxiement: Assurons-nous que la version affichée est dans la plage 2.x, en tapant la commande dotnet --version 
    Et dans notre cas on la version du sdk 2.1.818


# Explorez l’application. Signalez 2 bugs trouvés ?

1. Calcul incorrect du montant total du panier
Fichier : P2FixAnAppDotNetCode/Models/Cart.cs
Méthode : GetTotalValue()

La méthode calcule la somme des prix unitaires des produits présents dans le panier, sans tenir compte de la quantité (Quantity) de chaque produit. Si un utilisateur ajoute 2 fois le même produit, le total ne comptera qu'une seule fois le prix.

2. Réinitialisation de l'inventaire à chaque requête
Fichier : P2FixAnAppDotNetCode/Models/Repositories/ProductRepository.cs
Méthode : ProductRepository (Constructeur)

La liste _products est déclarée comme static, ce qui indique une intention de persister les données en mémoire partagée entre les requêtes. Cependant, le constructeur initialise cette liste (new List<Product>()) et recharge les données (GenerateProductData()) à chaque instanciation.
Comme ProductRepository est enregistré en tant que service Transient dans Startup.cs (services.AddTransient<IProductRepository, ProductRepository>();), une nouvelle instance est créée à chaque requête HTTP. Par conséquent, l'inventaire est remis à zéro à chaque action de l'utilisateur, et les décrémentations de stock (lors d'une commande) sont perdues immédiatement.


# 7. Placez un point d’arrêt sur les lignes suivantes du code :
# a. CartSummaryViewComponent ligne 12

1. Pas à pas détaillé 
Exécute la ligne en cours et entre dans la méthode appelée, s’il y en a une.
Dans ce cas précis, la ligne 13 (_cart = cart as Cart;) est une simple affectation qui ne contient aucun appel de méthode. Se comportant ainsi comme Pas à pas principal et passe simplement à la ligne suivante.

2. Pas à pas principal
Exécute la ligne en cours mais sans entrer dans les éventuelles méthodes appelées. Ici, elle permet d’avancer dans le constructeur ligne par ligne : accolade ouvrante {, affectation, puis accolade fermante }. Elle est particulièrement utile pour contrôler la valeur de _cart après l’exécution de l’affectation.

3. Pas à pas sortant
Exécute automatiquement tout le reste de la méthode actuelle, puis suspend l’exécution dès que le programme revient à la méthode appelante.
Dans ce contexte, elle permet de quitter rapidement le constructeur et de revenir au code du conteneur d’injection de dépendances qui instancie CartSummaryViewComponent.

# b. ProductController ligne 15
1. Pas à pas détaillé
Est utilisé pour entrer immédiatement dans le bloc du constructeur et s'arrêter sur la première ligne de code, ce qui permet de vérifier instantanément les valeurs des paramètres injectés (productService, languageService) dans la fenêtre "Locales" avant leur utilisation.

2. Pas à pas principal
Exécute la ligne en cours et passe à la suivante.
Dans ce contexte, elle permet d’avancer sur les lignes d’affectation du constructeur (_productService = ... et _languageService = ...) et de vérifier que les dépendances injectées sont correctement assignées aux champs privés de la classe.

3. Pas à pas sortant
Exécute le reste du constructeur puis en sort.
Elle est utile une fois les dépendances contrôlées, afin de laisser ASP.NET Core reprendre le flux normal d’exécution, notamment pour appeler ensuite la méthode Index().

# c. OrderController ligne 17
1. Pas à pas détaillé
Ca permet de faire entrer le débogueur dans le corps du constructeur, c’est-à-dire depuis la signature de la méthode jusqu’à l’accolade ouvrante.
Cela permet de commencer l’analyse du processus d’initialisation et de préparer l’affectation des champs privés de la classe.

2. Pas à pas principal
Ceci permet d'exécuter la ligne en cours et passe simplement à la suivante.
Elle permet ici d’avancer sur les lignes d’affectation du constructeur (_cart = pCart;, _orderService = service;, etc.). C’est le moment idéal pour vérifier, en survolant les variables, que pCart et service contiennent bien des instances valides et ne sont pas null.

3. Pas à pas sortant
Est utilisé, après avoir vérifié que les services sont correctement connectés aux champs privés, pour quitter le reste du constructeur instantanément et laisser le framework poursuivre son travail (en appelant par exemple la méthode d'action Index()).

# d. CartController ligne 15
1. Pas à pas détaillé
Fait entrer le débogueur dans le corps du constructeur, juste après la signature de la méthode.
Elle permet de commencer l’exécution du code d’initialisation du contrôleur.

2. Pas à pas principal
Cette commande exécute la ligne courante puis passe directement à la suivante.
Elle permet ici d’exécuter les lignes d’initialisation (_cart = pCart;__productService = productService;). Il y a pCart et productServicecontinents

3. Pas à pas sortant
Permet l’exécution du constructeur puis en sort immédiatement.
Une fois l’initialisation confirmée, elle permet de revenir au framework, qui poursuivra l’exécution en appelant l’action demandée par l’utilisateur (par exemple AddToCartouIndex).

# e. Startup ligne 20
1. Pas à pas détaillé
Le débogueur tente d’entrer dans le code de la propriété Configuration.
Comme Configuration est une propriété automatique ({ get; }) sans code d’accès, il n’y a rien à parcourir : Pas à pas détaillé se comportera donc comme le deboggage Pas à pas principal et passera à l’instruction suivante.

2. Pas à pas principal
Exécute la ligne courante puis passe à la suivante.
Ici, il exécute l’affectation de Configuration et avance jusqu’à l’accolade fermante du constructeur. Avant d’appuyer, survolez la variable configuration pour examiner son contenu et vérifier que les paramètres de appsettings.json ont bien été chargés.

3. Pas à pas sortant
Exécute le reste du constructeur puis en sort immédiatement.
Cette commande permet de quitter Startup.cs et de revenir à l’appelant (généralement Program.cs ou au code interne d’ASP.NET Core), confirmant que la phase d’initialisation est terminée.

# 5. Quels sont les namespaces, classes et méthodes visités avant l’affichage des produits sur l’écran d’accueil de votre navigateur ? Choisissez le mode approprié selon le contexte, "Pas à pas détaillé", "Pas à pas principal" ou "Pas à pas sortant". Vos réponses doivent être détaillées dans le fichier README du dépôt.

Etape 1: Initialisation (Démarrage de l’application)
Cette étape concerne uniquement le lancement initial de l’application.
Namespace : P2FixAnAppDotNetCode
Classe : Program
Méthode : Main → BuildWebHost
Namespace : P2FixAnAppDotNetCode
Classe : Startup
Méthodes : ConfigureServices (injection des dépendances) et Configure (pipeline HTTP)
Mode de débogage : Pas à pas principal
Permet de survoler la configuration initiale sans entrer dans le framework.

Étape 2: Réception de la requête
Namespace: P2FixAnAppDotNetCode.Controllers
Classe: ProductController
Méthode: Constructeur
Contexte: Le serveur reçoit la requête et instancie le contrôleur en injectant les services.
Mode choisi:Pas à pas principal
On veut simplement vérifier que le constructeur s'exécute et que les dépendances sont assignées, sans entrer dans les détails du framework .NET.

Étape 2 : Appel de l'action
Namespace : P2FixAnAppDotNetCode.Controllers
Classe : ProductController
Méthode : Index
Ligne : _productService.GetAllProducts()
Contexte : Le contrôleur demande la liste des produits au service.
Mode choisi : Pas à pas détaillé
C’est une méthode de notre code métier. Nous devons entrer dans la méthode pour vérifier la récupération des produits.

Étape 3 : Logique métier
Namespace : P2FixAnAppDotNetCode.Models.Services
Classe : ProductService
Méthode : GetAllProducts
Ligne : _productRepository.GetAllProducts()
Contexte : Le service fait le lien entre le contrôleur et le stockage de données.
Mode choisi : Pas à pas détaillé
On veut descendre au niveau du repository pour vérifier l’origine des données.

Étape 4 : Accès aux données
Namespace : P2FixAnAppDotNetCode.Models.Repositories
Classe : ProductRepository
Méthode : GetAllProducts
Contexte : Récupération directe des données depuis la liste en mémoire.
Mode choisi : Pas à pas sortant
La méthode retourne simplement la liste. Pas besoin de parcourir ligne par ligne, on souhaite revenir immédiatement au ProductService avec le résultat.

Étape 5: Retour au Contrôleur et rendu
Namespace: P2FixAnAppDotNetCode.Controllers
Classe: ProductController
Méthode: Index (fin)
Ligne: return View(products);
Contexte: Les données sont récupérées, le contrôleur génère la vue HTML.
Mode choisi: Pas à pas principal (F10)
La logique C# est terminée. On utilise F10 pour survoler la ligne et confirmer que la vue est correctement retournée, sans entrer dans le moteur Razor ou les ViewComponents déjà vérifiés séparément.


# Déployez votre solution sous forme d’exécutable Windows. Ci-dessous le lien de l'executable
https://drive.google.com/drive/folders/1gY1wIJDnuKc1DZZIy4IERVx_UX9529Fl?usp=sharing