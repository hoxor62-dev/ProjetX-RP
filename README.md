Projet X Roleplay – LesPacifistesJobs
PRÉSENTATION TECHNIQUE • ARMA REFORGER 1.9+


1. Présentation générale
LesPacifistesJobs est un mod développé pour Arma Reforger, conçu pour proposer une expérience Roleplay complète et immersive.

Il intègre une architecture composée de nombreux systèmes indépendants : gestion des métiers, économie, inventaires, véhicules, propriétés, interactions, interfaces utilisateur et sauvegardes persistantes.

Ce projet a été pensé pour fonctionner sur un serveur dédié, avec une gestion centralisée des données et une architecture adaptée au multijoueur.
2. Une architecture complète
83
Scripts Core

Managers, données et handlers


79
Scripts UI

Menus et interfaces HUD


64
Actions

Interactions contextuelles


108
Layouts UI

Interfaces et menus graphiques



Le mod repose également sur 29 composants attachables permettant de connecter les différents systèmes aux entités du jeu.
3. Installation et déploiement
L'installation s'effectue en plusieurs étapes :
1. Déploiement du mod dans le dossier addons du serveur.
2. Déclaration du mod dans la configuration serveur.
3. Création de l'arborescence de persistance.
4. Configuration des administrateurs et éventuellement de la whitelist.
5. Intégration des composants dans Workbench.
6. Configuration des entités et des prefabs nécessaires.
7. Vérification du fonctionnement à travers les logs serveur.
4. Un système de sauvegarde persistant








reforger.armaplatform.com


La persistance constitue un élément central du projet.

Les données sont enregistrées au format JSON dans le répertoire de profil du serveur, dans le dossier :

LesPacifistesJobs/

Les systèmes concernés comprennent notamment :
- Comptes bancaires et économie.
- Métiers et données des joueurs.
- Inventaires et coffres.
- Véhicules et assurances.
- Garages virtuels.
- Entreprises et fiscalité.
- Groupes et gangs.
- Casier judiciaire et dossiers médicaux.
- Portes et propriétés.
- Conversations SMS.
- Marché et bourse.
Les fichiers sont créés automatiquement lors des premières sauvegardes, à condition que l'arborescence nécessaire existe et soit accessible en écriture.
5. Administration et sécurité
Le mod intègre plusieurs outils de contrôle serveur :
- Gestion des administrateurs via GUID Bohemia.
- Système de whitelist facultatif.
- Rechargement automatique de la whitelist toutes les 5 minutes.
- Protection contre la destruction accidentelle des données métier.
- Gestion des accès et des portes.
- Nettoyage des personnages fantômes.
- Protection des PNJ contre le nettoyage automatique.
La whitelist permet notamment de restreindre l'accès au serveur sans nécessiter son redémarrage lors des modifications de la liste.
6. Intégration dans Workbench








community.bistudio.com


Le système repose sur une intégration directe dans l'environnement Enfusion.

Les composants sont répartis selon leur utilisation :
Emplacement	Fonction
GameMode	Nettoyage anti-duplication
PlayerController	Gestion des systèmes joueur
Character	Attribution des métiers
PNJ	Identification et protection
Entités du monde	Boutiques, distributeurs, récoltes, portes
Véhicules	Persistance après redémarrage

Une entité particulière est également requise : ItemPreviewManagerEntity.

Elle permet d'afficher les aperçus 3D des objets dans les différents menus du mod.
7. Initialisation intelligente
L'initialisation complète du mod se déclenche lors de la connexion du premier joueur.
1. +2 secondes
   
   Resynchronisation des portes.
2. +3 secondes
   
   Verrouillage initial des portes possédées.
3. +5 secondes
   
   Nettoyage des objets blacklistés.
4. +7 secondes
   
   Restauration des coffres et lancement de leur sauvegarde automatique toutes les 60 secondes.
5. +9 secondes
   
   Démarrage de la production animale.
6. +11 secondes
   
   Initialisation des stations-service et de leurs jauges de carburant.
Cette organisation permet de répartir les opérations d'initialisation après la connexion du premier joueur.
8. Sauvegardes et maintenance
Le système de persistance est regroupé dans un seul répertoire, ce qui simplifie considérablement les opérations de sauvegarde.

Les administrateurs peuvent notamment :
- Effectuer des sauvegardes complètes au format TAR.GZ.
- Programmer des sauvegardes quotidiennes via Cron.
- Surveiller les temps d'écriture disque.
- Contrôler la taille des fichiers JSON.
- Restaurer les données après un incident serveur.
Une sauvegarde serveur à l'arrêt est recommandée afin d'éviter les fichiers incomplets pendant une écriture.
9. Vérification et diagnostic
Le mod dispose de messages de logs permettant de contrôler le démarrage de ses principaux systèmes.

Les administrateurs peuvent vérifier :
- Le chargement des administrateurs.
- L'initialisation de l'anti-duplication.
- La restauration des coffres.
- Le lancement des sauvegardes automatiques.
- L'enregistrement des casiers judiciaires.
- La disponibilité des identifiants joueurs.
Un chapitre de dépannage accompagne également l'installation pour identifier les problèmes les plus courants.

10. Conclusion








wallpapercat.com


LesPacifistesJobs représente une base technique complète pour un serveur Arma Reforger Roleplay.

Avec ses nombreux composants, ses systèmes de persistance, ses outils d'administration et son intégration dans Workbench, le mod regroupe les fondations nécessaires au fonctionnement d'un environnement RP personnalisé.

L'objectif est de proposer une infrastructure cohérente, évolutive et maintenable, capable de gérer les différents aspects d'un serveur Roleplay.

PROJET X ROLEPLAY • LESPACIFISTESJOBS • DOCUMENTATION TECHNIQUE
