- - -
Title: Befehlszeilenwerkzeug Date: 2021-11-11 Order: 9
- - -

Bookwyrm-Entwickler und Instanz-Manager können das `bw-dev`-Skript für gemeinsame Aufgaben verwenden. Dies macht deine Befehle kürzer, leichter zu merken und schwieriger durcheinander zu bringen.

Sobald du Bookwyrm [in Produktion](installing-in-production.html) oder [in Entwicklung](https://docs.joinbookwyrm.com/developer-environment.html#setting_up_the_developer_environment)installiert hast, kannst du das Skript von der Kommandozeile aus mit `./bw-dev` ausführen, gefolgt von dem Unterbefehl, den du ausführen willst.

## Docker Kurzbefehle

### bash

Öffne eine interaktive `bash`-Sitzung im Docker-`Web`-Container.

### build

Äquivalent zu `docker-compose build`.

### dbshell

Öffnen Sie eine interaktive Postgres-Datenbank-Shell. Ich hoffe, du weißst, was du tust.

### runweb args

Führe einen beliebigen Befehl (oben durch `args` repräsentiert) im `web`-Container aus.

Äquivalent zu `docker-compose run --rm web`.

### service_ports_web args

Führe einen beliebigen Befehl im `Web`-Container (oben dargestellt durch `args`) mit exponierten Ports aus. Dies ist nützlich, wenn du `pdb`-Tests ausführen möchtest, da `runweb` nicht die `pdb`-Prompt freigibt.

Äquivalent zu `docker-compose run --rm --service-ports web`.

### shell

Öffne eine interaktive Django-Shell im Docker-`Web`-Container. Du würdest das verwenden, wenn du Django Shell-Befehle direkt ausführen möchtest.

### up [args]

Docker-Container starten oder neustarten, optional inklusive aller Argumente (oben dargestellt durch `args`). Äquivalent zu `docker-compose up --build [args]`

## Datenbank verwalten

### initdb

Eine Datenbank initialisieren.

### makemigrations [appname migration number]

_Dieser Befehl ist nicht verfügbar im `Produktion`szweig_.

Führt Djangos `makemigrations`-Befehl im Docker-Container aus. Wenn du die Datenbankstruktur in einem Entwicklungszweig geändert hast, musst du dies ausführen, damit deine Änderungen wirksam werden. Optional kannst du eine bestimmte Migration angeben, z.B. `./bw-dev makemigrations bookwyrm 0108`

### migrate

Führt Djangos `migrate`-Kommando im Docker-Container aus. Du musst dies immer nach `makemigrations` ausführen.

### resetdb

_Dieser Befehl ist nicht verfügbar im `Produktion`szweig_.

Setzt die Datenbank zurück. **Dieser Befehl löscht deine gesamte Bookwyrm-Datenbank**und initiiert dann eine neue Datenbank und führt alle Migrationen aus. Du solltest alle aktuellen Migrationsdateien löschen, die du nicht ausführen möchtest, _bevor_ das `resetdb` ausgeführt wird.

## Eine Bookwyrm-Instanz verwalten

### compile_themes

Kompiliert alle BookWyrm Themes, die `*.scss` Dateien sind, die in `bookwyrm/static/css/themes aufgelistet sind`.

### collectstatic

Migrier statische Assets in einen Docker-Container oder in einen S3-kompatiblen "Bucket", abhängig vom Kontext.

### generate_preview_images

Erzeugen von Vorschaubildern für Webseiten, Benutzer und Bücher. Es kann eine Weile dauern, wenn du viele Bücher hast. See [Optional Features: Generating preview images](/optional_features.html)

### remove_remote_user_preview_images

Remove generated preview images for remote users. Siehe [Optionale Funktionen: Vorschaubilder für entfernte Accounts entfernen](/optional_features.html)

### generate_thumbnails

Generates thumbnail images for book covers.

### populate_streams args

Re-populates Redis streams (user feeds). Normalerweise musst du das nicht ausführen, außer es gibt einen Fehler, der die Feeds deiner Benutzer*innen aus irgendeinem Grund löscht. You can specify which stream using the `--stream` argument.

### populate_list_streams

Re-populates Redis cache of lists. Du musst das normalerweise nicht ausführen, es sei denn, es gibt einen Fehler, der die Listen deiner Benutzer*innen aus irgendeinem Grund löscht.

### populate_suggestions

Empfohlene Benutzer*innen für alle anzeigen. You may want to run this manually to refresh suggestions.

### restart_celery

Startet den `celery_worker`-Docker-Container neu.

### update

Wenn es Änderungen im `-Produktions`-Zweig gibt, kannst Du Deine Instanz ohne Ausfallzeit aktualisieren.

This command `git pull`s the latest `production` branch updates, builds docker images if necessary, runs Django migrations, updates static files, and restarts all Docker containers.

### admin_code

Gets the secret admin code used to register the inital admin user on a new BookWyrm instance.

## S3 kompatiblen Speicher einrichten

Standardmäßig verwendet BookWyrm lokalen Speicher für statische Assets (Favicon, Standard-Avatar, etc.) und Medien (Benutzer-Avatare, Buchtitelbilder usw.), aber du kannst einen externen Speicherdienst verwenden, um diese Dateien zu bereitzustellen. BookWyrm verwendet django-storages, um externen Speicher wie S3-kompatible Dienste, Apache Libcloud oder SFTP anzubinden.

Siehe [Externer Speicher](/external-storage.html) für weitere Informationen.

### copy_media_to_s3

Migriere alle hochgeladenen Medien von einer bestehenden BookWyrm Installation in einen S3-kompatiblen "Bucket". Use for initial upload to an empty bucket.

### sync_media_to_s3

Sync new or changed uploaded media from an existing Bookwrym installation to an S3-compatible "bucket". Use to ensure all local files are uploaded to an existing bucket.

### set_cors_to_s3 filename

Copy a CORS rules JSON file to your S3 bucket, where `filename` is the name of your JSON file (e.g. `./bw-dev set_cors_to_s3 cors.json`)

## Entwicklung und Test

_These commands are not available on the `production` branch_.

### black

BookWyrm verwendet den [Black](https://github.com/psf/black) Code-Formatierer, um die Python Codebasis konsistent zu gestalten. Run `black` before committing your changes so the `pylint` task does not fail for your pull request and make you sad.

### prettier

BookWyrm uses [Prettier](https://prettier.io/) to keep the JavaScript codebase consistently styled. Run `prettier` before committing changes to scripts to automatically format your code.

### stylelint

BookWyrm uses [Stylelint](uhttps://stylelint.io/) to keep the CSS files consistently styled. Run `stylelintprettier` before committing changes to scripts to automatically format your code.

### formatters

This command runs all code formatters (`black`, `prettier`, and `stylelint`) in one go.

### clean

Remove all stopped Docker containers.

Äquivalent zu:

```shell
docker-compose stop
docker-compose rm -f
```

### makemessages

Erstellt Nachrichtendateien für alle Übersetzungstexte. After you have run `makemessages` you need to run `compilemessages` to compile the translations. Siehe [Djangos makemessages](https://docs.djangoproject.com/en/3.2/ref/django-admin/#makemessages).

### compilemessages

Compiles translation files. See [Django's compilemessages](https://docs.djangoproject.com/en/3.2/ref/django-admin/#compilemessages).

### pytest args

Run tests with `pytest`.

### deactivate_2fa

Deactivates two factor authentication for a given user.

### manual_confirm

Confirms a users email, sets the user to active.
