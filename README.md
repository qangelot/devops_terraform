# devops_terraform

Hands on Terraform exercises

![logo](https://assets.intersystems.com/e4/e5/c7728ffb4f60964a6e7d089905f0/azure-logo-large.jpg)

![logo](https://foghornconsulting.com/wp-content/uploads/2020/07/Terraform-Logo-1.png)


## Objectifs

* Créer une machine virtuelle Azure (VM) avec une adresse IP publique via Terraform
* Se connecter à la VM avec SSH
* Comprendre les différents services Azure (ACI vs. AVM) 

## Contraintes

* Location : france central
* Azure VM name: devops-<identifiant-efrei>
* VM size : Standard_D2s_v3
* Utiliser Azure CLI pour l'authentification
* User administrateur de la VM : devops
* Créer une clef SSH avec Terraform
* OS : Ubuntu 16.04-LTS

## Terraform et son intérêt

Terraform est un outil d'infrastructure en tant que code qui vous permet de définir des ressources dans des fichiers de configuration lisibles par l'homme, que vous pouvez modifier, réutiliser et partager. Vous pouvez ensuite utiliser un flux de travail cohérent pour approvisionner et gérer toute votre infrastructure tout au long de son cycle de vie. Tout cela est automatisé et limite largement les erreurs.

Terraform peut gérer des composants de bas niveau tels que les ressources de calcul, de stockage et de réseau, ainsi que les composants de haut niveau comme les entrées DNS et les fonctionnalités SaaS.

## Une architecture modulaire

Une bonne pratique consiste à découper une configuration Terraform afin de faciliter sa compréhension et sa maintenabilité. 

* Resources : Les ressources sont l'élément le plus important du langage Terraform. Chaque bloc de ressources décrit un ou plusieurs objets d'infrastructure, tels que des réseaux virtuels, des instances de calcul, ou des composants de plus haut niveau tels que des enregistrements DNS.

* Providers : Terraform s'appuie sur des plugins appelés " providers " pour interagir avec les fournisseurs de cloud, les fournisseurs SaaS et d'autres API. Les configurations Terraform doivent déclarer les fournisseurs dont elles ont besoin afin que Terraform puisse les installer et les utiliser. 

* Data : Les sources de données permettent à Terraform d'utiliser des informations définies en dehors de Terraform, définies par une autre configuration Terraform distincte, ou modifiées par des fonctions.

* Variables : Les variables d'entrée servent de paramètres pour un module Terraform, de sorte que les utilisateurs peuvent personnaliser le comportement sans modifier la source. Les valeurs de sortie sont comme des valeurs de retour pour un module Terraform.


## Les étapes du déploiement

```python
terraform init
```
La commande terraform init est utilisée pour initialiser un répertoire de travail contenant les fichiers de configuration de Terraform.

```python
terraform plan
```
La commande terraform plan crée un plan d'exécution, qui vous permet de prévisualiser les changements que Terraform prévoit d'apporter à votre infrastructure. 

```python
terraform apply
```
La commande terraform apply exécute les actions proposées dans un plan Terraform.

```python
terraform destroy
```

Enfin, il est important de détruire les ressources afin d'éviter des facturations excessives. La commande terraform destroy est un moyen pratique de détruire tous les objets distants gérés par une configuration Terraform particulière.

## Se connecter à la VM avec SSH

Pour cela, nous générons un couple clé publique/privée via Terraform et à chaque nouvelle exécution. La commande terraform output nous permet de stocker la clé privé ainsi générée et de l'utiliser par la suite pour se connecter à la VM Azure en SSH.

```python
terraform output --raw private_key > id_rsa
chmod 600 id_rsa
```


## Différence VM vs ACI

Les conteneurs et les machines virtuelles contribuent à améliorer l'utilisation des ressources informatiques, chacun a ses avantages et ses inconvénients. 

Les machines virtuelles existent depuis des décennies, permettant aux entreprises de combiner plusieurs serveurs exécutant différentes applications sur un seul serveur physique, même s'ils exécutent des systèmes d'exploitation différents. Cela a permis des économies substantielles sur le matériel et les logiciels de serveur, car ce qui fonctionnait auparavant sur plusieurs serveurs fonctionne désormais sur un seul serveur. Les machines virtuelles sont également à la base de la plupart des services cloud. AWS, Azure et d'autres clouds publics utilisent la machine virtuelle comme l'une de leurs offres standard. Cependant, comme ils encapsulent un serveur entier dans chaque machine virtuelle, la quantité de CPU et de RAM requise par les machines virtuelles peut devenir lourde et limiter le nombre de machines virtuelles pouvant exister sur un seul serveur.

Les conteneurs ont rapidement gagné en popularité depuis la sortie de Docker en 2013, en partie en réponse à la quantité de surcharge consommée par les machines virtuelles. Étant donné que les conteneurs fonctionnent sur le système d'exploitation d'un serveur, ils partagent cette seule instance de système d'exploitation et d'autres binaires et bibliothèques, de sorte que les conteneurs n'ont besoin d'inclure que le code d'application, que ce soit sous la forme d'une seule application monolithique ou de microservices regroupés dans un ou plusieurs conteneurs pour englober une fonction commerciale.

Ainsi, alors que les machines virtuelles permettent à une organisation d'exécuter plusieurs serveurs virtuels sur un seul matériel, quels que soient leurs systèmes d'exploitation, les conteneurs offrent une virtualisation d'applications légère et haute densité , la possibilité de faire tourner les applications et les instances de haut en bas en quelques secondes, et dans une certaine mesure de sécurité inhérente à la séparation des applications dans leurs propres conteneurs.

### Avantages de la VM :

Des décennies d'expertise en virtualisation permettent d'accéder à un ensemble robuste d'outils de gestion et de sécurité des machines virtuelles
Les machines virtuelles offrent la possibilité d'exécuter plusieurs applications nécessitant différents systèmes d'exploitation sur une seule infrastructure
Les machines virtuelles émulent un environnement informatique complet, y compris toutes les ressources du système d'exploitation
Les machines virtuelles simplifient la portabilité et la migration entre les plateformes sur site et les plateformes basées sur le cloud
Il existe un vaste écosystème et un marché de VM bien établis avec des leaders de l'industrie tels que VMware

### Inconvénients de la machine virtuelle :

Les images de machine virtuelle consomment généralement des gigaoctets et prennent donc plus de temps à sauvegarder ou à migrer entre les plates-formes
Parce qu'ils encapsulent l'intégralité du serveur, y compris le système d'exploitation, un serveur physique peut prendre en charge moins de machines virtuelles que les conteneurs
Le temps de démarrage de la machine virtuelle peut prendre quelques minutes

### Avantages du conteneur :

Les conteneurs sont plus légers que les machines virtuelles, car leurs images sont mesurées en mégaoctets plutôt qu'en gigaoctets
Les conteneurs nécessitent moins de ressources informatiques pour être déployés, exécutés et gérés
Les conteneurs tournent en quelques millisecondes
Comme leur ordre de grandeur est plus petit
Un seul système peut héberger beaucoup plus de conteneurs que les VM
 
### Inconvénients du conteneur :

Tous les conteneurs doivent fonctionner sur le même système d'exploitation - pas de mélange et de correspondance des systèmes d'exploitation ou des versions
Les conteneurs peuvent être moins sécurisés que les VM puisque le système d'exploitation sous-jacent est partagé
Les conteneurs sont une technologie plus récente et l'écosystème continue d'évoluer


## Comparatif financier

 <b> Pour un comparatif financier détaillé, se référer au document [compare_costs](compare_costs.pdf) </b>

En conclusion, à hardware égal, l’ACI est plus cher que Azure Virtual Machine. Cependant, il est bien plus léger à configurer et à maintenir.


## Automatisez Terraform avec les actions GitHub

Les actions GitHub ajoutent une intégration continue aux référentiels GitHub pour automatiser les builds, les tests et les déploiements de vos logiciels. L'automatisation de Terraform avec CI/CD applique les meilleures pratiques de configuration, favorise la collaboration et automatise le flux de travail Terraform.

L'action GitHub "Setup Terraform" de HashiCorp installe et configure la CLI Terraform dans votre flux de travail Github Actions. Cela permet à la plupart des commandes Terraform de fonctionner exactement comme sur votre ligne de commande locale.



 
