# request_django

## Plan d'Action

- Configuration d'un nouveau projet Django
- Introduction à la bibliothèque requests de Python
- Utilisation de l'API OpenWeatherMap
- Affichage des données météorologiques
- Introduction au Webscraping
- Utilisation de Beautiful Soup
- Exploration du contenu extrait
- Affichage des actualités

### Configuration d'un nouveau projet Django

Commençons par créer une nouvelle application Django pour ce projet. Depuis le terminal, nous pouvons le faire en utilisant l'une des commandes suivantes :


`django-admin startproject homepage`

ou

`python3 -m django startproject homepage`

Cela créera notre projet intitulé 'homepage'. Ensuite, accédez au dossier homepage et créez notre application main_app :

bash

`python manage.py startapp main_app`

Maintenant, lançons notre serveur et accédons à localhost:8000 pour nous assurer que tout fonctionne !
###2. Introduction à la bibliothèque requests de Python

Python est livré avec une bibliothèque fantastique appelée 'requests' que nous pouvons utiliser pour envoyer des requêtes ! Démarrons un shell Python avec python3 manage.py shell pour voir ce qu'elle fait !

python

### Tout d'abord, importons la bibliothèque
`import requests`
### Commençons par quelque chose de simple
`requests.get('https://spacejam.com/1996')`
> <Response [200]>

Une autre façon d'obtenir le code de réponse est d'ajouter .status_code à la fin de notre requête get. Mais que faire si nous voulons récupérer des données ? Utilisons l'API de test préférée de tout le monde, JSON Placeholder. Nous allons accéder à l'endpoint pour recevoir le premier Todo.

`requests.get('https://jsonplaceholder.typicode.com/todos/1').status_code`
> 200
### Examinons de plus près les données de la réponse
`requests.get('https://jsonplaceholder.typicode.com/todos/1').text`
> '{\n  "userId": 1,\n  "id": 1,\n  "title": "delectus aut autem",\n  "completed": false\n}'
### Ça n'a pas l'air tout à fait correct. Ce serait bien d'obtenir du JSON, retourné plutôt qu'une chaîne de caractères
`requests.get('https://jsonplaceholder.typicode.com/todos/1').json()`
> {'userId': 1, 'id': 1, 'title': 'delectus aut autem', 'completed': False}

Magnifique ! Nous pouvons maintenant accéder à une API, et avoir nos données retournées dans un format agréable et facile à utiliser ! Nous pouvons même continuer à enchainer cette requête (comme le permet Python) et essayer d'obtenir des valeurs plus spécifiques !
Exercices sur la librairie requests de Django

### Exercice 1

À l'aide de la librairie requests, créez une vue Django qui envoie une requête GET à une API publique de votre choix (par exemple, l'API JSON Placeholder) et affiche le code de statut de la réponse dans le navigateur.
Modifiez la vue précédente pour récupérer également les données JSON de la réponse et affichez-les dans le navigateur.
Créez une nouvelle vue Django qui envoie une requête GET à une API tierce de votre choix (par exemple, OpenWeatherMap) et affiche les données de réponse dans le navigateur.

### 3. Introduction au Webscraping

Le Webscraping est quelque chose qui est devenu assez populaire dans la communauté des développeurs. Permettez-moi de vous décrire une image :

Vous construisez une application qui a besoin de certaines données d'un site Web préexistant. Bien sûr, en tant que développeur compétent, vous vérifiez s'ils ont une API que votre application peut consommer. Vous êtes choqué de constater qu'ils n'en ont pas ! Comment allez-vous maintenant obtenir ces précieuses données dans votre application ?

Voici le Webscraping, une manière de, comme son nom l'indique, fouiller un site Web et extraire uniquement les informations que nous voulons. En y pensant, revenons à une requête précédente que nous avons effectuée et regardons les données qui sont renvoyées.

`requests.get('https://spacejam.com/1996').text`

> '<html>\r\n\r\n\r\n\r\n\r\n\r\n\r\n\r\n<!-- Copyright 1996 Warner Bros. Online -->\r\n...'

Regardez ça ! Tout de suite, nous pouvons dire que nous regardons le document HTML compilé pour spacejam.com ! Ça n'a pas l'air très bien, n'est-ce pas. Et la seule façon dont nous avons accès est dans une seule chaîne de caractères géante. Comme disent tous les infopublicités, "Il doit y avoir une meilleure façon !". Entrez...

### 4. Beautiful Soup

