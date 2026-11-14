Projet X Roleplay - Guide d'installation et de configuration

Installation complète du mod LesPacifistesJobs sur un serveur dédié Arma Reforger.

Ce document couvre l'installation serveur, la création de l'arborescence de persistance, les fichiers de configuration à créer à la main, le câblage des composants dans Workbench et la vérification post-installation.

Le fichier README.md du mod documente le contenu de gameplay (métiers, boutiques, prix). Ce guide-ci ne traite que de l'installation.

1. Prérequis

Un serveur dédié Arma Reforger en version 1.9 ou supérieure, un accès shell au serveur, et Arma Reforger Tools (Workbench) sur un poste de travail pour compiler et packager le mod.

Le mod ne dépend d'aucun autre mod Workshop. Il modifie SCR_BaseGameMode par héritage (modded class), il est donc compatible avec un scénario personnalisé tant qu'aucun autre mod ne surcharge les mêmes méthodes.

Volumétrie du mod, à titre indicatif : 83 fichiers dans scripts/Core, 29 dans scripts/Components, 79 dans scripts/UI, 64 dans scripts/Actions et 108 layouts dans Configs/UI.

2. Structure du mod
LesPacifistesJobs/
  Configs/
    Menus/
    UI/              108 layouts (menus, HUD)
  scripts/
    Actions/         64 actions contextuelles (interactions monde)
    Components/      29 composants attachables
    Core/            83 managers, données et handlers
    Data/            définitions statiques (catalogues, recettes)
    UI/              79 scripts de menus et de HUD
  README.md          documentation gameplay
  INSTALL.md         ce document
3. Installation du mod sur le serveur

Copier le dossier du mod dans le répertoire des addons du serveur, puis déclarer son GUID dans la liste des mods du config.json du serveur, comme pour n'importe quel mod Workshop ou local.

Si le mod est distribué en local (non publié sur le Workshop), il doit être présent dans le dossier addons du serveur et référencé dans le .gproj du projet serveur.

4. Création de l'arborescence de persistance

C'est l'étape la plus souvent oubliée. Le mod écrit 38 fichiers JSON dans le répertoire de profil du serveur, sous un dossier LesPacifistesJobs, plus trois sous-dossiers.

Le chemin $profile: correspond au répertoire passé au serveur via le paramètre -profile au lancement. Repérez-le d'abord :

bash
# Exemple : si le serveur est lance avec -profile /home/arma/profile
PROFILE_DIR=/home/arma/profile

Créez ensuite l'arborescence complète en une commande :

bash
mkdir -p "$PROFILE_DIR/LesPacifistesJobs/identities" \
         "$PROFILE_DIR/LesPacifistesJobs/sms" \
         "$PROFILE_DIR/LesPacifistesJobs/virtual_garage"

Les trois sous-dossiers sont obligatoires :

identities contient une fiche par joueur (identité RP, apparence). sms contient les conversations SMS entre joueurs. virtual_garage contient le garage virtuel découpé en shards, plus son _index.json.

Ajustez ensuite les droits pour que l'utilisateur qui fait tourner le serveur puisse écrire :

bash
chown -R arma:arma "$PROFILE_DIR/LesPacifistesJobs"
chmod -R 750 "$PROFILE_DIR/LesPacifistesJobs"

Remplacez arma:arma par l'utilisateur et le groupe réels de votre service.

Fichiers créés automatiquement

Les 36 fichiers JSON suivants sont créés tout seuls par leurs managers à la première sauvegarde. Vous n'avez rien à faire pour eux, mais le dossier parent doit exister et être accessible en écriture :

active_vehicles.json, bank_accounts.json, bans.json, bonpoint_ads.json, bonpoint_blacklist.json, bonpoint_chats.json, bonpoint_purchases.json, bonpoint_treasury.json, companies.json, company_tax.json, criminal_records.json, destroyed_plants.json, doors.json, face_change_cooldowns.json, fuel_stations.json, gangs.json, hud_prefs.json, license_plates.json, market.json, mayor_rewards.json, medical_index.json, pochon_bourse.json, pochon_sales.json, police_records.json, robbery_cooldowns.json, storages.json, vehicle_insurances.json, vehicle_rentals.json, virtual_garage.json.

