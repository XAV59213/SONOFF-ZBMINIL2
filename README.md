# SONOFF-ZBMINIL2

<img src="https://cdn.forumdomotique.com/monthly_2025_10/Designsanstitre.png.373e664afc5de5ad42117276219f44ef.png" alt="Schéma de design domotique" width="500" height="300">

## Installer et configurer le SONOFF ZBMINIL2 ZigBee sur Home Assistant : guide complet (poussoir, va-et-vient, interrupteur simple)

Dans le monde de la domotique, les modules ZigBee comme le SONOFF ZBMINIL2 offrent une flexibilité incroyable pour moderniser vos interrupteurs existants sans gros travaux. Ce petit module compact se glisse derrière un interrupteur mural standard ou directement dans votre tableau électrique et transforme n'importe quel bouton en commande intelligente : allumage ou extinction à distance, simulation de présence, ou intégration dans des scénarios complexes comme l’activation d’une lumière au lever du soleil.

Si vous utilisez Home Assistant comme hub pour piloter votre maison connectée, intégrer le SONOFF ZBMINIL2 est un jeu d’enfant grâce aux protocoles ZigBee 3.0 pris en charge par ZHA ou Zigbee2MQTT.

Pourquoi le choisir ? Il est abordable (autour de 10 à 20 €), fiable (alimentation secteur stable) et compatible avec la plupart des hubs ZigBee.

Surtout, le SONOFF ZBMINIL2 fonctionne sans neutre, ce qui le rend parfait pour moderniser les interrupteurs existants sans travaux de câblage complexes. Compact et universel, il s’intègre aussi bien derrière un interrupteur mural que dans un coffret électrique.

Dans ce guide, nous allons tout voir : du branchement physique à la configuration avancée sous Home Assistant, avec des conseils concrets pour éviter les erreurs de câblage et des exemples d’automatisations.

Ce tutoriel s’adresse aussi bien aux débutants qu’aux utilisateurs expérimentés.

J’ai testé ce module sur Home Assistant OS 2025.9.4 (installé sur Raspberry Pi 5) avec la clé SONOFF ZigBee 3.0 USB Dongle Plus, en utilisant Zigbee2MQTT.

## Plan de l'article

- Pourquoi utiliser le SONOFF ZBMINIL2 avec Home Assistant et quelles sont ses spécifications ?
- Matériel nécessaire pour l'installation.
- Étapes préliminaires : identifier phase et neutre dans votre installation
- Mode poussoir : la fonction pour remplacer un télérupteur par un SONOFF ZBMINIL2
- Mode va-et-vient : la fonction du SONOFF ZBMINIL2 pour moderniser un éclairage double interrupteur
- Mode interrupteur simple : la fonction du SONOFF ZBMINIL2 pour rendre un circuit d’éclairage intelligent
- Illustration pratique : installation pas à pas d’un interrupteur simple avec le SONOFF ZBMINIL2

**Prêt à booster votre setup domotique ? Allons-y !**

---

## Pourquoi utiliser le SONOFF ZBMINIL2 avec Home Assistant et quelles sont ses spécifications ?

Le SONOFF ZBMINIL2 est un module relais ZigBee compact (dimensions : 39 x 32 x 17 mm) conçu pour s'intégrer derrière un interrupteur mural monophasé. Il supporte une charge jusqu'à 10A/230V, parfait pour commander des lumières, ventilateurs ou petits appareils. Contrairement au précédent modèle ZBMINI, le ZBMINIL2 ne nécessite pas de neutre pour fonctionner. Grâce à son relais sec intégré, il peut s’adapter à la majorité des installations domestiques, même celles dépourvues de neutre derrière les interrupteurs.

Pourquoi l'associer à Home Assistant ? Home Assistant excelle dans l'interopérabilité : ce module s'intègre nativement via ZHA (ZigBee Home Automation) ou Zigbee2MQTT, deux intégrations gratuites. Résultat ?

Vous contrôlez tout depuis un tableau de bord unifié, créez des automatisations (ex. : éteindre les lumières si personne dans la pièce), Dans nos tests, il a tenu une connexion stable sur 1 an, avec un temps de réponse < 200ms.

## Matériel nécessaire pour l’installation du SONOFF ZBMINIL2