Beautiful Soup est l'outil privilégié pour le Webscraping avec Python. En fait, c'est l'outil privilégié pour le Webscraping en général. C'est une bibliothèque très puissante qui nous permet de rechercher, filtrer, et même parcourir l'HTML renvoyé depuis requests comme si nous parcourions un arbre DOM. Pour commencer, nous devons simplement l'installer avec pip3 install beautifulsoup4 (Oui, nous voulons la version 4. Si vous installez simplement BeautifulSoup, c'est une version plus ancienne). Nous allons également installer html5lib comme notre parseur HTML du jour.

### 5. Examiner le contenu extrait

Revenons maintenant et jetons un coup d'œil à cette page Space Jam. Notre objectif est de récupérer la mention de droits d'auteur de la page et de la faire apparaître dans la console.

<Captures d'écran, j'en suis sûr>

Si nous examinons l'HTML du site, nous pouvons voir que tout le contenu est à l'intérieur de ces balises <center>, et la toute dernière balise <font> contient notre mention de droits d'auteur. Utilisons donc Beautiful Soup pour y arriver :

> d'abord nous devons importer BeautifulSoup

`from bs4 import BeautifulSoup`
> sauvegardons maintenant la réponse de notre requête

`page = requests.get('https://spacejam.com/1996')`
> maintenant la magie

`soup = BeautifulSoup(page.content, 'html5lib')`
> maintenant si nous examinons le code html, nous verrons notre html plus clairement

`soup.find_all('font')`
> [<font class="footer-links" size="-1"><a href="https://policies.warnerbros.com/...
> Ça a l'air familier ! Recherchez le dernier seulement.

`soup.find_all('font')[-1]`
> <font size="-1">SPACE JAM, characters, names, and all related<br/>indicia...

Comme vous pouvez le voir, Beautiful Soup nous offre des méthodes que nous pouvons utiliser pour rechercher dans l'HTML. Il existe de nombreuses méthodes très utiles, telles que find_all() que nous avons utilisée ci-dessus pour renvoyer une liste de résultats, find() qui renverra le premier correspondant, find_next(), find_parent(), la liste continue. Lorsque nous appelons ces méthodes, le premier argument que nous passons est le nom de la balise HTML de l'élément que nous recherchons. Donc dans notre cas, nous cherchions toutes les balises <font> correspondantes (assez obsolètes, oui). Comme find_all() est la méthode la plus populaire, Beautiful Soup nous a donné un raccourci pour y accéder.

`soup.find_all('font')`

> est le même que

`soup('font')`

En plus du nom de la balise, nous pouvons également passer une classe, un identifiant, ou même des attributs pour obtenir des résultats plus spécifiques :

`soup.find_all('div', class_="nav")`
> renvoie tous les "div" avec la classe 'nav'

`soup.find_all('h1', id="title-124")`
> renvoie tous les h1 avec l'identifiant "title-124"
> si vous voulez rechercher par sélecteurs CSS, utile pour sélectionner plusieurs classes

`css_soup.select("p.content.aside#1")`
> renvoie <p class="aside content" id="1">

Bien sûr, extraire toute la balise ne sera pas très utile lorsque nous recherchons simplement le texte à l'intérieur. Dans notre exemple précédent, nous avons les balises <font> d'encadrement, et cette balise <br/> embêtante en plein milieu. Malheureusement, cette balise <br/> posera un petit problème, mais simple à résoudre. Nous vérifions à la fois le contenu et les enfants d'une balise en appelant .contents dessus. Revenons donc à Space Jam :

`soup('font')[-1]`

> cela nous donne la dernière balise <font> sur la page avec notre syntaxe abrégée

`soup('font')[-1].contents`

>['SPACE JAM, characters, names, and all related', <br/>, 'indicia...'
> Faisons un peu de mise en forme pour l'afficher joliment

`print(f"{soup('font')[-1].contents[0]} {soup('font')[-1].contents[2]}")`

> SPACE JAM, characters, names, and all related indicia are trademarks of Warner Bros. © 1996

Maintenant que nous sommes parfaitement à l'aise avec l'extraction de contenu à partir de n'importe quel site Web, passons à l'action. Aujourd'hui, nous allons faire du scraping sur le site d'actualités pour les développeurs dev.to.

### 6. Exercice 2

> Partie 1

    Utilisez Beautiful Soup pour extraire le titre et le lien de trois articles différents sur la page d'accueil de dev.to. Affichez ces informations dans la console de votre application Django.
> Partie 2

    Écrivez une fonction Django qui utilise Beautiful Soup pour extraire le contenu textuel de tous les paragraphes (<p>) d'un article spécifique sur dev.to. Testez cette fonction en passant l'URL d'un article dev.to comme argument.
> Partie 3

    Créez une vue Django qui, lorsqu'elle est appelée, extrait les cinq derniers articles publiés sur dev.to, affiche le titre et le lien de chacun dans une page HTML.

Conclusion

Dans cette présentation, nous avons exploré comment consommer des API tierces et réaliser du Webscraping avec Django. En utilisant la bibliothèque requests de Python, nous avons appris à envoyer des requêtes HTTP, à recevoir des réponses et à manipuler les données renvoyées. Ensuite, en utilisant Beautiful Soup, nous avons découvert comment extraire des données spécifiques à partir de pages Web. Ces compétences sont extrêmement précieuses pour enrichir vos applications Django avec des données provenant de sources externes et pour automatiser la collecte d'informations à partir du Web. Amusez-vous à explorer davantage ces concepts et à les intégrer dans vos projets !
