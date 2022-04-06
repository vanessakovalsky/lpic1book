## Pré-requis techniques


afin de pouvoir faire les différentes manipulations nous allons utiliser des machines virtuelles.


## Step 1 : installation de la VM 
### Installation de VirtualBox
- Télécharger VirtualBox : https://www.virtualbox.org/wiki/Downloads
- Suivez les étapes d'installation :
- La première étape vous informe, cliquer sur Installer
![install_vb1](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/install_vb1.PNG)
- La seconde étape vous invite à choisir les outils à installer : laisser les options par défaut
![install_vb2](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/install_vb2.PNG)
- La troisième étape vous permet de créer des raccourcis pour accéder à VirtualBox
![install_vb3](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/install_vb3.PNG)
- La quatrième étape vous avertit que l'installation va temporairement déconnecter votre réseau
![install_vb4](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/install_vb4.PNG)
- La cinquième étape vous informe que l'installation est prête : cliquer sur Installer
![install_vb5](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/install_vb5.PNG)
- L'étape suivante installe le logiciel (attendre la fin)
![install_vb6](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/install_vb6.PNG)
- L'outil est installé, il propose de démarrer
![install_vb7](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/install_vb7.PNG)

### Création de la VM

- Une fois installé, créer une VM avec Ubuntu 20.04 
- L'interface de VirtualBox liste les VM :
![install_vb7](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/install_vb7.PNG)
- En cliquant sur +Ajouter, vous obtenez l'écran d'ajout des VM dans lequel vous pouvez choisir le nom de votre VM, l'endroit dans lequel elle va être stocké, votre système d'exploitation, ici Linux avec Ubuntu 64bit
![nouvelle_vm](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/nouvelle_vm.PNG)
- L'étape suivante permet d'allouer de la RAM à la machine, ici j'ai volontairaiment augmentée la RAM à 4Go
![vb_ram](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/vb_ram.PNG)
- L'étape suivante permet de choisir si l'on alloue un disque dur (pour persister des données): ici j'ai choisi d'ajouter un disque virtuel de 10Go
![vb_dd](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/vb_dd.PNG)
- Ensuite nous choisissons le type de stockage (s'il est dynamiquement alloué en fonction des besoins (permet d'éviter d'encombrer inutilement le disque dur de l'hôte) ou s'il est fixe (permet de réserver de l'espace disque sur le disque dur de l'hôte))
![vb_stockage_dd](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/vb_stockage_dd.PNG)
- Ensuite nous choisissons l'emplacement physique de notre disque virtuel et sa taille
![vb_emplacement_dd](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/vb_emplacement_dd.PNG)
- Puis nous pouvons créé notre VM :
![vb_vm_creee](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/vb_vm_creee.PNG)

### Installation de l'OS
- Télécharger l'ISO d'Ubuntu : https://ubuntu.com/download
- Dans l'inteface de gestion des VM, choisir votre VM et cliquer sur Configuration. Dans le panneau de configuration, cliquer sur Stockage
- En cliquant sur le lecteur CD, vous avez de la configuration qui apparait, cliquer à droit sur l'icone de CD puis choissir Depuis un fichier, et aller cherchez votre fichier ISO :
![vb_choix_iso](https://github.com/vanessakovalsky/docker-training/blob/master/tp/tp1/corrige/images/vb_choix_iso.PNG)
- Cliquer sur Ok
- Lancer votre VM
- Suivre les étapes d'installation d'Ubuntu
