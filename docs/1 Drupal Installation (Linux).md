# 1 Drupal Installation

Download: [Drupal v8.6.10](https://www.drupal.org/project/drupal/releases/8.6.10).

## Voraussetzungen

* PHP Version 5.5.9 oder höher (PHP 7.2 oder höher wird empfohlen): `sudo apt-get install php7.0` (php7.2)
* Eines der Folgenden Datenbank-Systeme
  * MariaDB:
    * `apt` Repository Setup: `curl -sS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash`
    * MariaDB Setup: `sudo apt-get update && sudo apt-get install mariadb-server mariadb-client`
  * MySQL: `sudo apt-get install mysql-server mysql-client`
  * SQLite: `sudo apt-get install sqlite3`
    * **Hinweis** Eine installation von SQLite ist nicht notwendig um es in PHP zu benutzen, kann aber hilfreich sein, um die Datenbank manuell zu bearbeiten.
* Composer
  * Installations-Datei: [installer](https://getcomposer.org/installer)
  * Nach dem Runterladen
    * `php ./installer`
    * `mv composer.phar /usr/local/bin/composer`

## Oder: via Docker

Docker ist ein Container Service (ähnlich wie Virtuelle Maschinen) mit vorkonfigurierten Images für Drupal, MySQL, MariaDB und so weiter. Container beeinflussen das System nicht, sollange sie nicht laufen und lassen das Datei-System unberührt. Container können einfach gelöscht und gebupdatet werden.

Docker für Linux wird wie mit dem Folgenden Script installiert:

```console
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
...
```

  <!-- * `apt-get` voraussetzung zur Nutzung von HTTPS: `sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common`
  * Dockers offizieller GPG-Schlüssel: `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
  * Docker `apt` Repository: `sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`
  * Docker Installation: `sudo apt-get update && sudo apt-get install docker-ce docker-ce-cli containerd.io`
  * *Als Script:*

    ```sh
    sudo apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg-agent \
        software-properties-common && \
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && \
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" && \
    sudo apt-get update && sudo apt-get install docker-ce docker-ce-cli containerd.io
    ``` -->

## Drupal Setup

Nach der installation der Voraussetzungen muss sichergestellt werden, dass der Service/Daemon des ausgesuchten Datenbank-Systems läuft (mit der Ausnahme von SQLite, welches Dateibasiert ist).

> **Hinweis**
> <br>
> In allen Code-Schnipseln wird das Drupal Root-Verzeichnis als `$DRUPAL_ROOT` bezeichnet. Das kann durch das eigene Verzeichnis ersetzt werden, oder man fügt es dem System als Umgebungsvariable hinzu.

### Schritt 1: Aufsetzen des Dateisystems

#### Via Download

Nun kann der Inhalt von `drupal-8.6.10.zip` (bzw. `drupal-8.6.10.tar.gz`) in ein beliebiges Verzeichnis entpackt werden. Dieses Verzeichnis dient als das Drupal Root-Verzeichnis (`$DRUPAL_ROOT`). Alle relativen Pfade gehen von diesem Verzeichnis aus.

In dem Verzeichnis befinden sich folgende Unter-Verzeichnisse:

* `modules`: Module die die Funktion von Drupal erweitern.
* `themes`: Designs die das Aussehen von Drupal verändern.
* `sites`: Beinhaltet die Konfiguration von Drupal, sowie Öffentliche zugängliche und Private Dateien.
  * Im falle einer einfachen Seite befinden sich alle weiteren Dateien und Verzeichnisse im Unter-Verzeichnis `default`
  * Im falle eines Multi-Site Setups enthält dieses Verzeichnis weitere Verzeichnisse für jede Sub-Seite (z.B.: `sites/site1`, `sites/site2`, etc.)
* `core` und `vendors`: Systemdaten von Drupal. Diese sollten nicht verändert werden.

#### Via Composer

Composer kann eine Drupal Installation automatisch herunterladen: `composer create-project drupal-composer/drupal-project:8.x-dev <Installations-Verzeichnis>`

```console
$ mkdir -p "$DRUPAL_ROOT"
$ composer create-project drupal-composer/drupal-project:8.x-dev "$DRUPAL_ROOT" --no-interaction
Installing drupal-composer/drupal-project (8.x-dev 4229acea4bb6181f421e8dd0e72a02ccdc98df1d)
...
```

> **Hinweis**
> <br>
> Wenn die Installation durch Composer durchgeführt wurde, befinden sich alle relevanten Verzeichnisse im Unter-Verzeichnis `web` <br>
> Nur `composer` und `drush` verwenden dann das Verzeichnis `$DRUPAL_ROOT`, alle anderen (z.B.: `php`) gehen von `$DRUPAL_ROOT/web` aus.

### Optional: Drush Installation

Drush (**Dru**pal **Sh**ell) ist ein Konsolen-Anwendung, die die Bedienung von Drupal erleichtert. Die Installation geschieht durch Composer im Drupal Root-Verzeichnis:

```console
$ composer require drush/drush
Using version ^9.5 for drush/drush
...
```

### Schritt 2: Starten des Drupal Servers

#### Via PHP

PHP hat einen eingebauten webserver. Dafür muss ein Terminal in `DRUPAL_ROOT` geöffnet werden, dann wird der Server mit dem Befehl `php -S localhost:8000` gestartet.
<!-- Für lokale Zwecke sollte `Adresse` mit `localhost` `127.0.0.1` ersetzt werden. `Port` ist eine beliebige Zahl zwischen `1` und `65535`, Webseiten sind für gewöhnlich über den Port `80` zu erreichen (im falle von `http`; `443` für `https`). Ist der Port `80` nicht verwendbar, kann ein Port über `1024` versucht werden. Zum lokalen testen von Webseiten wird meist ein vierstelliger Port mit `8` am Anfang gewählt (z.B. `8000` oder `8080`). -->

##### Beispiel: PHP Server

```console
$ cd "$DRUPAL_ROOT"
$ php -S localhost:8000
PHP 7.2.2 Development Server started at ...
Listening on http://localhost:8000
Document root is ...
Press Ctrl-C to quit.
```

#### Via Docker

* Starte den Datenbank Server:

  ```sh
  docker run --name 'drupal_datenbank' -e MYSQL_ROOT_PASSWORD='passwort' -e MYSQL_DATABASE='drupal' -d mariadb
  ```

* Starte die Webseite:

  ```sh
  docker run --name 'drupal_webseite' --link 'drupal_datenbank:datenbank' -v "$DRUPAL_ROOT:/var/www/html" -p '8000:80' -d drupal
  ```

  Wobei in diesem Fall `$DRUPAL_ROOT` der **absolute Pfad** zum Verzeichnis sein muss.