Avant de plonger dans le branchement, assurez-vous d'avoir :

- Le module SONOFF ZBMINIL2 .
- Un hub ZigBee compatible : ConBee II, Sonoff ZigBee Bridge, ou SkyConnect (pour Home Assistant)
- Home Assistant installé (version 2024 ou + recommandée).
- Outils de base : tournevis isolé, multimètre pour vérifier les tensions le neutre et phase.
- Interrupteur mural existant : interrupteur simple ,va et vient ou télérupteur.
- Logiciel : Zigbee2MQTT ou ZHA activé dans Home Assistant avec l’intégration mqtt et module complémentaire mqttbroker

> **Attention : travaillez toujours sur circuit hors tension pour éviter les chocs électriques !**

Un guide parfait pour les débutants… et même les utilisateurs plus avancés.

## Étapes préliminaires : identifier phase et neutre dans votre installation

Pour mesurer la tension avec un multimètre et différencier la phase du neutre, voici les étapes et explications en français, de manière claire et concise :

### 1. Mesurer la tension avec un multimètre

#### Préparer le multimètre :

- Tournez le sélecteur du multimètre sur le mode tension alternative (AC), souvent indiqué par un symbole en forme de vague (~) ou "V~". Choisissez une plage adaptée (par exemple, 200 V ou 600 V pour une prise domestique en 230 V).
- Insérez la sonde rouge dans la borne marquée V/Ω et la sonde noire dans la borne COM.

#### Mesurer la tension :

- Insérez les sondes dans les bornes de la prise ou du circuit à tester (par exemple, une borne pour la phase et une pour le neutre dans une prise électrique).
- Lisez la valeur affichée sur l’écran du multimètre. Pour une prise domestique en France, vous devriez obtenir environ 230 V en courant alternatif.

#### Précautions de sécurité :

- Assurez-vous que les sondes sont bien isolées et en bon état.
- Ne touchez pas les parties métalliques des sondes pendant la mesure.
- Travaillez sur un circuit hors tension si possible, ou prenez des précautions si le circuit est sous tension.

### 2. Différencier la phase du neutre

Pour identifier la phase et le neutre dans une prise ou un circuit électrique :

#### Utiliser le multimètre en mode tension AC :

- Placez la sonde noire dans une borne connue comme la terre (souvent la broche centrale d’une prise ou un point de terre identifiable).
- Insérez la sonde rouge dans l’une des autres bornes de la prise (phase ou neutre).

#### Résultat :

- Si vous mesurez environ 230 V, la borne testée est la phase.
- Si vous mesurez environ 0 V (ou une valeur très faible), la borne testée est le neutre.

#### Alternative : Utiliser un tournevis testeur (option plus simple mais moins précise) :

- Un tournevis testeur s’allume lorsqu’il est en contact avec la phase. Il reste éteint sur le neutre.
- Cependant, un multimètre est plus fiable et sécurisé pour confirmer.

#### Si vous n’avez pas de point de terre :

- Mesurez la tension entre les deux bornes (phase et neutre) avec le multimètre. Vous obtiendrez environ 230 V, mais cela ne distingue pas directement la phase du neutre.
- Dans ce cas, utilisez un tournevis testeur pour identifier la phase, puis confirmez avec le multimètre si possible.

### 3. Conseils supplémentaires

Assurez-vous de respecter la norme électrique en vigueur en France, où les circuits comportent généralement une phase, identifiée par un fil marron ou noir, un neutre, de couleur bleue, et, dans certains cas, une terre, repérée par un fil vert-jaune. Travaillez avec la plus grande prudence sur les circuits électriques, même hors tension, et envisagez de faire appel à un électricien professionnel en cas de doute pour garantir la sécurité. Avant toute mesure, testez votre multimètre sur une source de tension connue afin de vérifier son bon fonctionnement et d’assurer des relevés fiables.

Si vous avez besoin d’un schéma ou d’une explication plus détaillée, faites-le-moi savoir !

## Guide pratique

Pour une application plus aisée sur les chantiers, Legrand met à disposition un guide pratique comportant les points clés de cette série de normes NF C 15-100 et ses dernières évolutions applicables depuis la publication de la révision du 23/08/2024

