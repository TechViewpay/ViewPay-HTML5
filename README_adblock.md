# ViewPay Adblock Wall - Guide d'int&eacute;gration

Ce guide est destin&eacute; aux &eacute;diteurs qui souhaitent int&eacute;grer ViewPay sur leur **Adblock Wall** (popup bloquant pour les utilisateurs avec adblock activ&eacute;).

## 📌 Contexte

Si vous avez **d&eacute;j&agrave; une int&eacute;gration ViewPay** sur votre site (par exemple dans un paywall g&eacute;r&eacute; par un prestataire comme Qiota), vous devez utiliser cette version sp&eacute;cifique pour &eacute;viter les conflits entre les deux int&eacute;grations.

### Diff&eacute;rences avec l'int&eacute;gration standard

| &Eacute;l&eacute;ment | Int&eacute;gration standard | Int&eacute;gration Adblock Wall |
|---------|----------------------|--------------------------|
| **Script JS** | `jkFbASQ.js` | `jkFbASQ-adblock.js` |
| **Objet global** | `JKFBASQ` | `JKFBASQ_ADBLOCK` |
| **IDs des div** | `VPmodal`, `cadreJokerlyADS` | `VPmodalAdblock`, `cadreJokerlyADSAdblock` |
| **Fonctions** | `VPinitVideo()`, `VPloadAds()` | `VPinitVideoAdblock()`, `VPloadAdsAdblock()` |

---

## 🚀 Installation

### 1. Ajout du code HTML/JavaScript

Ajoutez ce code dans votre page, id&eacute;alement au chargement de la page :

```html
<script>
// Cr&eacute;ation des conteneurs pour ViewPay Adblock
const divVPmodalAdblock = document.createElement('div');
const divCadreJokerlyADSAdblock = document.createElement('div');
divVPmodalAdblock.setAttribute('id', 'VPmodalAdblock');
divCadreJokerlyADSAdblock.setAttribute('id', 'cadreJokerlyADSAdblock');
divVPmodalAdblock.appendChild(divCadreJokerlyADSAdblock);

// Chargement du script ViewPay Adblock
const vpScriptAdblock = document.createElement('script');
vpScriptAdblock.src = 'https://cdn.jokerly.com/scripts/jkFbASQ-adblock.js';
vpScriptAdblock.defer = true;
vpScriptAdblock.addEventListener('load', VPinitVideoAdblock);
document.body.appendChild(divVPmodalAdblock);
document.body.appendChild(vpScriptAdblock);

// Initialisation de ViewPay Adblock
function VPinitVideoAdblock(){
    JKFBASQ_ADBLOCK.init({
        site_id: 'VOTRE_SITE_ID', // Remplacer par votre ID fourni par ViewPay
        load_callback: VPexistAdsAdblock,
        noads_callback: VPnoAdsAdblock,
        complete_callback: VPcompleteAdsAdblock,
        close_callback: VPcloseAdsAdblock,
        play_callback: VPplayAdsAdblock,
        cover: false,
    });
}

// Callback : au moins une publicit&eacute; disponible
function VPexistAdsAdblock(){
    // Afficher le bouton ViewPay dans votre adblock wall
    console.log('ViewPay Adblock: Publicit&eacute; disponible');
    document.getElementById('btnViewPayAdblock').style.display = 'block';
}

// Callback : aucune publicit&eacute; disponible
function VPnoAdsAdblock(){
    // Cacher le bouton ViewPay
    console.log('ViewPay Adblock: Aucune publicit&eacute; disponible');
    document.getElementById('btnViewPayAdblock').style.display = 'none';
}

// Fonction pour lancer ViewPay (appel&eacute;e au clic sur le bouton)
function VPloadAdsAdblock(){
    document.getElementById("VPmodalAdblock").style.display = "block";
    JKFBASQ_ADBLOCK.loadAds();
}

// Callback : l'utilisateur a compl&eacute;t&eacute; le parcours ViewPay
function VPcompleteAdsAdblock(){
    // Retirer le popup adblock
    document.getElementById("VPmodalAdblock").style.display = "none";
    document.getElementById("adblockWall").style.display = "none";

    // Optionnel : enregistrer que l'utilisateur a d&eacute;bloqu&eacute; le contenu
    // sessionStorage.setItem('viewpayAdblockCompleted', 'true');

    console.log('ViewPay Adblock: Parcours compl&eacute;t&eacute;');
}

// Callback : l'utilisateur a ferm&eacute; le parcours sans le compl&eacute;ter
function VPcloseAdsAdblock(){
    document.getElementById("VPmodalAdblock").style.display = "none";
    console.log('ViewPay Adblock: Parcours ferm&eacute;');
}

// Callback : l'utilisateur a d&eacute;marr&eacute; la vid&eacute;o
function VPplayAdsAdblock(){
    console.log('ViewPay Adblock: Vid&eacute;o d&eacute;marr&eacute;e');
}
</script>
```