S'y ajoutent les fichiers à nom variable : medical_patient_<UID>.json pour les dossiers médicaux, player_<GUID>.json pour les données de métier, et inventory_<UID>.json pour la sauvegarde d'inventaire.

5. Fichiers de configuration à créer à la main

Deux fichiers seulement demandent une intervention manuelle. Leur comportement en cas d'absence est volontairement différent, et il faut bien comprendre la nuance.

admins.json

Si ce fichier est absent, le mod le crée vide au premier démarrage, et personne n'est administrateur. Il faut donc le remplir avec les GUID Bohemia de vos admins.

Chemin : $PROFILE_DIR/LesPacifistesJobs/admins.json

json
{
  "count": 2,
  "uid_0": "REMPLACER_PAR_LE_GUID_DU_PREMIER_ADMIN",
  "uid_1": "REMPLACER_PAR_LE_GUID_DU_SECOND_ADMIN"
}

Le champ count doit correspondre exactement au nombre de clés uid_N présentes, et la numérotation doit être continue à partir de zéro. Si count vaut 2 mais que seul uid_0 existe, le second admin sera simplement ignoré sans erreur.

Pour récupérer le GUID d'un joueur, consultez les logs serveur au moment de sa connexion. Le mod loggue les identités sous cette forme :

INFO: Faction: player Pseudo (playerID = 2 | UUID = 6e126f87-7e75-4cd2-b416-df0aa524fc1c)

C'est la valeur UUID qu'il faut reporter dans admins.json.

whitelist.json

Comportement inverse et volontaire : si ce fichier est absent, tous les joueurs sont autorisés. C'est un garde-fou destiné à éviter de verrouiller tout le serveur à cause d'un fichier oublié ou mal formé.

Ne créez ce fichier que si vous voulez réellement passer le serveur en accès restreint.

Chemin : $PROFILE_DIR/LesPacifistesJobs/whitelist.json

json
{
  "uids": [
    "GUID_JOUEUR_1",
    "GUID_JOUEUR_2",
    "GUID_JOUEUR_3"
  ]
}

Un joueur absent de la liste reçoit un avertissement puis est expulsé après un délai de grâce de 60 secondes. La whitelist est rechargée automatiquement toutes les 5 minutes, ce qui permet à un bot Discord ou à un script externe de modifier le fichier à chaud sans redémarrer le serveur.

6. Câblage dans Workbench
Composant sur l'entité GameMode

Un seul composant doit être attaché manuellement à l'entité GameMode de votre scénario :

LP_GhostCleaner

Ce composant corrige la duplication de personnage apparue avec la version 1.9 du jeu. Sans lui, chaque joueur qui se reconnecte laisse une copie fantôme de son personnage dans le monde, avec tous ses objets.

Pour l'attacher : ouvrez le fichier .et de votre GameMode dans le Resource Browser, puis dans le panneau Object Properties, cliquez sur Add Component et cherchez LP_GhostCleaner. Sauvegardez avec Ctrl+S.

Les autres managers du mod sont des singletons qui s'initialisent seuls à la première sollicitation. Il n'y a rien à câbler pour eux.

Composants sur le PlayerController

Ces composants doivent être présents sur le prefab de PlayerController utilisé par votre scénario, typiquement DefaultPlayerControllerMP_ScenarioFramework :

JobPlayerComponent est le composant central, il porte la quasi-totalité des RPC du mod. LP_AlcoholPlayerComponent gère l'état d'alcoolémie. LP_CannabisPlayerComponent gère les effets du cannabis. LP_FaceChangeCooldownComponent gère le délai entre deux changements d'apparence. LP_VehicleInsuranceHudComponent et LP_VehicleRentalHudComponent affichent les HUD d'assurance et de location.

Composants sur les prefabs de personnage

JobCharacterJobComponent porte le métier du joueur et doit être sur le prefab de character joueur.

JobNPCComponent doit être sur tous vos prefabs de PNJ. Il sert de marqueur et protège notamment vos PNJ de la suppression par LP_GhostCleaner.