## Mode poussoir : la fonction pour remplacer un télérupteur par un SONOFF ZBMINIL2

La fonction poussoir du SONOFF ZBMINIL2 permet de contrôler une charge (comme une lampe ou un appareil < 6A) via un bouton momentané (poussoir). Une pression brève envoie une impulsion qui alterne l’état de la charge (ON/OFF). Cette configuration est idéale pour remplacer un circuit à télérupteur existant dans une installation domotique, permettant un contrôle via l’application Zigbee (eWeLink, Home Assistant, etc.) ou des assistants vocaux (Alexa, Google Home). Cependant, pour garantir un fonctionnement optimal, notamment les retours d’état (savoir si la lampe est allumée ou éteinte via l’app), il est nécessaire de modifier l’installation électrique existante, notamment en supprimant le télérupteur.

### Pourquoi supprimer le télérupteur ?

Dans une configuration classique, le télérupteur situé dans le tableau électrique maintient l’état de la charge, en alternant entre allumé et éteint à chaque impulsion des poussoirs. Bien qu’il soit techniquement possible de faire fonctionner le système en conservant le télérupteur et en connectant le ZBMINIL2, les retours d’état ne seront pas fiables, car le ZBMINIL2 ne peut pas détecter l’état mécanique du télérupteur qui contrôle l’alimentation de la charge. Cela provoque une désynchronisation dans l’application, par exemple en affichant la lampe comme éteinte alors qu’elle est allumée, rendant les automatisations et le contrôle à distance peu fiables. En supprimant le télérupteur, le ZBMINIL2 prend en charge la logique d’allumage et d’extinction ainsi que les retours d’état via le protocole Zigbee, garantissant une synchronisation parfaite entre l’application, les poussoirs et la charge.

### Où brancher le ZBMINIL2 ?

#### Installation du ZBMINIL2 dans le coffret électrique

Contrairement à une installation derrière un poussoir, comme dans un boîtier mural, il est recommandé de placer le ZBMINIL2 dans le coffret électrique, sur le même circuit 10A qui alimentait le télérupteur. Cette configuration garantit une alimentation stable directement depuis le disjoncteur, sans interférences causées par les impulsions des poussoirs, assurant ainsi une fiabilité électrique optimale. Les bornes S1 et S2 du ZBMINIL2 permettent de connecter plusieurs poussoirs, jusqu’à deux directement et davantage via le protocole Zigbee, facilitant le remplacement d’un circuit à télérupteur avec de multiples points de commande. Compact avec ses dimensions de 39 x 32 x 18 mm, le ZBMINIL2 s’intègre facilement dans un coffret électrique tout en respectant les normes de sécurité, notamment la protection contre les surcharges. Il est déconseillé de placer le ZBMINIL2 derrière un poussoir dans un boîtier mural lors du remplacement d’un télérupteur, car cela complique le câblage et limite l’accès à une alimentation stable, particulièrement dans des boîtiers étroits.

### Schéma de principe (maquette)

Voici une description textuelle du schéma pour une maquette de test ou une installation réelle :

#### Composants :

- SONOFF ZBMINIL2
- Bouton poussoir momentané (non maintenu)
- Lampe ou charge < 6A (ex. : LED 230V)
- Disjoncteur 10A dans le coffret électrique
- Fils électriques (phase, neutre si requis par la charge)
- Hub Zigbee (ex. : SONOFF ZBBridge, Conbee II)

#### Connexions :

- **Disjoncteur 10A** : Fournit la phase (L) au circuit, remplaçant l’alimentation qui allait au télérupteur.
- **Borne L in(ZBMINIL2)** : Connectée à la phase du disjoncteur 10A.
- **Borne S1 (ZBMINIL2)** : Connectée au fil du poussoir.
- **Borne L1 out (ZBMINIL2)** : Connectée à la lampe. L’autre borne de la lampe va au neutre (N) si nécessaire (le ZBMINIL2 n’a pas besoin de neutre pour fonctionner).
- **Borne S2 (optionnel)** : Connectée au fil du poussoir.

#### Schéma de principe avec le poussoir et sans télérupteur

#### Schéma de principe avec le poussoir et télérupteur (Attention pas de retour d’état)

