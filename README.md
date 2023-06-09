# Projet_archi_distribuées ![icons8-architecture-32](https://user-images.githubusercontent.com/91553182/230304758-6aa48d7a-aa54-450c-9e4d-6a8a2dd910a0.png)

## Introduction du projet

Ce github contient notre projet d'*Architecture Distribuée* encadré par M. Lima. Nous avons décidé de travailler sur une corrélation entre la **disponibilité des vélib** et la **météo** par zone géographique à Lyon.

Dans ce Readme sera expliqué le cheminement à travers différents outils comme "[**Kafka**](https://kafka.apache.org/)", "[**Spark**](https://spark.apache.org/)" et "[**Zookeeper**](https://zookeeper.apache.org/))" pour arriver au produit final : un **Dashboard** disposant les données ainsi traitées de manière visuelle.


## Méthodologie

Il vous faudra dans un premier temps créer un container **Kafka**, **Zookeeper**, **Spark Master** et **Spark Worker** dans votre plateforme Docker. Pour cela, exécutez le docker-compose disponible dans ce répertoire via la commande :
```
docker-compose up -d
```

Vous pouvez vérifier la bonne création de vos container via la commande *docker ps* ou votre logiciel Docker Desktop.


### Kafka et Zookeeper

Créez votre **Topic** :
```
kafka-topics.sh --create --bootstrap-server localhost:9092 --topic 'mon_topic'
```

Ensuite accédez à **Kafka** :
```
docker exec -it 'container_kafka' bash
```

Une fois 2 invites de commandes **Kafka** ouverts, vous créez :

  - Un **Producer**, qui écrit les données. Il les envoie à un **Topic** ou "catégorie" dans lequel les messages sont stockés et publiés.
```
kafka-console-producer.sh --topic 'mon_topic' --bootstrap-server localhost:9092
```

  - Un **Consumer**, celui qui s’occupe de lire les données.
```
kafka-console-consumer.sh --topic 'mon_topic' --bootstrap-server localhost:9092
```

Quand vous écrivez des messages du côté **Producer**, ils s'affichent côté **Consumer**. 

Nous avons ensuite créé un script python (**producer.py**) pour automatiser cela. Gardez votre invite de commande qui contient le **consumer** ouvert et exéutez notre **producer.py**, le **consummer** reçoit l'API que nous avons récupérée.

### Spark-submit
Maintenant, nous allons soumettre un traitement via **Spark-submit**.

S'il vous manque des installations, vous devrez vous mettre en **root** :
```
docker exec -u root -it 'container_spark_master' bash
```
Et utilisez 'apt-get update', 'apt install nano', 'apt install python3', 'apt install  python3-pip', 'pip install requests', 'pip install kafka-python' pour ajouter ce qu'il vous manque potentiellement.

Ensuite quittez le **root** et relancer votre bash Spark :
```
docker exec -it 'container_spark_master' bash
```

Ensuite ajouter le fichier python de votre traitement à soumettre (**submit.py**) où vous le souhaitez et soumettez le à **Spark** :
```
spark-submit --packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.3.0 submit.py
```

Le traitement se lance et un tableau **clé-valeur** se présente finalement à vous.

Relancez un **producer** sur un nouvelle invite de commande en précisant que vous utilisez une clé :
```
kafka-console-producer.sh --bootstrap-server localhost:9092 --topic 'mon_topic' --property "parse.key=true" --property "key.separator=:"
```

Vous pourrez ainsi envoyer des messages adapté au format **clé-valeur**. La clé est séparée de la valeur par 2 points. Par exemple :
```
1:valeur1
```

Nous avons réussi à aller jusque là dans le projet, malheureusment nous ne parvenons pas à alimenter la base **MongoDB** avec nos données.


## Finalité du projet
Nous n'avons malheureusement pas pu terminer ce projet pour différentes raisons, nous allons tout de même détailler le Dashboard comme nous l'aurions conçu.

Pour une bonne visualisation, nous avons imaginé des graphiques "camemberts" de ce type :

![Dessin sans titre](https://user-images.githubusercontent.com/91553182/230186369-bc1eb2ac-fa3c-4f74-b53c-95c59cc7c93a.png)

Ou même aller plus loin en créant une carte intéractive !
