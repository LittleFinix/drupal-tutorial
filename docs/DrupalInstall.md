# Drupal 8.6.10 Installation

Download: [Docker v8.6.10](https://www.drupal.org/project/drupal/releases/8.6.10)

## Voraussetzungen

### Windows

* PHP Version 5.5.9 oder höher (PHP 7.2 oder höher wird empfohlen).
  * [Microsoft Web Platform](https://www.microsoft.com/web/downloads/platform.aspx), oder
  * via Chocolatey: `choco install -y php --version 7.3.1`
* Datenbank-System
  * [MariaDB](https://downloads.mariadb.org/interstitial/mariadb-10.3.13/winx64-packages/mariadb-10.3.13-winx64.msi/from/http%3A//mirror.netcologne.de/mariadb/),
    * via Chocolatey: `choco install -y mariadb --version 10.3.13`
  * [MySQL](https://dev.mysql.com/downloads/file/?id=484919), oder
    * via Chocolatey: `choco install -y mysql --version 8.0.15`
  * [SQLite](https://sqlite.org/download.html)
    * via Chocolatey: `choco install -y sqlite --version 3.27.1`
    * **Hinweis** Eine installation von SQLite ist nicht notwendig um es in PHP zu benutzen, kann aber hilfreich sein, um die Datenbank manuell zu bearbeiten.
* [Composer](https://getcomposer.org/Composer-Setup.exe)
  * via Chocolatey: `choco install -y composer --version 4.10.0`

### Linux (Debian/Ubuntu)

* PHP Version 5.5.9 oder höher (PHP 7.2 oder höher wird empfohlen): `sudo apt-get install php7.0` (php7.2)
* Datenbank-System:
  * MariaDB:
    * `apt` Repository Setup: `curl -sS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash`
    * MariaDB Setup: `sudo apt-get update && sudo apt-get install mariadb-server mariadb-client`
  * MySQL: `sudo apt-get install mysql-server mysql-client`
  * SQLite: `sudo apt-get install sqlite3`
    * **Hinweis** Eine installation von SQLite ist nicht notwendig um es in PHP zu benutzen, kann aber hilfreich sein, um die Datenbank manuell zu bearbeiten.
* [Composer](https://getcomposer.org/installer)
  * Nach dem Runterladen
    * `php ./installer`
    * `mv composer.phar /usr/local/bin/composer`

### MacOS (10.5 oder höher)

* PHP Version 5.5.9 oder höher (PHP 7.2 oder höher wird empfohlen).
  * PHP 5 vor-installiert seit OS X 10.5
  * Für PHP 7.3:
    * Homebrew: `brew install php@7.3`
    * Liip's php-osx tool: `curl -s https://php-osx.liip.ch/install.sh | bash -s 7.3`
* Datenbank-System:
  * MariaDB (via Homebrew): `brew install mariadb`
  * MySQL (für MacOS 10.13, oder höher):
    * via Homebrew `brew install mysql`
    * via DMG: [MySQL Server, DMG Archive](https://dev.mysql.com/downloads/file/?id=484914)
  * SQLite vor-installiert seit OS X 10.4
* [Composer](https://getcomposer.org/installer)
  * Nach dem Runterladen
    * `php ./installer`
    * `mv composer.phar /usr/local/bin/composer`

## Oder: via Docker

* Windows: [Docker Installer](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe)
* MacOS (OSX v10.12 oder höher): [Docker Installer](https://download.docker.com/mac/stable/Docker.dmg)
* Unix (Debian/Ubuntu):
  * `apt-get` voraussetzung zur Nutzung von HTTPS: `sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common`
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
    ```

## Drupal Setup

Nach der installation der Voraussetzungen muss sichergestellt werden, dass der Service/Deamon des ausgesuchten Datenbank-Systems läuft (mit der Ausnahme von SQLite, welches Dateibasiert ist).

> **Hinweis**
> <br>
> In allen Code-Schnipseln wird das Drupal Root-Verzeichnis als `$DRUPAL_ROOT` bezeichnet. Das kann durch das eigene Verzeichnis ersetzt werden, oder man fügt sie dem System als Umgebungsvariable hinzu.

### Schritt 1: Aufsetzen des Dateisystems

#### Via Download

Nun kann der Inhalt von `drupal-8.6.10.zip` (bzw. `drupal-8.6.10.tar.gz`) in ein beliebiges Verzeichnis entpackt werden. Dieses Verzeichnis dient als das Drupal Root-Verzeichnis (`$DRUPAL_ROOT`). Alle relativen Pfade gehen von diesem Verzeichnis aus.

In dem Verzeichnis befinden sich folgende Unter-Verzeichnisse:

* `modules`: Module die die Funktion von Drupal erweitern.
* `themes`: Designs die das Aussehen von Drupal verändern.
* `sites`: Beinhaltet die configuration von Drupal, sowie Öffentliche zugängliche und Private Dateien.
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

Drush (**Dru**pal **Sh**ell) ist eine Konsolen-Anwendung, die die Bedienung von Drupal erleichtert. Die Installation geschieht durch Composer im Drupal Root-Verzeichnis:

```console
$ composer require drush/drush
Using version ^9.5 for drush/drush
...
```

### Shritt 2: Starten des Drupal Servers

#### Via PHP

PHP hat einen eingebauten webserver. Dafür muss ein Terminal in `DRUPAL_ROOT` geöffnet werden, dann wird der Server mit dem Befehl `php -S <Adresse>:<Port>` gestartet. Für lokale Zwecke sollte `Addresse` mit `localhost`, `127.0.0.1` oder `[::1]` ersetzt werden. `Port` ist eine beliebige Zahl zwischen `1` und `65535`, Webseiten sind für gewöhnlich über den Port `80` zu erreichen (im falle von `http`; `443` für `https`). Ist der Port `80` nicht verwendbar, kann ein Port über `1024` versucht werden. Zum lokalen testen von Webseiten wird meist ein vierstelliger Port mit `8` am Anfang gewählt (z.B. `8000` oder `8080`).

##### Beispiel: PHP Server

```console
$ mkdir -p "$DRUPAL_ROOT"
$ cd "$DRUPAL_ROOT"
$ php -S localhost:8000
PHP 7.2.2 Development Server started at ...
Listening on http://localhost:8000
Document root is ...
Press Ctrl-C to quit.
```

#### Via Docker

* Starte den Datenbank Server: `docker run --name drupal_datenbank -e MYSQL_ROOT_PASSWORD=passwort -e MYSQL_DATABASE=drupal -d mariadb`
* Starte die Webseite: `docker run --name drupal_webseite --link drupal_datenbank:datenbank -p 8000:80 -d drupal`

### Shritt 3: Configuration des Drupal Systems

> **Hinweis**
> <br/>
> Wer sein System mit Docker installiert hat, muss als Datenbank-Host nicht `localhost` oder `127.0.0.1` angeben, sondern `datenbank` (siehe `--link` Option beim starten des Docker Containers)

#### Via Web-Interface

Nach dem der Drupal Server gestartet wurde, kann auf die lokale Webseite zugegriffen werden (z.B.: [`http://localhost:8000`](http://localhost:8000)). Folge dort den Anweisungen bis zum Punkt `Datenbank einrichten`. Welches Profil beim einrichten gewählt wird ist für dieses Dokument nicht wichtig. Hier wird jedoch von `Minimal` ausgegangen.

Unter `Datenbank einrichten` muss nun das zuvor konfigurierte Datenbank-System ausgewählt werden. Ist das gewünschte System nicht zu sehen, konnte es nicht von Drupal gefunden, werden. Ggf. stimmt dann etwas nicht mit der installation von PHP oder dem Datenbank-System.

Als nächstes werden die grundlegenden Einstellungen der Webseite vorgenommen und der Administrator angelegt.

> **Hinweis**
> <br>
> Standardmäßig können keine Emails vom System verschickt werden, daher ist es egal, was als Email eingetragen wird.

#### Via Drush

Drush kann die Einrichtung selbstständig vornehmen: `drush site:install <profil> --locale=de`, wobei `<profil>` eines von `standard` oder `minimal` sein muss. Im anschluss werden nach den Datenbank-Einstellungen gefragt.

> **Hinweis**
> <br/>
> Wer SQLite benutzen möchte muss an den Befehl noch (z.B.) `--db-url="sqlite://$DRUPAL_ROOT/meine_datenbank.sqlite"` anhängen