Composants sur les entités du monde

Ces composants se posent sur les entités concernées directement dans l'éditeur de monde : JobAtmComponent sur les distributeurs, JobGenericShopComponent sur les boutiques, JobHarvestComponent sur les zones de récolte, JobMiningSourceComponent sur les filons, JobMineralsBuyerComponent sur les acheteurs de minerai, JobDrugDealerComponent et JobDrugZoneComponent sur le circuit stupéfiants, JobPoliceDoorComponent et JobHospitalDoorComponent sur les portes à accès restreint, LP_DoorAccessComponent sur les portes achetables, LP_HuntingZoneComponent sur les zones de chasse, et SimpleMapIconComponent sur tout ce qui doit apparaître sur la carte.

LP_VehiclePersistComponent doit être présent sur chaque prefab de véhicule achetable, sans quoi le véhicule ne survivra pas à un redémarrage.

Entité requise dans le monde

Le mod a besoin d'une entité nommée exactement ItemPreviewManagerEntity placée dans le monde. Elle est utilisée par sept menus différents pour afficher l'aperçu 3D des objets : la boutique, le garage virtuel, la fiche RP, les deux menus Bon Point, et les deux menus de craft (bûcheron et épicerie).

Placez une entité ItemPreviewManagerEntity n'importe où sur la carte et renommez-la exactement ainsi, en respectant la casse. Sans elle, les aperçus d'objets resteront vides dans tous ces menus.

7. Configuration du serveur

Dans le config.json de votre serveur, le paramètre suivant mérite attention :

json
"slotReservationTimeout": 300

Il contrôle la durée pendant laquelle le personnage d'un joueur déconnecté reste réservé. La valeur maximale est 300 secondes. Une valeur élevée protège les joueurs contre les déconnexions accidentelles, mais laisse leur corps plus longtemps dans le monde.

8. Séquence de démarrage

Point important à comprendre pour le diagnostic : l'initialisation complète du mod ne se déclenche pas au démarrage du serveur, mais à la connexion du premier joueur.

Une fois ce premier joueur connecté, la séquence suivante se déroule :

À 2 secondes, resynchronisation des portes pour le joueur qui vient d'arriver. Cette étape se répète à chaque connexion.

À 3 secondes, verrouillage initial de toutes les portes possédées. Une seule fois par démarrage.

À 5 secondes, nettoyage des objets blacklistés laissés au sol. Une seule fois par démarrage.

À 7 secondes, restauration des objets manquants dans les coffres, puis démarrage de la sauvegarde automatique des coffres toutes les 60 secondes. Une seule fois par démarrage.

À 9 secondes, démarrage du cycle de production animale, qui fait apparaître des oeufs sur les poules et du lait sur les vaches toutes les 3 heures. Une seule fois par démarrage.

À 11 secondes, découverte des stations-service de la carte et initialisation de leurs jauges de carburant. Une seule fois par démarrage.

Conséquence pratique : un serveur vide reste dans un état non initialisé. Si vous testez une fonctionnalité juste après un redémarrage, laissez passer une quinzaine de secondes après votre connexion avant de conclure à un dysfonctionnement.

9. Vérification post-installation

Connectez-vous au serveur et surveillez les logs. Les lignes suivantes confirment que les briques principales ont démarré correctement.

Pour les administrateurs :

[JobAdminManager] N admin(s) charge(s)

Si vous lisez Aucun fichier admin trouve, votre admins.json est absent ou mal placé. Si vous lisez 0 admin(s) charge(s), le fichier existe mais son contenu n'a pas été interprété : vérifiez la cohérence entre count et les clés uid_N.

Pour l'anti-duplication :

[LP_GhostCleaner] Initialise - voie rapide OnPlayerSpawned active (premier scan a T+150ms)

Si cette ligne est absente, le composant n'est pas attaché au GameMode. Si vous lisez à la place un avertissement GetOnPlayerSpawned indisponible, l'API n'existe pas sous ce nom dans votre version du jeu et seul le mécanisme de secours sera actif.

Pour les coffres :

