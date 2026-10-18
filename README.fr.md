# Lab d’administration Linux : utilisateurs, permissions, cron et systemd dans une VM Ubuntu

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md)

[![check](https://github.com/mitsurutech/lab-linux-admin/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-linux-admin/actions/workflows/check.yml)

Un **lab d’administration Linux** prêt à l’emploi pour la classe, issu de la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning), où les écoles et les établissements donnent à chaque élève sa propre machine Ubuntu et un terminal dans le navigateur. L’élève est l’administrateur d’un petit serveur et accomplit quatre tâches d’un administrateur système débutant : **ajouter un utilisateur et un groupe**, **définir les permissions d’un dossier partagé**, **planifier une tâche avec cron** et **écrire et activer un service systemd**. Rien n’est installé sur l’ordinateur de l’élève, et le lab est corrigé par un script : l’enseignant n’a donc pas à lire chaque machine.

## Les quatre tâches

| Tâche | Ce que fait l’élève |
| --- | --- |
| 1. Un utilisateur et un groupe | Créer un groupe ordinaire `devs` et un utilisateur ordinaire `maria` avec un dossier personnel et bash, membre de ce groupe et non administrateur |
| 2. Un dossier partagé | Faire de `/srv/projects` le dossier de l’équipe : les membres du groupe peuvent y lire, y écrire et y entrer, les fichiers qui y sont créés appartiennent automatiquement au groupe, et personne d’autre n’y a accès |
| 3. Une tâche planifiée | Exécuter le `backup.sh` du lab en tant que root tous les jours à 02:30 depuis un fichier de `/etc/cron.d`, écrit comme cron l’exige |
| 4. Un service | Écrire un fichier d’unité systemd pour le `heartbeat.sh` du lab, redémarré s’il échoue, et l’activer pour qu’il démarre à chaque amorçage |

Le lab dépose dans le dossier personnel de l’élève un README qui dit quoi faire et ne donne aucune réponse : ni commandes, ni modes, ni fichier d’unité. Il est volontairement petit, de sorte qu’il convient à une première leçon sur les **utilisateurs et les permissions, cron et systemd** et peut être étendu avec des disques, LVM ou un pare-feu en copiant le fichier.

## Comment il est corrigé

Le script `check` s’exécute en tant que root sur une copie de la machine enregistrée après un démarrage à neuf : il ne voit donc que ce qui a été **enregistré**, jamais une commande tapée puis perdue au redémarrage. Il passe en revue les quatre tâches dans l’ordre et s’arrête à la première qui est fausse, et sa première ligne dit laquelle et pourquoi, par exemple `NOT PASSED: task 3: the user field is /usr/local/bin/backup.sh, it must be root`. Il lit l’état réel : l’utilisateur et le groupe dans la base des comptes, le propriétaire et le mode du dossier, et un vrai fichier créé par le nouvel utilisateur et par quelqu’un d’extérieur au groupe ; le propriétaire, les permissions et la fin du fichier cron, ainsi que son unique ligne de tâche ; les directives `ExecStart`, `Restart` et `WantedBy` du fichier d’unité, et le lien que crée `systemctl enable`. Une crontab d’utilisateur, un dossier grand ouvert avec `chmod 777`, un service démarré mais jamais activé, et une copie du fichier d’unité à la place du lien échouent tous.

## Ce que contient le fichier

`linux-admin.lab.yaml` est une **définition de lab VM** complète, exactement ce que la plateforme exécute :

| Clé | Ce que c’est |
| --- | --- |
| `cpus`, `memory`, `disk` | la taille de la VM de chaque élève : 1 CPU, 1024 Mo et 10 Go |
| `open` | les ports qui s’ouvrent dans le navigateur : aucun, le lab est le terminal |
| `setup` | un script exécuté une seule fois, en tant que root, au premier démarrage de la VM de chaque élève : il installe `cron` depuis l’archive d’Ubuntu, crée les deux petits scripts et le dossier partagé, et écrit le README du lab |
| `check` | le script de correction décrit ci-dessus ; son code de sortie est le résultat et `0` signifie réussi |

## L’utiliser sur la plateforme

**Pour le donner comme travail à une classe :**

1. Dans votre module, ajoutez une tâche et choisissez la carte **Lab VM Ubuntu**.
2. Appuyez sur l’exemple, ou collez le contenu de `linux-admin.lab.yaml`. Utiliser un exemple remplace le texte, et Annuler rend le vôtre.
3. Publiez et essayez d’abord votre propre copie. Chaque élève reçoit une VM à lui, conservée d’une session à l’autre.
4. Distribuez-la comme n’importe quelle tâche. Lorsqu’elle est rendue, lancez le check sur la ligne d’un élève dans la liste de la classe : il démarre une copie de sa machine enregistrée, qui n’est pas sauvegardée en retour, et montre si elle a réussi et ce qu’elle a affiché.

La [page de ce lab](https://mitsurutech.co.uk/fr/help/lab-linux-admin) dans le manuel d’aide, son [guide des labs VM Ubuntu](https://mitsurutech.co.uk/fr/help/labs) et sa [page des labs d’exemple](https://mitsurutech.co.uk/fr/help/sample-labs) disent le reste. Pas encore de compte d’établissement ? [La première année est gratuite](https://mitsurutech.co.uk/fr/pricing) pour 50 élèves au plus, et [s’inscrire](https://mitsurutech.co.uk/fr/signup) ne demande qu’une adresse e-mail.

## Vérification du fichier

À chaque push, `.github/workflows/check.yml` vérifie que le fichier est du YAML valide avec les clés ci-dessus et que `setup` et `check` tiennent chacun dans la limite de 16 Ko de la plateforme. Il ne démarre pas de VM : cela se fait sur les machines de la plateforme.

## Ce qui ne marche pas dans un lab

La VM atteint l’archive de paquets d’Ubuntu, PyPI, npm et GitHub, et rien d’autre : on ne peut donc pas pointer un lab vers un hôte d’Internet. C’est une seule VM, il n’y a donc pas de seconde machine à administrer à travers un réseau.

## À propos

Réalisé par [Mitsuru Technologies](https://mitsurutech.co.uk/fr) pour la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning) : Python, Java, Go, Node, SQL et Linux dans le navigateur pour les écoles et les établissements, avec des labs de conteneurs, des labs VM, des applications web déployées depuis GitHub et des quiz qui se corrigent tout seuls. Essayez du code sans compte dans le [bac à sable](https://mitsurutech.co.uk/fr/playground).

D’autres labs du même endroit : [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) et [lab-sample](https://github.com/mitsurutech/lab-sample).

## Licence

Cette œuvre est sous licence [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Vous pouvez la partager et l’adapter pour apprendre et enseigner, à condition de citer Mitsuru Technologies Ltd, de ne pas l’utiliser commercialement et de partager tout ce que vous en tirez sous la même licence. Le texte complet est dans le fichier `LICENSE`.

`cron` est installé depuis l’archive d’Ubuntu au démarrage d’une VM ; rien de lui n’est distribué ici.
