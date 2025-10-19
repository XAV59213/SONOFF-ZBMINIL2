# Installer et configurer le **SONOFF ZBMINIL2** ZigBee sur Home Assistant  
## Guide complet (poussoir, va-et-vient, interrupteur simple)

<img src="https://cdn.forumdomotique.com/monthly_2025_10/Designsanstitre.png.373e664afc5de5ad42117276219f44ef.png" alt="Schéma de design domotique" width="500" height="300">

[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-2024%2B-blue)](#)
[![Zigbee2MQTT](https://img.shields.io/badge/Zigbee2MQTT-Support-green)](#)
[![ZHA](https://img.shields.io/badge/ZHA-Support-green)](#)
[![Zigbee-3.0](https://img.shields.io/badge/Zigbee-3.0-orange)](#)

> ⚠️ **Sécurité électricité**
>
> Travaillez **hors tension** (disjoncteur coupé). Vérifiez toujours l’absence de tension au multimètre.  
> En cas de doute, **faites intervenir un électricien qualifié**. Respectez la **NF C 15-100**.

---

## Table des matières

- [Aperçu](#aperçu)
- [Pourquoi ZBMINIL2 + Home Assistant ? Spécifications](#pourquoi-zbminil2--home-assistant--spécifications)
- [Matériel nécessaire](#matériel-nécessaire)
- [Étapes préliminaires : identifier phase et neutre](#étapes-préliminaires--identifier-phase-et-neutre)
- [Mode poussoir (remplacer un télérupteur)](#mode-poussoir--remplacer-un-télérupteur)
- [Mode va-et-vient (double commande)](#mode-va-et-vient--double-commande)
- [Mode interrupteur simple](#mode-interrupteur-simple)
- [Illustration pratique : pas à pas (inter simple)](#illustration-pratique--pas-à-pas-inter-simple)
- [Exemples d’automatisations Home Assistant](#exemples-dautomatisations-home-assistant)
- [Avantages & limites](#avantages--limites)
- [Dépannage rapide](#dépannage-rapide)
- [Annexes (images, schémas)](#annexes-images-schémas)
- [Conclusion](#conclusion)

---

## Aperçu

Dans le monde de la domotique, les modules ZigBee comme le **SONOFF ZBMINIL2** offrent une flexibilité incroyable pour moderniser vos interrupteurs existants sans gros travaux. Ce petit module compact se glisse derrière un interrupteur mural standard ou directement dans votre tableau électrique et transforme n'importe quel bouton en commande intelligente : **allumage/extinction à distance**, simulation de présence, scénarios (ex. : lumière au lever du soleil), etc.

Si vous utilisez **Home Assistant** comme hub, l’intégration du ZBMINIL2 est simple via **ZHA** ou **Zigbee2MQTT** (ZigBee 3.0).

**Matériel testé (exemple de référence)**  
- Home Assistant OS **2025.9.4** sur **Raspberry Pi 5**  
- Clé **SONOFF ZigBee 3.0 USB Dongle Plus**  
- **Zigbee2MQTT**

---

## Pourquoi ZBMINIL2 + Home Assistant ? Spécifications

Le **SONOFF ZBMINIL2** est un module **relais ZigBee compact** *(≈ 39 × 32 × 17 mm)* conçu pour s'intégrer derrière un interrupteur mural monophasé.  
- **Charge** : jusqu’à **10 A / 230 V** (éclairages, petits appareils).  
- **Sans neutre** : gros avantage vs ZBMINI classique.  
- **Relais sec** : s’adapte à la majorité des installations.  
- **Interopérabilité** : intégration native **ZHA** ou **Zigbee2MQTT**.  
- **Stabilité** : latence constatée < 200 ms, lien stable sur 1 an (tests personnels).  


<img src="https://forumdomotique.com/uploads/monthly_2025_10/Zbminil2.png.83a8e32aed3a9a435fdc8e502a797313.png" alt="Schéma de design domotique" width="500" height="300">

Avec Home Assistant, vous **centralisez** le contrôle, créez des **automatisations** (ex. : éteindre si personne dans la pièce), et exposez à vos **assistants vocaux**.

---

## Matériel nécessaire

- 1× **SONOFF ZBMINIL2**  
- Un **hub ZigBee** compatible (ConBee II, Sonoff Zigbee Bridge, SkyConnect/HA)  
- **Home Assistant** (2024 + recommandé)  
- **Zigbee2MQTT** *ou* **ZHA** (+ intégration MQTT si Zigbee2MQTT)  
- **Outils** : tournevis isolé, **multimètre**  
- **Interrupteur** existant : simple, va-et-vient ou poussoir/télérupteur

> ⚠️ Travaillez **hors tension**.

---

## Étapes préliminaires : identifier phase et neutre

1. **Préparer le multimètre** : mode tension **AC (V~)**, plage 200 V/600 V.  
2. **Mesurer** : entre **phase** et **neutre** ≈ **230 V** en France.  
3. **Différencier** :  
   - Entre **phase** et **terre** ≈ 230 V ⇒ c’est la **phase**.  
   - Entre **neutre** et **terre** ≈ 0 V.  
4. **Couleurs usuelles** : **phase** (marron/noir), **neutre** (bleu), **terre** (vert/jaune).  
5. **Alternative** : tournevis testeur (indicatif, moins fiable).

> Guide pratique : Legrand propose un guide synthétique des points clés **NF C 15-100** (rév. 23/08/2024).

---

## Mode poussoir : remplacer un télérupteur

La **fonction poussoir** alterne l’état ON/OFF à chaque impulsion. Idéal pour **remplacer un télérupteur** et conserver plusieurs points de commande via **S1/S2** (plus d’extensions via scénarios ZigBee/HA).

**Pourquoi supprimer le télérupteur ?**  
Pour obtenir des **retours d’état fiables** (synchronisation app ↔ charge). Le télérupteur conserve un état **mécanique** non visible du ZBMINIL2 → désync app/automatisations.

**Où placer le module ?**  
Dans le **coffret électrique** (même circuit **10A**), pour une **alimentation stable** et un câblage propre.  
- **L in** : phase du disjoncteur 10A  
- **L1 out** : vers la lampe (l’autre borne lampe → **N**)  
- **S1/S2** : vers les poussoirs

**Appairage & réglage**  
- Maintenir bouton **5 s** (LED clignote) → appairer dans **Zigbee2MQTT/ZHA**  
- **3 appuis rapides** : bascule **mode poussoir**  
- Dans Zigbee2MQTT, **actualiser** pour voir le mode

  **Schéma poussoir sans télérupteur**
<img src="https://forumdomotique.com/uploads/monthly_2025_10/Zbminil2.png.83a8e32aed3a9a435fdc8e502a797313.png" alt="Schéma de design domotique" width="500" height="300">
  **Schéma poussoir avec télérupteur**
<img src="https://forumdomotique.com/uploads/monthly_2025_10/Zbminil2.png.83a8e32aed3a9a435fdc8e502a797313.png" alt="Schéma de design domotique" width="500" height="300">

---

## Mode va-et-vient : double commande

Contrôle depuis **deux points** (ex. couloir/escalier) **sans navettes** traditionnelles : les entrées **S1/S2** gèrent la logique.  
- Placer dans le **coffret** (circuit 10A)  
- **Retirer** un télérupteur éventuel  
- Connexions : **L in** (phase), **S1** + **S2** (interrupteurs/poussoirs reliés à L), **L1 out** (lampe → neutre)

> **Note navettes** : on ne les réutilise pas (souvent orange/violet dans boîtes de dérivation).

**Appairage & réglage**  
- 5 s bouton → appairage  
- 3 appuis rapides → (bascule mode)  
- Configurer en **Two-Way/Toggle** dans l’app

![Schéma va-et-vient](assets/schema-va-et-vient.png)

---

## Mode interrupteur simple

Cas le plus **simple** : un seul point de commande, interrupteur classique ou poussoir.  
- **L in** : phase  
- **S1** : vers l’interrupteur (autre borne → phase)  
- **L1 out** : vers la lampe (la lampe retourne au **neutre**)  
- **S2** : optionnel si 2ᵉ commande

![Schéma interrupteur simple](assets/schema-interrupteur-simple.png)

---

## Illustration pratique : pas à pas (inter simple)

> ⚠️ **Sécurité** : disjoncteur **coupé**, vérification **0 V** au multimètre.

1. **Déballage & préparation outils**  
   ![Outils](assets/etape-1-outils.png)
2. **Vérification absence de tension**  
   ![0 V](assets/etape-2-zero-volt.png)
3. **Identifier phase/neutre/terre**  
   ![Mesures](assets/etape-3-mesure-1.png) ![Mesures](assets/etape-3-mesure-2.png)
4. **Préparer la boîte d’encastrement**  
   ![Boîte](assets/etape-4-boite.png)
5. **Câbler l’interrupteur simple**  
   - Phase → **L in**  
   - Retour lampe → **L1 out**  
   - Interrupteur → **S1** (+ retour phase)  
   ![Câblage](assets/etape-5-cablage-1.png)
6. **Fixer l’interrupteur**  
   ![Fixation](assets/etape-6-fixation.png)
7. **Test & appairage Zigbee**  
   ![Appairage](assets/etape-7-appairage.png)
8. **Automatisation VMC (exemple)** – voir plus bas  
9. **Exposition Alexa**  
10. **Finitions**

---

## Exemples d’automatisations Home Assistant

### 1) Allumer la salle de bain après 20h si personne à la maison
```yaml
alias: "Allumer lumière salle de bain après 20h00"
description: "Allume la lumière de la salle de bain après 20h00 si personne n'est à la maison"
triggers:
  - entity_id: sensor.nombre_famille_home
    below: 1
    trigger: numeric_state
conditions:
  - condition: time
    after: "20:00:00"
actions:
  - delay: "00:00:20"
  - type: turn_on
    device_id: d7e7ae98a8d9853f428e9d2dc8b42f01
    entity_id: 101e84ae96ff6d7639f265c53e62988d
    domain: light
  - data:
      message: "Lumière de la salle de bain allumée après 20h00 !"
    target:
      entity_id:
        - notify.maman
        - notify.papa
        - notify.xavier
        - notify.naomie
        - notify.anais
    action: notify.send_message
mode: single
```

### 2) Couloir suit la salle de bain (va-et-vient logique)
```yaml
alias: "Couloir suit salle de bain"
description: "Allume le couloir quand la salle de bain s'allume"
mode: single
triggers:
  - type: turned_on
    device_id: d7e7ae98a8d9853f428e9d2dc8b42f01
    entity_id: 101e84ae96ff6d7639f265c53e62988d
    domain: light
    trigger: device
actions:
  - type: turn_on
    device_id: 248aaae85decfd16116b4ecd95448c3a
    entity_id: c04a0cfbcda745400a1ff67c120c2880
    domain: light
```

### 3) Éteindre la salle de bain si personne après 20 secondes
```yaml
alias: "Extinction auto salle de bain"
description: "Éteint la lumière après 20 secondes si personne à la maison"
triggers:
  - entity_id: sensor.nombre_famille_home
    below: 1
    trigger: numeric_state
actions:
  - delay: "00:00:20"
  - type: turn_off
    device_id: d7e7ae98a8d9853f428e9d2dc8b42f01
    entity_id: 101e84ae96ff6d7639f265c53e62988d
    domain: light
  - data:
      message: "Personne à la maison, extinction de la salle de bain."
    target:
      entity_id:
        - notify.maman
        - notify.papa
        - notify.xavier
        - notify.naomie
        - notify.anais
    action: notify.send_message
mode: single
```

---

## Avantages & limites

**Avantages**
- Installation **simplifiée**, surtout en remplacement de **télérupteur**
- **Retours d’état fiables** (sans télérupteur)
- **Plusieurs points de commande** via **S1/S2**
- **Sans neutre**, idéal pour moderniser l’existant

**Limites**
- Charge max recommandée : **6 A (~1380 W @230 V)**
- Besoin d’un **hub ZigBee**
- Risque de latence si le réseau ZigBee est saturé  
  (solution : ajouter des **routeurs ZigBee** sur secteur)

---

## Dépannage rapide

- **Pas de retour d’état** → télérupteur encore présent ? Retirez-le.  
- **Appairage impossible** → maintenir **5 s** pour reset, rapprocher du coordinateur.  
- **Inversions ON/OFF** → changer le **mode** (Edge/Toggle/Pulse).  
- **Coupures** → vérifier serrage des bornes, section des fils et disjoncteur.

---

## Annexes (images, schémas)

> Remplace les noms par tes fichiers réels dans `/assets/` du repo.

- `assets/design-sans-titre.png`  
- `assets/schema-poussoir-sans-telerupteur.png`  
- `assets/schema-poussoir-avec-telerupteur.png`  
- `assets/schema-va-et-vient.png`  
- `assets/schema-interrupteur-simple.png`  
- `assets/etape-1-outils.png`  
- `assets/etape-2-zero-volt.png`  
- `assets/etape-3-mesure-1.png`  
- `assets/etape-3-mesure-2.png`  
- `assets/etape-4-boite.png`  
- `assets/etape-5-cablage-1.png`  
- `assets/etape-6-fixation.png`  
- `assets/etape-7-appairage.png`

---

## Conclusion

Le **SONOFF ZBMINIL2** est un excellent choix **sans neutre** pour moderniser l’éclairage : **compact, fiable, Zigbee 3.0**, parfaitement **intégré à Home Assistant** via ZHA ou Zigbee2MQTT.  
Que ce soit en **poussoir**, **va-et-vient** ou **interrupteur simple**, il garantit des **retours d’état précis** et une **intégration fluide**.  

➡️ Une brique simple pour une maison **réactive, locale et sans cloud**.