### Étapes d’installation

1. **Couper l’alimentation** : Désactivez le disjoncteur général pour des raisons de sécurité.
2. **Retirer le télérupteur** : Dans le coffret électrique, localisez le télérupteur et débranchez ses fils (phase, poussoirs, sortie vers la charge). Notez les connexions pour faciliter le remplacement.
3. **Installer le ZBMINIL2** :
   - Connectez la phase du disjoncteur 10A à la borne L du ZBMINIL2.
   - Connectez les poussoirs existants aux bornes S1 et S2 .
   - Connectez la borne L1 out à la charge (lampe), et reliez la lampe au neutre si requis.
   - Connectez la borne L1 in phase.
   - **Fixation** : Placez le ZBMINIL2 dans le coffret (ou sur un rail DIN ou fixé sécuritairement).
4. **Appairage Zigbee** : Rétablissez l’alimentation. Maintenez le bouton du ZBMINIL2 pendant 5 secondes (LED clignote). Ajoutez le module à votre hub Zigbee via l’app (eWeLink, Zigbee2MQTT, etc.).
5. **Configuration** : Dans l’app, sélectionnez le mode "poussoir" (parfois appelé "Edge Mode" ou "Pulse Mode"). Testez en appuyant sur le poussoir : une pression doit alterner ON/OFF, avec l’état mis à jour dans l’app.

### Test et vérification

- Vérifiez que chaque appui sur le poussoir change l’état de la lampe.
- Confirmez que l’app affiche correctement l’état (ON/OFF), prouvant que les retours d’état fonctionnent.
- Testez avec une charge faible (< 6A) pour éviter tout risque de surcharge.
- Si plusieurs poussoirs sont connectés (via S1/S2), assurez-vous qu’ils contrôlent tous la même charge.

### Paramétrage du SONOFF ZBMINIL2

Pour passer en mode impulsion appuie 3 fois rapidement sur le bouton du SONOFF ZBMINIL2, il basculera automatiquement en mode poussoir.

Une petite actualisation sur zigbee2mqtt permettra d’actualiser sa fonction poussoir.

### Exemple avec ma maquette de test

Dans cette maquette j'ai choisi la fonction ou on a supprimer le télérupteur pour avoir toute les remonter d’information sur Zigbee2MQTT

### Exemple d’automatisation

Un scénario simple permet d’allumer la lampe d’une simple pression sur l’interrupteur, tandis qu’une double pression déclenche une scène domotique, telle que l’extinction de toutes les lumières via Home Assistant. Une automatisation peut également être configurée dans Home Assistant pour activer automatiquement la lampe après 20h, offrant une gestion intelligente de l’éclairage adaptée aux besoins horaires.

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
  - delay:
      hours: 0
      minutes: 0
      seconds: 20
      milliseconds: 0
  - type: turn_on
    device_id: d7e7ae98a8d9853f428e9d2dc8b42f01
    entity_id: 101e84ae96ff6d7639f265c53e62988d
    domain: light
  - data:
      message: lumière de la salle de bain allumée après 20h00 !
    target:
      entity_id:
        - notify.maman
        - notify.papa
        - notify.xavier
        - notify.naomie
        - notify.anais
    action: notify.send_message
