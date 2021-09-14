
# File in edition process, subject to modifications.


# ViewPay HTML5: Startup guide
This guide aims at guiding you in the setup of the ViewPay JS widget in your web site.

As a reminder, ViewPay is a micro-payment system based on the user attention, it lets the user unlock a premium content simply by watching an ad.  Viewpay is intended to be an alternative to other options for unblocking premium content, and it should not be installed as the only option of a paywall.

Here is an example of an article unlocking with ViewPay : 

![sample](https://github.com/TechViewpay/ViewPay-iOS/blob/master/DocImages/parcours_vp_mobile3.png?raw=true)

## Javascript loading
```javascript
<script type="text/javascript" src="//cdn.jokerly.com/scripts/jkFbASQ.js"></script> 
```
The jkFbASQ.js file is the only file that must be loaded within the page that will host Viewpay (meaning the page that will display the paywall). This file is served by our CDN server, to ensure you always use the latest version. 

NB: You should place the script as high as possible in the page in order to optimize its loading time.



## Creation of a div to host the advertisement
Add a div in your page with the "cadreJokerlyADS" id.
```html
<div id="cadreJokerlyADS"></div>
```

The CSS design is generated automatically upon the ViewPay initialization, this way you don't have to add CSS. However, if you need to change the design, your CSS will have the priority. In this case, assign this div the dimensions you want to assign to Viewpay, making sure to keep a 1,44 width-to-height ratio.
We impose 650x450 for the desktop, and it must also be centered horizontally and vertically.
So here is the CSS that we load ourselves:

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

// The z-index is necessary for the visibility of the ad.
// The z-index must be increased if another frame / banner / other stands above the ViewPay div.
// This is mandatory in order to ensure high CPMs.

```
If you wish to modify any of these data, do not hesitate to contact your ViewPay contact.

The same logic goes for Mobile / Tablet sites.

For your mobile site, we use all available vertical space by opening the iframe in 100% width and height, unless you specify the maximum size you want to dedicate to ViewPay.

The media tags are as follows to allow mobile users to make the most of the ViewPay interface:

```css
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

```

## Définition of JavaScript functions and callbacks

Les fonctions et callbacks fournis par Viewpay sont les suivants : 

The functions and callbacks provided by Viewpay are the following ones:

Functions:
VPinit () - called at the page onload to check the availability of an advertisement. This function calls the VPexistAds () callback when one or more ads are found, and calls the VPnoAds () callback when no ad is available for this user.
VPloadAds () - called once you've had confirmation that an ad is available (via the VPexistAds () callback). It opens the Viewpay system.

Callbacks:
VPexistAds () - callback called if Viewpay has at least one advertisement to serve. This function allows you to display the button to launch Viewpay when you receive an OK (If KO the VPnoAds () callback is called).
VPnoAds () - callback called if Viewpay has no ads to serve.
VPplayAds () - notification callback called when the user starts playing the video.
VPcompleteAds () - called when the user has finished the Viewpay journey.
VPcloseAds () - called if the user closes ViewPay.

ViewPay is a solution that responds to incoming calls with agility and speed, therefore the best for you is to position each call closest to the start of the Html page loading, since it won't impact the page loading.

Here is the code to integrate in a script tag:
```javascript

<script> 
	function VPinitVideo(){
		JKFBASQ.init({
			site_id: 'b23d3f0235ae89e4', // functional demo id
			load_callback : VPexistAds,
			noads_callback : VPnoAds,
			complete_callback : VPcompleteAds,
			close_callback : VPcloseAds,
			play_callback : VPplayAds,
			cover:true,
		});
	}
		
	function VPexistAds(){
		alert("existAds");
		$("#btnShowViewpay").css("display","block");
	}
	function VPloadAds(){
		alert("loadAds");
		JKFBASQ.loadAds();
	}
	function VPnoAds(){
		$("#btnShowViewpay").css("display","none");
		alert("noAds");
	}
	function VPcompleteAds(){
		alert("completeAds");
		/*Once the ad is finished, the code to unlock the article must be placed here*/
	}
	function VPcloseAds(){
		alert("closeAds");
	}
	function VPplayAds(){
		alert(“playAds”);
	}
</script>
 ```

To unlock the article, insert within the VPcompleteAds() your function/code allowing to unlock the article.

NB: The VPinitVideo() function, that you have configured with your Viewpay identifier (site_id), must be called before the appearance of the Viewpay system on screen, in order to allow our system to query the advertisement catalog to find the appropriate available ads. 

Example : 
```html
<body onload="VPinitVideo()">
...
</body>
```
Or more simply within a tag ```html <script>```

## Securing the VPcompleteAds callback
Adding this server to server feature is not mandatory but strongly recommended in order to better secure the use of the Viewpay system on your site.
When a transaction is validated by the Viewpay server, an HTTP request will be made by our server to a URL provided by you, which informs you that a transaction has been completed. This way you can ensure that the user has seen the ad and is eligible for the desired content.

Request format
Once the Viewpay journey is successfully finished, our server will call a callback URL (configured in your Viewpay account: see with your contact at Viewpay).

The URL will be shaped like this:
```html
http:///your.callback.url/callback_page.html?ref=&editor_id=&challenge=&shared_secret=
```
Your callback URL is the URL that is set in your Viewpay account.
The "ref" and "challenge" parameters are ancient parameters which no longer need a value for new integrations.
The "editor_id" parameter is the site_id that was provided to you with the integration documentation.
The "shared_secret" parameter (not required) is an encrypted key defined in your Viewpay account that can be sent to you in order to secure the transaction (see your contact at Viewpay). 
You can optionally send us a specific key for each user by entering a new parameter in the Viewpay initialization: secureId = {yourSecureId}. This key will be sent back to you with the callback using a different parameter name: dynamicParameter = {yourSecureId}.

Exemple : 
```javascript
// In the init :
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

// Example of what you will receive in the callback : 
http://your.callback.url/callback_page.html?...&editor_id={your_editorID}&dynamicParameter=012345aze
```
IMPORTANT : In order for the transaction to be validated, our server awaits to receive a valid HTTP answer with the status 200 (OK).

## Call buttons to ViewPay
Here are 3 samples of the ViewPay call button that  are made available to you in order to respect the Viewpay design charter:

Press in white : http://cdn.jokerly.com/images/logosVP/Bouton_carre_blanc_press.png
![sample](https://cdn.jokerly.com/images/logosVP/Bouton_carre_blanc_press.png)

Press in blue : http://cdn.jokerly.com/images/logosVP/Bouton_carre_bleu_press.png
![sample](https://cdn.jokerly.com/images/logosVP/Bouton_carre_bleu_press.png)

Presse Bleu arrondi « Je » : http://cdn.jokerly.com/images/logosVP/Bouton_arrondi_bleu_presse_je.png
![sample](https://cdn.jokerly.com/images/logosVP/Bouton_arrondi_bleu_presse_je.png)


Here are some rules to respect in the design of this button : 
- The ViewPay logo must be present on the left side like in the samples above above,
- The background and text colors can be adapted to the publisher web site.
- The wording should be adapted as well as possible with the wording of the other alternative buttons presented in your paywall together with the ViewPay button. For example, if the other button says "I subscribe", then the ViewPay button should also use the "I" : "I access this article by"...
Don't hesitate to contact us to adapt our button to your specificities.

### Optimising the ViewPay button loading
It is essential to optimize the speed to display the Viewpay button, in order to maximize the click rate on the button and thus the income generated. You must therefore make sure to call VpInit () as early as possible in the page, so that the ad availability is already known when you display your paywall.

Despite this, as the page loading often calls for a multitude of processes (ads, content, analytics, etc.), it may happen that the response arrives after the display of the paywall: the risk is then that the user leaves the page even before the button has been displayed...

To avoid this case, a temporary, non-clickable ViewPay button must be displayed by default, which displays the Viewpay proposal and informs that the ad search process is in progress. This button is replaced by the clickable button as soon as the answer is obtained.

The logic is the following:
- Display of the ViewPay button grayed out and not clickable, with a wording such as "Searching for ads to unlock this article...".
- We check if there are any advertisements available,
	- If Yes (VPexistAds ()) : The button wording & color change and it becomes clickable
	- If No (VPnoAds ()) : The button disappears (or a message can be shown)

Technically speaking there are therefore two different buttons:
- A temporary (#btnVpLoading) and non-clickable button available from the start
- A final button (#btnShowViewPay), clickable but hidden. 
As soon as an advertisement is available, the VPloadAds function replaces the temporary buttin with the final one .

This way, any lag is avoided between the display of the paywall and that of the ViewPay button: this will allow readers to better discover the existence of the ViewPay button in the paywall.

Here is how to do this :
```html
<button id="btnVpLoading" style="display:block; background-color:grey; ">Searching for ads to unlock this article...</button>
<button id="btnShowViewpay" style="display:none; background-color:green;" onclick="VPloadAds()">Access this article by watching an ad</button>
```
The CSS, the wording and concerned div need to be readapted  according to the graphical charters and the integration.

The two ViewPay buttons now need to interact according to the ads availability:

```javascript
function VPexistAds(){
	alert("existAds");
	$("#btnVPLoading").css("display","none");
	$("#btnShowViewpay").css("display","block");
}
```	

```javascript
function VPnoAds(){
	$("#btnVpLoading").css("display","none");
	alert("noAds");
}
```

N.B. : It may be necessary to also hide what surrounds the button, for example: a wording located above the button, a separation between the two buttons, etc..

## Dark background
A dark background must be added around the Viewpay system, which helps optimize the user experience and attention. Here is an example of how this looks :
![sample](https://cdn.jokerly.com/images/logosVP/screenshotUK.png)


To do so, the background must be displayed at the same time as the ViewPay AdSelector, by adding it in the VPloadAds function:

```javascript
function VPloadAds(){
	document.getElementById("modal").style.display = 'block';
	JKFBASQ.loadAds();
}
```
A 0.9 background opacity is recommanded to ensure advertisers the optimal visibility of their vidéos. The "VPmodal" word represents the Id of the div having the following CSS code : 
```css
#VPmodal{
    width: 100%;
    height: 100%;
    display: none;
    position: fixed;
    background-color: rgba(0, 0, 0, 0.9);
    z-index: 1000;
}
```

This div must also disappear when the user reaches the VPcompleteAds, here how to get this :
 
```javascript 
function VPcompleteAds(){
	document.getElementById("cadre").style.display = 'none';
}
```

In addition, it must be removed if the user decides to quit the adSelector :
```javascript
function VPcloseAds(){
	document.getElementById("modal").style.display = 'none';
}	
```

Finally, the #cadreJokerlyAds div must be positioned inside the #modal div:

```html
<div id="modal">
	<div id="cadreJokerlyADS"></div>
