# ViewPay HTML5: Guide de démarrage
Ce guide a pour objectif de vous guider dans la mise en place du widget JS ViewPay dans votre site web.

Pour rappel, Viewpay est une solution de micro-paiement par l'attention publicitaire, qui permet à l'utilisateur de débloquer un contenu premium en regardant une publicité. Viewpay a pour vocation d'être une alternative à d'autres options pour débloquer un contenu premium, et ne doit pas être installé comme seule option d'un paywall.

Voici un exemple de déblocage d'article avec Viewpay : 

![sample](https://github.com/TechViewpay/ViewPay-iOS/blob/master/DocImages/parcours_vp_mobile3.png?raw=true)

## Chargement du Javascript
```javascript
<script type="text/javascript" src="//cdn.jokerly.com/scripts/jkFbASQ.js"></script> 
```
Le fichier jkFbASQ.js est le seul fichier qui doit être chargé dans la page qui va accueillir Viewpay (page affichant le paywall). Ce fichier est servi par notre CDN, pour vous garantir d'avoir toujours la dernière version.

NB: Il faut placer le script le plus haut possible dans la page afin d’optimiser son temps de chargement.


## Création d’un div pour accueillir la publicité
Ajoutez un div avec l’id "cadreJokerlyADS" dans votre page. 
```html
<div id="VPmodal">
	<div id="cadreJokerlyADS"></div>
</div>
```

Le design CSS est généré automatiquement lors de l’initialisation de Viewpay, ainsi vous n’êtes pas obligés de rajouter du CSS. Cependant, si vous devez changer le design, votre CSS sera pris en priorité. Pour ceci, donnez à ce div les dimensions que vous souhaitez attribuer à Viewpay, en veillant à conserver un ratio largeur/hauteur de 1,44.
Nous imposons 650x450 pour du desktop et de le centrer horizontalement et verticalement.
Pour votre site mobile, on utilise tout l’espace vertical disponible en ouvrant l’iframe en 100% width et height, sauf si vous spécifiez la taille maximale que vous souhaitez nous offrir.
Voici donc le CSS à mettre dans votre page :

```css
#cadreJokerlyADS{
	margin: auto;
	top: 0;
	right: 0;
	left: 0;
	position: fixed;
	bottom: 0;
	width: 650px !important;
	height: 450px !important;
	z-index:9999999 !important;
}
@media screen and (max-width: 600px){
	#cadreJokerlyADS{
	width:100% !important;
	height:100% !important;
	margin-top:0;
	}
}
@media screen and ((min-width: 601px) and (max-width: 1024px)){
	#cadreJokerlyADS{
	width:100% !important;
	height:100% !important;
	margin-top:0;
	margin-left:0;
	}
}
#VPmodal{
    width: 100%;
    height: 100%;
    display: none;
    position: fixed;
    background-color: rgba(0, 0, 0, 0.9);
    z-index: 9999999 !important;
}
//Le z-index est nécessaire à la visibilité de la publicité. 
//Il est impératif de le monter si nécessaire si une frame/bannière/autre est au dessus du nôtre.
//Ceci est obligatoire afin de garantir de CPM élevé.
```

Si vous souhaitez modifier l'une de ces données, n'hésitez pas à contacter votre contact ViewPay

## Définition des fonctions et callbacks JavaScript

Les fonctions et callbacks fournis par Viewpay sont les suivants : 

Fonctions : 
- VPinitVideo() - appelée typiquement au onload d’une page pour vérifier la disponibilité d’une publicité. Cette fonction appelle le callback VPexistAds() si une ou plusieurs vidéos ont été trouvées, et appelle le callback VPnoAds() si aucune n’a été trouvée pour cet utilisateur.
- VPloadAds() - appelée une fois que vous avez eu la confirmation qu’une pub existe (via le callback VPexistAds()). Permet d’ouvrir le système Viewpay.

Callbacks :
- VPexistAds() - callback appelé si Viewpay a au moins une publicité à servir. Cette fonction vous permet d’afficher le bouton pour lancer Viewpay si vous recevez un OK. Si KO le callback VPnoAds() est appelé.
- VPnoAds() - callback appelé si Viewpay n’a aucune publicité à servir.
- VPplayAds() - callback de notification appelé lorsque l’utilisateur démarre la vidéo.
- VPcompleteAds() - appelé quand l’utilisateur a fini le parcours.
- VPcloseAds() - appelé si l’utilisateur ferme le parcours.

ViewPay est une solution qui répond aux appels reçus avec agilité et rapidité, il est donc avantageux pour vous de situer chacun des appels au début du chargement de la page Html.

Voici le code à intégrer dans une balise script : 
```javascript

<script> 
	function VPinitVideo(){
		JKFBASQ.init({
			site_id: 'b23d3f0235ae89e4', // id de démo fonctionnel
			load_callback : VPexistAds,
			noads_callback : VPnoAds,
			complete_callback : VPcompleteAds,
			close_callback : VPcloseAds,
			play_callback : VPplayAds,
			cover:false,
		});
	}
		
	function VPexistAds(){
		console.log("existAds");
		document.getElementById("btnShowViewpay").style.display = "block";
	}
	function VPloadAds(){
		console.log("loadAds");
		document.getElementById("modal").style.display = "block";
		JKFBASQ.loadAds();
	}
	function VPnoAds(){
		document.getElementById("btnShowViewpay").style.display = "none";
		console.log("noAds");
	}
	function VPcompleteAds(){
		console.log("completeAds");
		document.getElementById("modal").style.display = "none";
		/*Une fois la pub finie, le code permettant de débloquer l’article doit se situer ici*/
	}
	function VPcloseAds(){
		console.log("closeAds");
		document.getElementById("modal").style.display = "none";
	}
	function VPplayAds(){
		console.log("playAds");
	}
</script>
 ```

Pour débloquer l’article, insérez dans le VPcompleteAds(), votre fonction/code permettant de débloquer l’article.

NB: La fonction VPinitVideo(), que vous avez configurée avec votre identifiant Viewpay (site_id), doit être appelée en amont de l’apparition du système Viewpay, afin de permettre à notre système d’aller interroger le catalogue de publicités pour trouver le contenu adapté. 

Exemple : 
```html
<body onload="VPinitVideo()">
...
</body>
```
Ou plus simplement dans une balise ```html <script>```

## Bouton d’appel à ViewPay
Voici un exemple de bouton d’appel à ViewPay que nous vous proposons :

Presse Bleu arrondi « Je » : http://cdn.jokerly.com/images/logosVP/Bouton_arrondi_bleu_presse_je.png
![sample](https://cdn.jokerly.com/images/logosVP/Bouton_arrondi_bleu_presse_je.png)

Nous conseillons d’adapter le wording au mieux par rapport au wording de l’autre alternative proposée en face de ViewPay dans votre paywall. 
Ainsi, si l’autre alternative est “je m’inscris” alors notre bouton devra également utiliser le “je” : “J’accède à cet article”...
N’hésitez pas à nous contacter pour adapter notre bouton à vos spécificités.

Voici l'url du picto ViewPay que nous devez intégrer à votre bouton : 
Blanc : 
![sample](https://cdn.jokerly.com/img/minilogo.png)
Noir : 
![sample](https://cdn.jokerly.com/img/minilogo_noir.png)

Vous pouvez positionner ce picto avec du css comme ceci :
```css
background: url(https://cdn.jokerly.com/img/minilogo.png) 10px center no-repeat;
```

### Optimisation du chargement du bouton ViewPay
Il est primordial d’optimiser la vitesse d’affichage du bouton Viewpay, pour maximiser le taux de clic sur le bouton et donc les revenus générés. Il faut donc s’assurer d’appeler VpInit() le plus tôt possible, pour que la disponibilité de la pub soit déjà connue au moment d’afficher votre paywall.

Malgré cela, comme le chargement des pages fait souvent appel à une multitudes de process (pubs, contenus, analytics…), il peut arriver que la réponse arrive après l’affichage du paywall : le risque est alors que l’utilisateur quitte la page avant même que le bouton soit affiché…

Pour éviter ce phénomène, nous vous recommandons d'afficher un bouton temporaire non cliquable, qui place la proposition Viewpay dans le paywall et qui informe que le process de recherche de pub est en cours. Ce bouton est remplacé par le bouton cliquable dès que la réponse est obtenue.

La logique est la suivante :
- Affichage du bouton ViewPay grisé et non cliquable, avec un wording du type "Recherche de publicités...".
- On vérifie s'il y a des publicités disponibles, 
	- Si Oui (VPexistAds())
		- Le bouton change de wording et devient cliquable
	- Si Non (VPnoaAds())
		- Le bouton disparaît ou le wording s'adapte pour expliquer qu'aucune publicité n'est disponible.

 Techniquement, il suffit de faire comme ceci :
 - Un élement button avec ```id="btnShowViewPay"``` avec le texte "Recherche de publicité". Eventuellement avec la propriété de style : ```cursor: not-allowed```
```html
<button type="button" id="btnShowViewPay" style="cursor:not-allowed">Recherche de publicit&eacute;</button>
```
Ensuite, il ne reste plus qu'à utiliser les callbacks pour appliquer le style et les propriétés JS adaptées : 
```javascript
function VPexistAds(){
	var btnVp = document.getElementById("btnShowViewPay");
	btnVp.style.backgroundColor = #1bbbec;
	btnVp.style.cursor = "pointer";
	btnVp.onclick = VPloadAds();
}
...
function VPnoAds(){
	var btnVP = document.getElementById("btnShowViewPay");
	btnVp.textContent = "Aucune publicité disponible.";
}
```
  
De cette façon, on évite tout lag entre l’affichage du paywall et celui du bouton ViewPay, ce qui permettra aux lecteurs de découvrir plus rapidement l’existence du bouton ViewPay dans le paywall.

NB: si vous remarquez que le texte de votre bouton en cas de publicité disponible n'est pas identique à celui que vous avez configuré, contactez nous. Il s'agit d'un texte par défaut qui s'applique, et nous le modifierons à votre convenance. 


## Informations de ciblage
Il est possible (et très souhaitable) de transmettre à ViewPay des informations qui nous permettront de mieux cibler les publicités pour chaque utilisateur. Si vous disposez d’informations non nominatives sur l’utilisateur, telles que son sexe et son âge, où encore la catégorie de l’article à débloquer (Economie, International, sport…), ces informations peuvent être renseignées dans la fonction Init().

Discutons ensemble du champ des possibles au moment de démarrer l’intégration!

```javascript
function VPinitVideo(){
	JKFBASQ.init({
		site_id: 'b23d3f0235ae89e4', // id de démo	
		load_callback : VPexistAds,
		noads_callback : VPnoAds,
		complete_callback : VPcompleteAds,
		close_callback : VPcloseAds,
		play_callback : VPplayAds,
		codeCategory:1
		userInfo : {
			age:35,
			gender:'M',
			country:'FR',
			language:'fr',
			postcode:75018,
		}
	});
}
```

Tous les champs ne sont pas obligatoires. Les valeurs par défaut de ces champs sont donc vides. 

| Paramètres |      Exemple      |  Description |
|----------|:-------------:|-------|
| codeCategory (int) | 1 | Représente la catégorie d’articles <br>que l’utilisateur souhaite débloquer:<br> 0 : News,<br> 1 : Sports,<br> 2 : Economy,<br> 3 : Politics,<br> 4 : Environment,<br> 5 : Science,<br> 6 : Technology,<br> 7 : Others |
| age (int) | 35 | Age de l'utilisateur |
| gender (char) | M | Sexe de l'utilisateur. Valeurs possibles 'M' ou 'F' |
| country (char) | FR | Code pays de l'utilisateur |
| language (char) | fr | Code langue de l'utilisateur |
| postcode (int) | 75001 | Code postal de l'utilisateur |


Nous avons la possibilité d’ajouter des codeCategory selon vos besoins.
Ainsi si vous ne trouvez pas les catégories qui vous correspondent, l’équipe de ViewPay est à votre disposition pour en discuter.


## Conseils d’optimisation d’un Paywall avec Viewpay
Grâce à notre expérience chez nos éditeurs, voici quelques conseils afin de maximiser votre taux de clics sur notre bouton:
Insérer le mot gratuit, ou gratuitement, dans le wording du bouton ou celui autour du bouton ViewPay peut faciliter la compréhension et l’utilisation de l’offre.

Présenter les offres alternatives (Abonnement, inscription, Viewpay…) de manière équivalente / équitable, et proposer un choix clair pour l’utilisateur, l’offre 1 ou bien l’offre 2. La mise en avant d’une séparation par un “OU BIEN” est très utile pour clarifier les choses.

La charte graphique du bouton ViewPay (longueur, bordure ....) peut être modifiée afin de mieux respecter la votre.
Présenter le paywall le plus en haut possible de la page pour éviter le plus possible la nécessité de scroller pour rendre le bouton VP visible. Positionner le bouton Viewpay en première option (verticalement parlant) est préférable, pour la même raison.
Chaque paywall est unique, alors discutons ensemble au moment de l’intégration pour optimiser au mieux le rendu!

Si vous utilisez Bootstrap, nous avons remarqué que les modales de Bootstrap sont par défaut affichées avec un délai de 0.15 secondes. Pour corriger ce problème dans un site Bootstrap, il suffit de lancer VPLoadAds dans un setTimeout d’au minimum 0.15s.
