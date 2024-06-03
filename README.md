# Liaison-SGBDR-ElasticSearch

## Partie 1: Installation et Configuration du SGBDR & peuplement des données

### 1. Choix de l'Environnement :

- **Dockers** : Pour l'installation de notre SGBDR, nous avons choisi d'utiliser Docker.

### 2. Installation du SGBDR :

- On crée un fichier `docker-compose.yml` qui contient les informations suivantes :

```yml
version: '3.1'

services:
  mysql:
    image: mysql:lastest
    container_name: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: db
      MYSQL_USER: user
      MYSQL_PASSWORD: user
    ports:
      - "3306:3306"
    volumes:
      - ./mysql:/var/lib/mysql
```

### 3. Création de données fictives :

- On crée un fichier `data.sql` qui contient les données suivantes :

```sql

CREATE DATABASE IF NOT EXISTS db;

USE db;

CREATE TABLE IF NOT EXISTS `users` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(100) NOT NULL,
  `email` varchar(100) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

INSERT INTO `users` (`name`, `email`) VALUES
('Mederreg Kheir', 'Kheir.eddine@gmail.com')

```

### 4. Validation :

- On exécute la commande suivante pour lancer notre SGBDR :

```bash
docker-compose up -d
```

- On exécute la commande suivante pour peupler notre base de données :

```bash
docker exec -i mysql mysql -uroot -proot db < data.sql
```

## Partie 2 : Synchronisation des données du SGBDR avec ElasticSearch :

### 1. Choix de l'Environnement :

- **Logstash** : Pour la synchronisation des données entre notre SGBDR et ElasticSearch, nous avons choisi d'utiliser Logstash.
- **ElasticSearch** : Pour le stockage des données, nous avons choisi d'utiliser ElasticSearch.
- **Kibana** : Pour la visualisation des données, nous avons choisi d'utiliser Kibana.

### 2. Installation de Logstash :

- On crée un fichier `logstash.conf` qui contient les informations suivantes :

```conf
input {
  jdbc {
    jdbc_connection_string => "jdbc:mysql://mysql:3306/db"
    jdbc_user
    jdbc_password
    jdbc_driver_library => "/usr/share/java/mysql-connector-java-8.0.23.jar"
    jdbc_driver_class => "com.mysql.cj.jdbc.Driver"
    statement => "SELECT * FROM users"
    }
}

output {
  elasticsearch {
    hosts => ["http://elasticsearch:9200"]
    index => "users"
  }
}
```

### 3. Configuration de pipeline de données :

- On exécute la commande suivante pour lancer notre pipeline de données :

```bash
docker run -it --rm --name logstash -v "$PWD":/config-dir logstash -f /config-dir/logstash.conf
```

### 4. Validation :

- On exécute la commande suivante pour lancer notre SGBDR :

```bash
docker-compose up -d
```

- On exécute la commande suivante pour lancer notre pipeline de données :

```bash
docker run -it --rm --name logstash -v "$PWD":/config-dir logstash -f /config-dir/logstash.conf
```