---

### 2. Ajout du CSS

Ajoutez ce CSS pour styliser le modal ViewPay :

```css
/* Modal ViewPay Adblock */
#VPmodalAdblock {
    top: 0;
    width: 100%;
    height: 100%;
    display: none;
    position: fixed;
    background-color: rgba(0, 0, 0, 0.85);
    z-index: 9999999 !important;
}

/* Cadre ViewPay Adblock - Desktop */
#cadreJokerlyADSAdblock {
    margin: auto;
    top: 0;
    right: 0;
    left: 0;
    position: fixed;
    bottom: 0;
    width: 650px !important;
    height: 450px !important;
    z-index: 9999999 !important;
}

/* Cadre ViewPay Adblock - Mobile (max 600px) */
@media screen and (max-width: 600px) {
    #cadreJokerlyADSAdblock {
        width: 100% !important;
        height: 100% !important;
        margin-top: 0;
    }
}

/* Cadre ViewPay Adblock - Tablette (601px - 1024px) */
@media screen and (min-width: 601px) and (max-width: 1024px) {
    #cadreJokerlyADSAdblock {
        width: 100% !important;
        height: 100% !important;
        margin-top: 0;
        margin-left: 0;
    }
}
```

---

### 3. Int&eacute;gration dans votre Adblock Wall

Exemple de structure HTML pour votre adblock wall :

```html
<div id="adblockWall" style="display: none;">
    <div class="adblock-message">
        <h2>D&eacute;sactivez votre bloqueur de publicit&eacute;</h2>
        <p>Pour continuer &agrave; lire gratuitement, merci de d&eacute;sactiver votre adblock.</p>

        <!-- Bouton ViewPay -->
        <button id="btnViewPayAdblock" onclick="VPloadAdsAdblock()" style="display: none;">
            <img src="https://cdn.jokerly.com/images/play_btn_white_small.svg" alt="Play">
            Regarder une publicit&eacute; pour d&eacute;bloquer
        </button>

        <p><small>Ou d&eacute;sactivez votre adblock pour acc&eacute;der au contenu</small></p>
    </div>
</div>

<script>
// D&eacute;tection de l'adblock et affichage du wall
if (adblockDetected) {
    document.getElementById('adblockWall').style.display = 'block';
}
</script>
```

---

## 📋 R&eacute;f&eacute;rence des fonctions et callbacks

### Fonctions principales

| Fonction | Description | Utilisation |
|----------|-------------|-------------|
| `VPinitVideoAdblock()` | Initialise ViewPay et v&eacute;rifie la disponibilit&eacute; des pubs | Appel&eacute;e automatiquement au chargement du script |
| `VPloadAdsAdblock()` | Lance le parcours ViewPay | Appeler au clic sur le bouton ViewPay |

### Callbacks

