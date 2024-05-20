
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