[StorageSaveManager] Charge N snapshots de coffres.
[StorageSaveManager] Restore boot : N coffres verifies, N item(s) restaure(s)
[StorageSaveManager] Auto-save demarree (60s).

Pour le casier judiciaire, après qu'un événement ait modifié un casier :

[JobCriminalManager] FlushSave : ecriture disque = XX ms (N records)

Pour les métiers, cette ligne est normale pendant les premières secondes d'une connexion et ne doit pas persister :

[JobManager] SAUVEGARDE ANNULEE pour playerId=X : UID backend indisponible

Elle indique que la garde anti-destruction fonctionne : le serveur refuse d'écrire tant qu'il n'a pas confirmé l'identité du joueur, ce qui préserve son métier.

10. Sauvegardes et maintenance

L'ensemble des données de jeu tient dans le dossier LesPacifistesJobs du profil. Une sauvegarde complète se résume donc à archiver ce dossier :

bash
tar czf "sauvegarde-projetx-$(date +%Y%m%d-%H%M).tar.gz" \
    -C "$PROFILE_DIR" LesPacifistesJobs

Pour une sauvegarde automatique quotidienne, ajoutez une tâche cron :

bash
0 5 * * * tar czf /home/arma/backups/projetx-$(date +\%Y\%m\%d).tar.gz -C /home/arma/profile LesPacifistesJobs

Faites cette sauvegarde serveur à l'arrêt, ou à défaut à un moment creux. Plusieurs fichiers sont écrits en continu, et une archive prise pendant une écriture peut contenir un JSON tronqué.

Surveillez la taille de criminal_records.json et de companies.json, qui sont les plus volumineux. Au-delà de quelques mégaoctets, les temps d'écriture deviennent visibles dans les logs via les avertissements FlushSave : ecriture disque = XX ms.

11. Dépannage

Aucun joueur n'a les droits admin. Vérifiez que admins.json est bien dans $PROFILE_DIR/LesPacifistesJobs/ et non à la racine du profil. Vérifiez que count correspond au nombre réel de clés. Vérifiez que les GUID sont ceux des logs de connexion et non les pseudos.

Tous les joueurs sont expulsés au bout de 60 secondes. Votre whitelist.json existe mais ne contient pas leurs GUID. Pour rouvrir le serveur en urgence, renommez ou supprimez simplement le fichier : son absence réautorise tout le monde.

Les joueurs apparaissent en double à la reconnexion. Le composant LP_GhostCleaner n'est pas attaché à l'entité GameMode. Vérifiez la présence de la ligne d'initialisation dans les logs.

Les aperçus d'objets sont vides dans les menus. L'entité ItemPreviewManagerEntity est absente du monde ou mal nommée. Le nom doit être exact, casse comprise.

Rien ne se passe après un redémarrage. L'initialisation attend la première connexion joueur. Connectez-vous et patientez une quinzaine de secondes.

Les objets disparaissent des coffres après un redémarrage. Vérifiez que le dossier de profil est accessible en écriture par l'utilisateur du service, et que storages.json est bien créé et mis à jour.

Un véhicule acheté disparaît au redémarrage. Le composant LP_VehiclePersistComponent manque sur le prefab de ce véhicule.

12. Récapitulatif de la check-list

Dossier du mod déployé dans les addons du serveur et déclaré dans le .gproj.

Arborescence LesPacifistesJobs créée dans le profil, avec les trois sous-dossiers identities, sms et virtual_garage, et les droits en écriture positionnés.

Fichier admins.json créé et rempli avec les GUID des administrateurs.

Fichier whitelist.json créé uniquement si vous voulez un serveur restreint.

Composant LP_GhostCleaner attaché à l'entité GameMode.

Composants joueur présents sur le PlayerController et sur le prefab de character.

Composant JobNPCComponent présent sur tous les prefabs de PNJ.

Composant LP_VehiclePersistComponent présent sur tous les prefabs de véhicule achetable.

Entité ItemPreviewManagerEntity placée dans le monde avec le nom exact.

Paramètre slotReservationTimeout réglé dans le config.json du serveur.

Sauvegarde automatique du dossier de profil programmée.

Logs vérifiés après la première connexion.
