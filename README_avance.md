# ViewPay HTML5 : guide avancé

Ce guide complète le [guide d'intégration](README.md), à lire en premier. Il détaille le comportement du script et les cas particuliers.

## Comportement détaillé

### Déroulement de la recherche de publicité

À l'appel de `JKFBASQ.init()` :

1. ViewPay attend le signal de consentement de votre CMP, 500 ms au maximum (voir [Consentement](#consentement-cmp--tcf)).
2. Il interroge le serveur ViewPay pour ce lecteur.
3. Il appelle `load_callback` si une publicité est disponible, `noads_callback` sinon.

Si le serveur ne répond pas, ViewPay attend 10 secondes, refait une tentative, puis appelle `noads_callback` : dans le pire des cas, la réponse négative arrive donc après une vingtaine de secondes.

### Cas où aucun callback n'est appelé

- **Le script n'a pas pu être chargé** (bloqueur de publicité, réseau) : `JKFBASQ` n'existe pas. Écoutez l'événement `error` de la balise script, comme dans le guide d'intégration.
- **Le div `#cadreJokerlyADS` est absent de la page** : ViewPay attend qu'il apparaisse avant de lancer la recherche.

Un `site_id` absent ou invalide déclenche immédiatement `noads_callback`.

### Les callbacks peuvent être appelés plusieurs fois

Si le lecteur répond à votre bandeau de consentement après l'appel à `init`, ViewPay relance la recherche avec le nouveau signal, et `load_callback` ou `noads_callback` est rappelé. Un `noads_callback` peut ainsi être suivi d'un `load_callback`.

Écrivez donc ces deux callbacks de façon à pouvoir être rejoués : masquez ou affichez le bouton, sans le supprimer de la page.

`complete_callback` n'est appelé qu'une fois par recherche.

### Appeler `loadAds()` avant la réponse

Si `loadAds()` est appelé alors que la recherche est encore en cours, ViewPay s'ouvre automatiquement dès qu'une publicité est trouvée. Si aucune n'est trouvée, `noads_callback` est appelé : pensez à y refermer `#VPmodal`, comme dans le guide d'intégration.

## Options de `JKFBASQ.init()`

| Option | Type | Rôle |
|---|---|---|
| `site_id` | chaîne | Identifiant de votre site, fourni par ViewPay. Obligatoire. |
| `load_callback` | fonction | Une publicité est disponible. Obligatoire. |
| `noads_callback` | fonction | Aucune publicité disponible. Obligatoire. |
| `complete_callback` | fonction | Le lecteur a terminé le parcours. Obligatoire. |
| `close_callback` | fonction | Le lecteur a fermé ViewPay avant la fin. Obligatoire. |
| `play_callback` | fonction | Le lecteur a démarré la vidéo. Optionnel, appelé une seule fois. |
| `agree_cookie_callback` | fonction | Le consentement publicitaire est acquis. Optionnel. |
| `desagree_cookie_callback` | fonction | Le consentement publicitaire est absent ou refusé. Optionnel. |

L'option `cover`, présente dans d'anciens exemples, n'a plus d'effet : vous pouvez la retirer.

D'autres options existent pour des besoins spécifiques (ciblage, affichage intégré à la page plutôt qu'en modale). Contactez votre interlocuteur ViewPay.

## Chargement par balise script

Si vous préférez une balise classique à l'injection dynamique du guide d'intégration :

```html
<div id="VPmodal">
	<div id="cadreJokerlyADS"></div>
</div>

<script src="https://cdn.jokerly.com/scripts/jkFbASQ.js" defer onload="VPinitVideo()" onerror="VPnoAds()"></script>
```

Les fonctions `VPinitVideo` et `VPnoAds` doivent être définies avant cette balise.

Évitez `<body onload="VPinitVideo()">` : cet événement attend le chargement de toutes les ressources de la page (images, publicités), ce qui retarde inutilement l'affichage du bouton.

Le fichier est servi par notre CDN pour vous garantir la dernière version : ne l'hébergez pas vous-même.

## Dimensions et affichage

- **Desktop** : 650×450, centré horizontalement et verticalement. Si vous changez la taille, conservez un ratio largeur/hauteur de 1,44. La taille minimale est de 290×250.
- **Mobile et tablette** (fenêtre de 1100 px de large ou moins) : le script force le plein écran à l'ouverture, quel que soit votre CSS.
- **`z-index`** : ViewPay doit rester au-dessus de tous les éléments de la page. Une publicité partiellement masquée est moins bien valorisée.

Si vous utilisez Bootstrap : ses modales s'affichent par défaut avec un délai de 0,15 seconde. Si ViewPay s'ouvre depuis une modale Bootstrap, lancez `VPloadAds` dans un `setTimeout` d'au moins 150 ms.

## Consentement (CMP / TCF)

Vous n'avez rien à configurer si votre CMP est conforme au TCF v2 (Didomi, OneTrust, Sirdata, etc.) : ViewPay lit le consentement via l'API standard `__tcfapi`, et à défaut dans le cookie `euconsent-v2`.

- Avec le consentement du lecteur aux finalités 1, 2, 7 et 10, ViewPay fonctionne normalement.
- Sans consentement, ou si aucune CMP n'est détectée, ViewPay fonctionne quand même, sans dépôt de cookies publicitaires.

Chargez votre CMP avant ViewPay : si elle n'est pas détectée dans les 500 ms qui suivent le chargement du script, ViewPay se rabat sur le cookie `euconsent-v2` et, s'il est absent, considère le consentement comme non donné.

Si la CMP est détectée mais que le lecteur n'a pas encore répondu, la recherche démarre sans consentement, puis est relancée à sa réponse (voir [Les callbacks peuvent être appelés plusieurs fois](#les-callbacks-peuvent-être-appelés-plusieurs-fois)).

Les options `agree_cookie_callback` et `desagree_cookie_callback` vous permettent d'être notifié de l'état retenu par ViewPay.

## Sites en SPA

ViewPay associe la publicité à l'URL et au titre de la page lus au moment de `init`. Sur un site qui change d'article sans recharger la page :

- rappelez `JKFBASQ.init({...})` à chaque changement d'article, **après** la mise à jour de l'URL et du titre ;
- conservez `#VPmodal` et `#cadreJokerlyADS` dans la page d'un article à l'autre, ou recréez-les avant de rappeler `init` ;
- ne rechargez pas le script : une seule balise suffit pour toute la session.

## Afficher un bouton d'attente

La vitesse d'affichage du bouton ViewPay conditionne directement le taux de clic, donc vos revenus. Malgré un appel à `init` très tôt, la réponse peut arriver après l'affichage du paywall, et le lecteur peut quitter la page sans avoir vu le bouton.

Pour l'éviter, affichez d'emblée un bouton grisé et non cliquable, remplacé par le bouton actif dès la réponse :

```html
<button type="button" id="btnShowViewPay" disabled style="cursor:not-allowed">
	Recherche de publicité…
</button>
```

```javascript
function VPexistAds(){
	var btnVp = document.getElementById('btnShowViewPay');
	btnVp.disabled = false;
	btnVp.style.cursor = 'pointer';
	btnVp.style.backgroundColor = '#1bbbec';
	btnVp.textContent = 'Je regarde une publicité';
	btnVp.onclick = VPloadAds;
}

function VPnoAds(){
	var btnVp = document.getElementById('btnShowViewPay');
	btnVp.disabled = true;
	btnVp.style.cursor = 'not-allowed';
	btnVp.onclick = null;
	btnVp.textContent = 'Aucune publicité disponible';
	document.getElementById('VPmodal').style.display = 'none';
}
```

Avec ce bouton d'attente, deux précautions s'imposent :

- **Branchez `VPnoAds` sur l'événement `error` du script**, comme dans le guide d'intégration. Sans cela, un lecteur équipé d'un bloqueur de publicité verrait « Recherche de publicité… » indéfiniment.
- **Ajoutez votre propre délai de garde** si vous ne voulez pas laisser le bouton en attente jusqu'à 20 secondes en cas de panne réseau :

```javascript
var vpReponseRecue = false;
setTimeout(function(){
	if(!vpReponseRecue) VPnoAds();
}, 5000);
// puis passez vpReponseRecue à true au début de VPexistAds et de VPnoAds
```

Le texte du bouton peut être remplacé par celui configuré pour votre site quand une publicité est disponible. S'il ne correspond pas à votre choix, contactez-nous.

## Optimiser votre paywall

Quelques conseils tirés de notre expérience chez nos éditeurs pour maximiser le taux de clic :

- **Utilisez le mot « gratuit »** ou « gratuitement » dans le wording du bouton ou autour : il facilite la compréhension de l'offre.
- **Présentez les offres à égalité** (abonnement, inscription, ViewPay) et proposez un choix clair entre l'offre 1 et l'offre 2. Un séparateur « OU BIEN » clarifie beaucoup.
- **Placez le paywall le plus haut possible** dans la page, pour que le bouton ViewPay soit visible sans scroller. Le placer en première option, verticalement, est préférable pour la même raison.
- **Adaptez la charte du bouton** (longueur, bordure, couleurs) à la vôtre.

Chaque paywall est unique : discutons-en au moment de l'intégration pour optimiser le rendu.