mode: single
Avantages
L’installation est simplifiée grâce au remplacement du télérupteur sans nécessiter de câblage complexe, tout en intégrant des fonctionnalités domotiques. La suppression du télérupteur garantit des retours d’état fiables, permettant à l’application de refléter systématiquement l’état réel de la charge. La flexibilité est assurée par la possibilité de contrôler une même lampe à partir de plusieurs poussoirs via le réseau Zigbee.
Limites
La charge maximale supportée est de 6 A (~1380 W à 230 V), et il est recommandé d’installer un fusible pour les charges proches de cette limite afin d’assurer la sécurité. Un hub Zigbee est requis pour la configuration initiale et la gestion des automatisations. Une latence peut survenir si le réseau Zigbee est saturé ou si le hub est trop éloigné de l’appareil.
Conclusion
En remplaçant le télérupteur par le ZBMINIL2 dans le coffret électrique, vous modernisez votre installation tout en garantissant des retours d’état précis pour une domotique fiable. Ce setup est parfait pour les couloirs, escaliers ou toute zone avec plusieurs points de commande.
Mode va-et-vient : la fonction du SONOFF ZBMINIL2 pour moderniser un éclairage double interrupteur
La fonction va-et-vient du SONOFF ZBMINIL2 permet de contrôler une charge (comme une lampe ou un appareil < 6A) depuis deux points de commande différents, par exemple deux interrupteurs situés à chaque extrémité d’un couloir ou d’un escalier. Contrairement à un va-et-vient traditionnel qui nécessite un câblage complexe avec des fils "navettes", le ZBMINIL2 simplifie l’installation grâce à ses bornes S1 et S2, qui gèrent deux interrupteurs (ou poussoirs) via le protocole Zigbee. Cette configuration est idéale pour moderniser une installation existante tout en ajoutant des fonctionnalités domotiques, comme le contrôle via une application (eWeLink, Home Assistant) ou des assistants vocaux (Alexa, Google Home). Comme pour la fonction poussoir, il est recommandé de placer le ZBMINIL2 dans le coffret électrique sur le circuit 10A, en supprimant tout télérupteur existant pour garantir des retours d’état fiables.
Pourquoi connaître le branchement actuel de vos va-et-vient ?
Pour intégrer le SONOFF ZBMINIL2 dans une installation va-et-vient, il est essentiel de comprendre le câblage actuel de votre système électrique, surtout dans une maison de plain-pied où les câbles, y compris les fils navettes, convergent souvent dans des boîtes de dérivation. Par exemple, dans une maison de plain-pied, il est courant d’avoir des boîtes de dérivation près des interrupteurs ou dans le plafond, où tous les câbles (phase, neutre, navettes, et sortie vers la lampe) sont regroupés. Les fils navettes sont utilisés dans un va-et-vient traditionnel pour relier les deux interrupteurs et permettre le contrôle alterné de la lampe.
En quoi c’est important ?
Une identification précise des fils est essentielle pour reconnecter correctement le ZBMINIL2, en repérant les navettes reliant les interrupteurs, la phase et la connexion à la lampe. Une erreur d’identification peut provoquer un dysfonctionnement ou un court-circuit. Le ZBMINIL2 simplifie le câblage par rapport à un va-et-vient classique en éliminant les navettes, les bornes S1 et S2 remplaçant leur logique, ce qui ne nécessite que la phase et les connexions des interrupteurs ou poussoirs vers ces bornes. Si un télérupteur est présent dans le circuit, par exemple dans le coffret électrique, il doit être retiré, car il gère mécaniquement l’état de la charge, empêchant le ZBMINIL2 de fournir des retours d’état fiables, ce qui pourrait entraîner une désynchronisation entre l’application Zigbee et l’état réel de la lampe. En installant le ZBMINIL2 dans le coffret électrique et en supprimant le télérupteur, le contrôle est centralisé, garantissant une synchronisation parfaite avec l’application. Pour une maison de plain-pied avec des boîtes de dérivation contenant les navettes, il est crucial de localiser la boîte où arrivent la phase du disjoncteur 10A et la sortie vers la lampe. Installer le ZBMINIL2 dans le coffret électrique simplifie les connexions et évite de modifier les boîtes de dérivation, souvent encombrées ou difficiles d’accès.
En résumé, connaître le branchement actuel (phase, navettes, charge) permet de reconnecter le ZBMINIL2 correctement, d’éliminer les navettes inutiles, et de garantir une installation domotique fiable.
Schéma de principe (maquette)
Voici une description textuelle du schéma pour une maquette ou une installation réelle en mode va-et-vient, adaptée à une maison avec boîtes de dérivation :
Composants :

SONOFF ZBMINIL2
Deux interrupteurs classiques va-et-vient
Lampe ou charge < 6A (ex. : lampe LED 230V)
Disjoncteur 10A dans le coffret électrique
Fils électriques (phase, neutre si requis par la charge)
Hub Zigbee (ex. : SONOFF ZBBridge, Conbee II)

Connexions :

