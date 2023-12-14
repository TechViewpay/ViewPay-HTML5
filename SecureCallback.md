## Sécurisation du callback VPcompleteAds
L’ajout de cette fonctionnalité n’est pas obligatoire mais fortement conseillé afin de sécuriser l’utilisation du système Viewpay dans votre site.
Lorsqu’une transaction est validée par le serveur Viewpay, une requête HTTP va être  effectuée par notre serveur vers une URL que vous nous aurez fournie, qui vous prévient qu’une transaction a été effectuée. Vous pouvez ainsi vous assurer que l’utilisateur a bien vu la publicité et est éligible au bien que vous voulez lui offrir. 

Format de la requête
Lorsque le parcours Viewpay s’est déroulé avec succès, notre serveur appellera une URL de callback. (configurée dans votre compte Viewpay : voir avec votre contact chez Viewpay)

L’URL aura la forme suivante :
```html
http:///your.callback.url/callback_page.html?ref=&editor_id=&challenge=&shared_secret=
```
Votre URL de callback est l’URL qui est renseignée dans votre compte Viewpay.
Les paramètres ref, et challenge sont des paramètres spécifiques à des éditeurs anciens de Viewpay, et ne sont pas renseignés pour une nouvelle intégration. 
Le paramètre editor_id correspond au site_id qui vous a été communiqué avec la documentation d’intégration.
Le paramètre shared_secret est une clé cryptée définie dans votre compte Viewpay qui peut vous être envoyée afin de sécuriser la transaction (à voir avec votre contact chez Viewpay). (non obligatoire)
Vous pouvez éventuellement nous envoyer une clé spécifique à chaque utilisateur en renseignant dans l’initialisation de Viewpay un nouveau paramètre : secureId={yourSecureId}. Cette clé vous sera renvoyée avec le callback dans un paramètre avec un nom différent : dynamicParameter={yourSecureId}

Exemple : 
```javascript
// Dans l’init :
JKFBASQ.init({
	site_id: 'your_id',
	load_callback: VPexistAds,
	noads_callback: VPnoAds,
	complete_callback: VPcompleteAds,
	close_callback: VPcloseAds,
	play_callback : VPplay,
	cover:true,
	codeCategory:1,
	secureId : '012345aze',
	userInfo : {
		age:35,
		gender:'M',
		country:'FR',
		language:'fr',
		postcode:75018
	}
});

// Ce que vous recevrez dans le callback : 
http://your.callback.url/callback_page.html?...&editor_id={your_editorID}&dynamicParameter=012345aze
```
IMPORTANT : Afin que la transaction ait bien lieu, notre serveur attend de recevoir une réponse HTTP valide avec le statut 200 (OK).
