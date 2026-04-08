# Controle_TP_FPGA

Architecture du système
L’architecture est composée de plusieurs blocs matériels :

    1. Module capteurs_sol
Ce module réalise l’acquisition des capteurs analogiques via l’ADC LTC2308.

Fonctions principales :
génération du signal CONVST pour lancer une conversion ADC
communication SPI avec l’ADC
lecture séquentielle des 7 canaux analogiques
stockage des résultats de conversion
génération du signal data_ready lorsque toutes les données sont disponibles
Les données sont retournées sur les signaux : 


2. Interface SPI avec l’ADC

Le module communique avec le LTC2308 via les signaux :

ADC_CONVST
ADC_SCK
ADC_SDI
ADC_SDO

Le fonctionnement est le suivant :

data_capture déclenche une nouvelle acquisition
le module active ADC_CONVST pour démarrer la conversion
un temps d’attente est respecté (CONVST_WAIT_CLOCK_NUM)
les données sont lues via l’interface SPI
les valeurs sont stockées dans les registres internes

Une fois les 7 canaux acquis :

data_ready = 1
3. Machine d’états (FSM)

Le module fonctionne grâce à une machine d’états :

Etat	Fonction
S0	attente du signal data_capture
S1	déclenchement de la conversion ADC
S2	temporisation pour la conversion
S3	lecture SPI des données
S4	passage au canal suivant
S5	données disponibles (data_ready)
4. Module capteurs_sol_seuil

Ce module compare les valeurs des capteurs avec un seuil programmable :

NIVEAU

Il génère alors un vecteur logique :

vect_capt

Chaque bit indique si la valeur du capteur dépasse le seuil.

5. Processeur Nios II

Le processeur Nios II permet de :

lire les données des capteurs
récupérer le vecteur vect_capt
écrire les valeurs des moteurs
gérer la logique de contrôle du robot

La communication se fait via le bus Avalon.

6. Génération des moteurs (PWM)

Le module PWM_generation génère les signaux de commande pour les moteurs :

dc_motor_p_R
dc_motor_n_R
dc_motor_p_L
dc_motor_n_L

Ces signaux pilotent les moteurs du robot.

Horloges

La carte DE0-Nano fournit une horloge de :

50 MHz

Une PLL est utilisée pour générer :

40 MHz → horloge du module capteurs
2 kHz  → signal de déclenchement data_capture