| Callback | Quand est-il appel&eacute; ? | Action recommand&eacute;e |
|----------|----------------------|----------------------|
| `VPexistAdsAdblock()` | Au moins une pub disponible | Afficher le bouton ViewPay |
| `VPnoAdsAdblock()` | Aucune pub disponible | Cacher le bouton ViewPay |
| `VPplayAdsAdblock()` | L'utilisateur d&eacute;marre la vid&eacute;o | Tracking optionnel |
| `VPcompleteAdsAdblock()` | Parcours compl&eacute;t&eacute; avec succ&egrave;s | Retirer le popup adblock |
| `VPcloseAdsAdblock()` | L'utilisateur ferme sans finir | Fermer le modal ViewPay |

---

## 🎨 Personnalisation du bouton ViewPay

### Exemples de wording

- "Regarder une publicit&eacute; pour d&eacute;bloquer"
- "D&eacute;bloquer avec ViewPay"
- "Acc&eacute;der gratuitement en 30 secondes"

### Pictos disponibles

Blanc : ![sample](https://cdn.jokerly.com/images/play_btn_white_small.svg) `https://cdn.jokerly.com/images/play_btn_white_small.svg`
Gris clair : ![sample](https://cdn.jokerly.com/images/play_btn_lightgray_small.svg) `https://cdn.jokerly.com/images/play_btn_lightgray_small.svg`
Gris fonc&eacute; : ![sample](https://cdn.jokerly.com/images/play_btn_darkgray_small.svg) `https://cdn.jokerly.com/images/play_btn_darkgray_small.svg`
Noir : ![sample](https://cdn.jokerly.com/images/play_btn_black_small.svg) `https://cdn.jokerly.com/images/play_btn_black_small.svg`

### Exemple de bouton styl&eacute;

```css
#btnViewPayAdblock {
    background: #0066cc url(https://cdn.jokerly.com/images/play_btn_white_small.svg) 15px center no-repeat;
    background-size: 24px;
    color: white;
    border: none;
    padding: 12px 20px 12px 50px;
    font-size: 16px;
    border-radius: 5px;
    cursor: pointer;
    margin: 20px 0;
}

#btnViewPayAdblock:hover {
    background-color: #0052a3;
}
```

---

## ⚠️ Points d'attention

### 1. Ne pas m&eacute;langer les deux versions

Si vous avez d&eacute;j&agrave; une int&eacute;gration ViewPay standard (paywall, etc.) :
- ✅ Utilisez `jkFbASQ-adblock.js` pour l'adblock wall
- ✅ Gardez `jkFbASQ.js` pour l'int&eacute;gration existante
- ❌ N'utilisez PAS le m&ecirc;me script pour les deux

### 2. IDs uniques

Les IDs des div doivent &ecirc;tre uniques sur la page :
- `VPmodalAdblock` (pas `VPmodal`)
- `cadreJokerlyADSAdblock` (pas `cadreJokerlyADS`)

### 3. Z-index

Le modal ViewPay doit toujours &ecirc;tre au-dessus de votre adblock wall :
```css
#adblockWall { z-index: 9999998; }
#VPmodalAdblock { z-index: 9999999; }
```

---

## 🧪 Test de l'int&eacute;gration

### Checklist de validation

- [ ] Le script `jkFbASQ-adblock.js` se charge sans erreur
- [ ] `VPinitVideoAdblock()` est appel&eacute;e au chargement
- [ ] Le bouton ViewPay s'affiche quand `VPexistAdsAdblock()` est appel&eacute;
- [ ] Le modal s'ouvre au clic sur le bouton
- [ ] Le parcours ViewPay se d&eacute;roule correctement
- [ ] `VPcompleteAdsAdblock()` retire le popup adblock
- [ ] `VPcloseAdsAdblock()` ferme le modal sans d&eacute;bloquer

### D&eacute;bogage

Ouvrez la console du navigateur (F12) pour voir les logs :
```
ViewPay Adblock: Publicité disponible
ViewPay Adblock: Vidéo démarrée
ViewPay Adblock: Parcours complété
```

---

## 📞 Support

Pour toute question ou assistance, contactez votre interlocuteur ViewPay.

**Version du document :** 1.0
**Derni&egrave;re mise &agrave; jour :** 2025
