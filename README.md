# ViewPay HTML5 : guide d'intégration

Ce guide vous accompagne dans la mise en place du widget JS ViewPay sur votre site web.

ViewPay est une solution de micro-paiement par l'attention publicitaire : l'utilisateur débloque un contenu premium en regardant une publicité. ViewPay a vocation à être une alternative aux autres options de votre paywall, et ne doit pas en être la seule option.

Voici un exemple de déblocage d'article avec ViewPay :

![sample](https://github.com/TechViewpay/ViewPay-iOS/blob/master/DocImages/parcours_vp_mobile3.png?raw=true)

> Ce guide suffit pour une intégration standard. Consentement (CMP), sites SPA, personnalisation des dimensions, optimisation du paywall : voir le [guide avancé](README_avance.md).

## Le principe

1. Au chargement de la page, ViewPay cherche une publicité pour ce lecteur.
2. S'il y en a une, vous affichez le bouton ViewPay dans votre paywall.
3. Au clic, ViewPay s'ouvre par-dessus la page et diffuse la publicité.
4. Quand le lecteur a terminé le parcours, vous débloquez le contenu.

## Le code à intégrer

Trois éléments à copier dans la page qui affiche le paywall : le bouton, le CSS et le script.

### 1. Le bouton

À placer dans votre paywall. Il est masqué par défaut et n'apparaît que si une publicité est disponible.

```html
<button type="button" id="btnShowViewPay" style="display:none" onclick="VPloadAds()">
	Je regarde une publicité pour accéder à l'article
</button>
```

L'identifiant `btnShowViewPay` est reconnu par ViewPay : le script affiche lui-même ce bouton quand une publicité est trouvée, et peut remplacer son texte par celui configuré pour votre site. Si le texte affiché n'est pas celui que vous attendez, contactez-nous.

### 2. Le CSS

```css
#VPmodal{
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	display: none;
	position: fixed;
	background-color: rgba(0, 0, 0, 0.85);
	z-index: 9999999 !important;
}
#cadreJokerlyADS{
	margin: auto;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	position: fixed;
	width: 650px !important;
	height: 450px !important;
	z-index: 9999999 !important;
}
```

ViewPay s'affiche en 650×450 centré sur desktop. Sur mobile et tablette (fenêtre de 1100 px de large ou moins), le script passe automatiquement en plein écran : aucune media query n'est nécessaire.

Le `z-index` garantit la visibilité de la publicité : augmentez-le si un élément de votre page (bannière, header fixe, autre iframe) passe au-dessus de ViewPay.

### 3. Le script

À placer dans le `<body>`, le plus haut possible : plus la recherche de publicité démarre tôt, plus le bouton s'affiche vite.

```html
<script>
// Emplacement de la publicité
var vpModal = document.createElement('div');
vpModal.id = 'VPmodal';
var vpCadre = document.createElement('div');
vpCadre.id = 'cadreJokerlyADS';
vpModal.appendChild(vpCadre);
document.body.appendChild(vpModal);

// Chargement de ViewPay (asynchrone : ne ralentit pas votre page)
var vpScript = document.createElement('script');
vpScript.src = 'https://cdn.jokerly.com/scripts/jkFbASQ.js';
vpScript.addEventListener('load', VPinitVideo);
vpScript.addEventListener('error', VPnoAds); // script bloqué ou injoignable
document.body.appendChild(vpScript);

// Lance la recherche de publicité
function VPinitVideo(){
	JKFBASQ.init({
		site_id: 'b23d3f0235ae89e4', // identifiant de démo : remplacez-le par le vôtre
		load_callback: VPexistAds,
		noads_callback: VPnoAds,
		play_callback: VPplayAds,
		complete_callback: VPcompleteAds,
		close_callback: VPcloseAds
	});
}

// Une publicité est disponible : on affiche le bouton
function VPexistAds(){
	document.getElementById('btnShowViewPay').style.display = 'block';
}

// Aucune publicité disponible : on masque le bouton
function VPnoAds(){
	document.getElementById('btnShowViewPay').style.display = 'none';
	document.getElementById('VPmodal').style.display = 'none';
}

// Clic sur le bouton : on ouvre ViewPay
function VPloadAds(){
	document.getElementById('VPmodal').style.display = 'block';
	JKFBASQ.loadAds();
}

// Le lecteur a démarré la vidéo (notification, rien à faire)
function VPplayAds(){
}

// Le lecteur a terminé le parcours : débloquez le contenu ici
function VPcompleteAds(){
	document.getElementById('VPmodal').style.display = 'none';
	// votre code de déblocage de l'article
}

// Le lecteur a fermé ViewPay avant la fin
function VPcloseAds(){
	document.getElementById('VPmodal').style.display = 'none';
}
</script>
```

## Qui appelle quoi

Deux fonctions sont appelées par vous :

| Fonction | Quand l'appeler |
|---|---|
| `JKFBASQ.init({...})` | Dès que le script est chargé. Lance la recherche de publicité. |
| `JKFBASQ.loadAds()` | Au clic sur le bouton. Ouvre ViewPay. |

Cinq callbacks, passés à `init`, sont appelés par ViewPay :

| Option de `init` | Appelé quand | Ce que vous y faites |
|---|---|---|
| `load_callback` | Une publicité est disponible | Afficher le bouton |
| `noads_callback` | Aucune publicité disponible | Masquer le bouton |
| `play_callback` | Le lecteur démarre la vidéo | Rien d'obligatoire (optionnel) |
| `complete_callback` | Le lecteur a terminé le parcours | Débloquer le contenu |
| `close_callback` | Le lecteur ferme ViewPay avant la fin | Fermer la modale |

Attention au nom : `load_callback` signale qu'une publicité est **disponible**, alors que `loadAds()` **ouvre** ViewPay.

Fournissez toujours `load_callback`, `noads_callback`, `complete_callback` et `close_callback`.

## Tester votre intégration

L'identifiant de démo `b23d3f0235ae89e4` permet de tester sans attendre votre identifiant définitif. Vérifiez les points suivants :

- le bouton apparaît quelques instants après le chargement de la page ;
- le clic ouvre ViewPay par-dessus la page, centré sur desktop et en plein écran sur mobile ;
- à la fin du parcours, le contenu est débloqué ;
- la fermeture avant la fin ramène au paywall, sans débloquer le contenu ;
- si `cdn.jokerly.com` est bloqué (bloqueur de publicité), le bouton reste masqué et le reste du paywall fonctionne.

Avant la mise en production, remplacez l'identifiant de démo par le `site_id` fourni par votre contact ViewPay.

## Le bouton ViewPay

Adaptez le wording du bouton au contexte et à l'autre option proposée dans votre paywall : si celle-ci est « Je m'inscris », utilisez aussi le « je » (« J'accède à cet article »).

Exemple de bouton dans la presse :

![sample](https://cdn.jokerly.com/images/logosVP/Bouton_arrondi_bleu_presse_je.png)

Exemple de bouton dans un jeu :

![sample](https://cdn.jokerly.com/img/exemple_btn_jeu.png)

Nous conseillons d'intégrer au bouton un élément visuel, picto ViewPay ou bouton « Play », pour susciter l'intérêt du lecteur. Pictos à votre disposition :

| Picto | URL |
|---|---|
| ![sample](https://cdn.jokerly.com/images/play_btn_white_small.svg) Play blanc | https://cdn.jokerly.com/images/play_btn_white_small.svg |
| ![sample](https://cdn.jokerly.com/images/play_btn_lightgray_small.svg) Play gris clair | https://cdn.jokerly.com/images/play_btn_lightgray_small.svg |
| ![sample](https://cdn.jokerly.com/images/play_btn_darkgray_small.svg) Play gris foncé | https://cdn.jokerly.com/images/play_btn_darkgray_small.svg |
| ![sample](https://cdn.jokerly.com/images/play_btn_black_small.svg) Play noir | https://cdn.jokerly.com/images/play_btn_black_small.svg |
| ![sample](https://cdn.jokerly.com/img/minilogo.png) Logo blanc | https://cdn.jokerly.com/img/minilogo.png |
| ![sample](https://cdn.jokerly.com/img/minilogo_noir.png) Logo noir | https://cdn.jokerly.com/img/minilogo_noir.png |

Vous pouvez positionner le picto en CSS :

```css
background: url(https://cdn.jokerly.com/images/play_btn_lightgray_small.svg) 10px center no-repeat;
```

## Aller plus loin

Le [guide avancé](README_avance.md) couvre :

- le détail du comportement de ViewPay (délais, appels multiples des callbacks) ;
- la personnalisation des dimensions ;
- le consentement (CMP / TCF) ;
- les sites en SPA ;
- l'affichage d'un bouton d'attente pendant la recherche de publicité ;
- les conseils pour optimiser votre paywall.

Pour toute question, contactez votre interlocuteur ViewPay.
