# ViewPay HTML5: Guide de démarrage simplifié
Ce guide a pour objectif de vous guider dans la mise en place du widget JS ViewPay dans votre site web.

Pour rappel, Viewpay est une solution de micro-paiement par l'attention publicitaire, qui permet à l'utilisateur de débloquer un contenu premium en regardant une publicité. Viewpay a pour vocation d'être une alternative à d'autres options pour débloquer un contenu premium, et ne doit pas être installé comme seule option d'un paywall.

Voici un exemple de déblocage d'article avec Viewpay : 

![sample](https://github.com/TechViewpay/ViewPay-iOS/blob/master/DocImages/parcours_vp_mobile3.png?raw=true)

## Ajout du code Javascript
```javascript
<script>
const divVPmodal = document.createElement('div');
const divCadreJokerlyADS = document.createElement('div');
divVPmodal.setAttribute('id', 'VPmodal');
divCadreJokerlyADS.setAttribute('id', 'cadreJokerlyADS');
divVPmodal.appendChild(divCadreJokerlyADS);

const vpScript = document.createElement('script');
vpScript.src = 'https://cdn.jokerly.com/scripts/jkFbASQ.js';
vpScript.defer = true;
vpScript.addEventListener('load', VPinitVideo);
document.body.appendChild(divVPmodal);
document.body.appendChild(vpScript);

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
	//fonction qui sera appelée lorsqu'au moins une publicité a été trouvée. Vous pouvez afficher un bouton dans un paywall par exemple.
}
function VPnoAds(){
	//fonction qui sera appelée quand aucune publicité n'a été trouvée. Vous pouvez par exemple cacher le bouton, ou déclencher un autre process.
}
function VPloadAds(){
	//fonction que vous lancez pour ouvrir ViewPay. Par exemple onclick sur un bouton "Regarder une publicité"
document.getElementById("VPmodal").style.display = "block";
	JKFBASQ.loadAds();
}
function VPcompleteAds(){
//fonction appelée quand l'utilisateur aura complété un parcours ViewPay. Insérer ici le code nécessaire à l'obtention de l'article / du contenu.
	document.getElementById("VPmodal").style.display = "none";
}
function VPcloseAds(){
//fonction appelée si l'utilisateur quitte le parcours ViewPay.
	document.getElementById("VPmodal").style.display = "none";
}
function VPplayAds(){
}
</script>
```

Ce bloc de code permet de charger le fichier jkFbASQ.js, qui est servi par notre CDN, pour vous garantir d'avoir toujours la dernière version. Il est chargé en "defer" pour ne pas ralentir le chargeemnt de votre page.
Il crée également les emplacements pour accueillir la publicité, et définit les fonctions nécessaires à l'initialisation de ViewPay et qui vous permettent de gérer l'interaction avec ViewPay. 



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
    background-color: rgba(0, 0, 0, 0.85);
    z-index: 9999999 !important;
}
```

Si vous souhaitez modifier l'une de ces données, n'hésitez pas à contacter votre contact ViewPay

## Définition des fonctions et callbacks JavaScript

Les fonctions et callbacks fournis par Viewpay sont les suivants : 

Fonctions : 
- VPinitVideo() - appelée au onload du fichier script pour vérifier la disponibilité d’une publicité. Cette fonction appelle le callback VPexistAds() si une ou plusieurs vidéos ont été trouvées, et appelle le callback VPnoAds() si aucune n’a été trouvée pour cet utilisateur.
- VPloadAds() - vous pouvez appeler cette fonction une fois que vous avez eu la confirmation qu’une pub existe (via le callback VPexistAds()). Permet d’ouvrir le système Viewpay. (ex: onclick sur le bouton)

Callbacks :
- VPexistAds() - callback appelé si Viewpay a au moins une publicité à servir. Cette fonction vous permet d’afficher le bouton pour lancer Viewpay si vous recevez un OK. Si KO le callback VPnoAds() est appelé.
- VPnoAds() - callback appelé si Viewpay n’a aucune publicité à servir.
- VPplayAds() - callback de notification appelé lorsque l’utilisateur démarre la vidéo.
- VPcompleteAds() - appelé quand l’utilisateur a fini le parcours. Détaillez ici votre code pour débloquer l'article.
- VPcloseAds() - appelé si l’utilisateur ferme le parcours.

ViewPay est une solution qui répond aux appels reçus avec agilité et rapidité, il est donc avantageux pour vous de situer chacun des appels au début du chargement de la page Html.

## Bouton ViewPay

Nous vous conseillons d'adapter le wording du bouton ViewPay au mieux par rapport au contexte dans lequel il est intégré.

Exemple de bouton ViewPay dans la Presse :
![sample](https://cdn.jokerly.com/images/logosVP/Bouton_arrondi_bleu_presse_je.png)

Exemple de bouton ViewPay dans un jeu : 
![sample](https://cdn.jokerly.com/img/exemple_btn_jeu.png)

Nous conseillons également d'intégrer un élément visuel comme un picto ViewPay ou un bouton "Play" à votre bouton, pour susciter de l'intérêt chez l'utilisateur.
Voici des pictos que que nous mettons à votre disposition : 

Blanc : ![sample](https://cdn.jokerly.com/images/play_btn_white_small.svg) https://cdn.jokerly.com/images/play_btn_white_small.svg  
Gris clair : ![sample](https://cdn.jokerly.com/images/play_btn_lightgray_small.svg) https://cdn.jokerly.com/images/play_btn_lightgray_small.svg  
Gris foncé : ![sample](https://cdn.jokerly.com/images/play_btn_darkgray_small.svg) https://cdn.jokerly.com/images/play_btn_darkgray_small.svg  
Noir : ![sample](https://cdn.jokerly.com/images/play_btn_black_small.svg) https://cdn.jokerly.com/images/play_btn_black_small.svg  
Blanc : ![sample](https://cdn.jokerly.com/img/minilogo.png) https://cdn.jokerly.com/img/minilogo.png  
Noir : ![sample](https://cdn.jokerly.com/img/minilogo_noir.png) https://cdn.jokerly.com/img/minilogo_noir.png  

Vous pouvez positionner ce picto avec du css comme ceci :
```css
background: url(https://cdn.jokerly.com/images/play_btn_lightgray_small.png) 10px center no-repeat;
```