Disjoncteur 10A : Fournit la phase (L) au circuit, remplaçant l’alimentation d’un éventuel télérupteur.
Borne L in (ZBMINIL2) : Connectée à la phase du disjoncteur 10A.
Borne S1 (ZBMINIL2) : Connectée à un fil du premier interrupteur/poussoir. L’autre fil de cet interrupteur retourne à la phase (L).
Borne S2 (ZBMINIL2) : Connectée à un fil du second interrupteur/poussoir. L’autre fil de cet interrupteur retourne à la phase (L).
Borne L1 out (ZBMINIL2) : Connectée à la lampe. L’autre borne de la lampe va au neutre (N) si nécessaire (le ZBMINIL2 n’a pas besoin de neutre pour fonctionner).

Schéma de principe Va-et-Vient
Note sur les navettes
Les fils navettes présents dans les boîtes de dérivations sont souvent orange ou violet .
Étapes d’installation

Couper l’alimentation : Désactivez le disjoncteur général pour des raisons de sécurité.
Identifier le câblage actuel : Dans la boîte de dérivation ou le coffret, localisez la phase (venant du disjoncteur 10A), les fils navettes (reliant les deux interrupteurs), et la sortie vers la lampe. Utilisez un multimètre si nécessaire pour confirmer la phase.
Retirer un éventuel télérupteur : Si un télérupteur est présent dans le coffret, débranchez ses fils (phase, poussoirs, sortie vers la charge). Notez les connexions pour faciliter le remplacement.
Installer le ZBMINIL2 :

Connectez la phase du disjoncteur 10A à la borne L in du ZBMINIL2.
Connectez le premier interrupteur/poussoir à la borne S1 et à la phase (ignorez les navettes).
Connectez le second interrupteur/poussoir à la borne S2 et à la phase.
Connectez la borne L1 à la charge (lampe), et reliez la lampe au neutre si requis.
Fixation : Placez le ZBMINIL2 dans le coffret électrique (sur un rail DIN ou fixé sécuritairement).


Appairage Zigbee : Rétablissez l’alimentation. Maintenez le bouton du ZBMINIL2 pendant 5 secondes (LED clignote). Ajoutez le module à votre hub Zigbee via l’app (eWeLink, Zigbee2MQTT, etc.).
Configuration : Dans l’app, sélectionnez le mode "va-et-vient" (souvent appelé "Two-Way Control" ou "Toggle Mode"). Configurez les bornes S1 et S2 pour qu’une pression sur l’un ou l’autre interrupteur alterne l’état de la lampe (ON/OFF).

Test et vérification
Vérifiez que chaque interrupteur ou poussoir modifie correctement l’état de la lampe, en alternant entre allumé et éteint. Confirmez que l’application affiche précisément l’état de la lampe, prouvant ainsi le bon fonctionnement des retours d’état. Effectuez les tests avec une charge inférieure à 6 A pour éviter tout risque de surcharge. Assurez-vous que les deux interrupteurs contrôlent la même charge de manière synchronisée, garantissant une gestion cohérente et fiable.
Paramétrage du SONOFF ZBMINIL2
Pour passer en mode on et off appuie 3 fois rapidement sur le bouton du SONOFF ZBMINIL2, il basculera automatiquement en mode poussoir.
Une petite actualisation sur zigbee2mqtt permettra d’actualiser ça fonction poussoir.
Exemple avec ma maquette de test
Dans cette maquette j'ai choisi la fonction ou on a supprimer le télérupteur pour avoir toute les remonter d’information sur Zigbee2MQTT
Exemple d’automatisation
Automatisation de l'interrupteur du couloir avec l’interrupteur de la salle de bain
Cette automatisation active automatiquement l’éclairage du couloir dès que la lumière de la salle de bain est allumée. Configurée dans Home Assistant, elle se déclenche lorsque l’interrupteur de la salle de bain, reconnu comme une entité de type lampe, passe à l’état allumé, commandant ainsi l’activation de l’éclairage du couloir. Aucune condition supplémentaire, comme une restriction horaire, n’est appliquée, garantissant une exécution immédiate à chaque allumage de la lumière. Conçue pour s’exécuter une seule fois par activation, l’automatisation évite les déclenchements répétés tant que l’état de la lampe reste inchangé.
Une utilisation de l’éclairage qui permet d'améliorer le confort et simplifier l’usage.
yaml
