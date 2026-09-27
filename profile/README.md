# Portage de la Nanostack (Wi-SUN) sur Zephyr RTOS

Travail de Bachelor de **Benoît Delay**. HEIG-VD, département TIC, filière Informatique et systèmes de communication, orientation Informatique embarquée, 2025-26. Travail encadré par le Prof. Pierre Favrat.

## Le projet

**Wi-SUN** est le standard de réseau maillé sans fil sub-GHz sécurisé utilisé pour les déploiements IoT à grande échelle (Smart Cities, compteurs intelligents, éclairage public). Sa pile open-source de référence, la **Nanostack** d'Arm, était liée à **Mbed OS**, abandonné en 2024.

Ce projet porte la Nanostack sous **Zephyr RTOS**, pour offrir une base Wi-SUN open-source sur un RTOS moderne et maintenu :

- **module Zephyr externe** : la Nanostack s'intègre à n'importe quelle application Zephyr ;
- **couche d'adaptation système** : timers, sections critiques, aléa et boucle d'événements réimplémentés avec les primitives Zephyr ;
- **ponts logiciels L2** (Ethernet et radio IEEE 802.15.4 sub-GHz) : ils relient la Nanostack aux pilotes Zephyr existants sans les modifier ;
- **sécurité** : authentification EAP-TLS et chiffrement de liaison via Mbed TLS.

## Dépôts

| Dépôt | Contenu |
|---|---|
| [**zephyr-nanostack**](https://github.com/Travail-de-Bachelor-Delay-Benoit/zephyr-nanostack) | Le module Zephyr : sources Nanostack, couche d'adaptation, ponts Ethernet et radio |
| [**applications**](https://github.com/Travail-de-Bachelor-Delay-Benoit/zephyr-first-step) | Applications de test : validation Ethernet, routeur de bordure Wi-SUN et nœud Wi-SUN |
| [**Rapport**](https://github.com/Travail-de-Bachelor-Delay-Benoit/Rapport) | Rapport du TB et affiche (Typst) |

## Résultats

| Scénario | Matériel | État |
|---|---|---|
| Pont Ethernet : SLAAC, ping6, serveur TCP | STM32F769I-DISCO | Validé, latence moyenne 0.68 ms |
| Pont radio : échange de trames TX/RX | 2 × TI LP-CC1352P7 | Validé |
| Réseau Wi-SUN complet (routeur de bordure + nœud) | 2 × TI LP-CC1352P7 | En cours de validation |

**Limitation connue** : le pilote radio Zephyr des TI CC13xx ne gère que le canal 0 (868.3 MHz) de la bande européenne. Le réseau fonctionne donc sur un canal fixe, sans saut de fréquence.

## Démarrage rapide

```sh
git clone https://github.com/Travail-de-Bachelor-Delay-Benoit/zephyr-nanostack.git
git clone https://github.com/Travail-de-Bachelor-Delay-Benoit/applications.git

cd applications
west build -b cc1352p7_lp app_border_router -d app_border_router/build
west flash -d app_border_router/build
```

Les instructions détaillées se trouvent dans le README de chaque dépôt.

## Perspectives

- Corriger le pilote IEEE 802.15.4 des CC13xx dans Zephyr, pour prendre en charge les 35 canaux européens et réactiver le saut de fréquence (FHSS).
- Intégrer la pile aux sockets BSD de Zephyr via le *socket offloading*.
- Faire des mesures de performance et de consommation sur un réseau maillé multi-sauts.