</div>
```

NB : We noticed that the Bootstrap modals are by default displayed with a delay of 0.15 seconds. To correct this problem in a Bootstrap site, just run VPLoadAds in a setTimeout of at least 0.15s.

## Targeting information
It is possible (and very recommanded) to provide ViewPay with information that will allow us to better target advertisements for each user. If you have non-nominative information on the user, such as their gender and age, or even the category of the item to be unlocked (Economy, International, sport, etc.), this information can be entered within the Init function ( ).

Let's discuss the range of possibilities together when we start the integration!

```javascript
function VPinitVideo(){
	JKFBASQ.init({
		site_id: 'b23d3f0235ae89e4', // demo Id	
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

Not all the fields are mandatory. The default values ​​of these fields are therefore empty. 

| Parameters |      Example      |  Description |
|----------|:-------------:|-------|
| codeCategory (int) | 1 | Represents the article category <br>that the user wishes to unlock:<br> 0 : News,<br> 1 : Sports,<br> 2 : Economy,<br> 3 : Politics,<br> 4 : Environment,<br> 5 : Science,<br> 6 : Technology,<br> 7 : Others |
| age (int) | 35 | User's age |
| gender (char) | M | User's gender. Possible values 'M' or 'F' |
| country (char) | FR | User's country code |
| language (char) | fr | User's language code |
| postcode (int) | 75001 | User's postal code |


New codeCategory can be added according to your needs : if you can't find the categories that suit you, the ViewPay team is available to discuss it.

## Tips to optimize a paywall proposing ViewPay
Thanks to our experience with our publishers, here are some tips to maximize your click-through rate on the Viewpay  button:

Inserting the word "free", or "freely", in the button wording or around the ViewPay button can make the offer easier to understand and use.

- Presenting the alternative offers (Subscription, registration, Viewpay, etc.) in an equivalent / fair manner, and offer a clear choice for the user : offer 1 or offer 2. Adding “ OR ” or a visual separator between the buttons  is very useful to make  things clear.

- The graphic charter of the ViewPay button (length, border, etc.) can be modified in order to better respect yours.
- Note that an A/B test showed that using a green background for the button generated a better click rate than with a blue background! 
- Present the paywall as much at the top of the page as possible to avoid needing to scroll to make the VP button visible. Positioning the Viewpay button as the first option (vertically speaking) is preferable, for the same reason.

Each paywall is unique, so let's discuss things together during integration to get the best results!
