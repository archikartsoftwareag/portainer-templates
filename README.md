# Archikart Software AG - portainer files

This repostiory contains our portainer template and docker-compose files

## Hinweise zu Portainer

- Bitte beachten Sie das aktuelle Portainer Versionen eine neue Template Version benötigen. Falls es bei Ihnen Probleme mit dem Template gibt, dann nutzen Sie bitte folgendes Template, welches Sie unter Portainer -> Settings -> App Templates -> URL einfügen können:

neue v3 Version:

``` sh
https://raw.githubusercontent.com/archikartsoftwareag/portainer-templates/main/archikart-templates.v3.json
```

alte v2 Version:

``` sh
https://raw.githubusercontent.com/archikartsoftwareag/portainer-templates/main/archikart-templates.json
```

## Anmerkungen

- 22.05.2025 - Die Versionierung unserer Elastic Images wurde verändert. Es gibt jetzt keine Images mit Endungen wie „_v77“ mehr. Je Archikart-Version gibt es nun zwei Versionsschemata (Major.Minor und Major.Minor.Patch).
  - Nachfolgendes Beispiel ist für Archikart 4.77 und das Elastic-Image:
    - docker.archikart.de/archikart/elastic:4.77 – „4.77“ entspricht der „latest“-Version für Archikart 4.77 und wird auch als Standard im Portainer-Template verwendet.
    - docker.archikart.de/archikart/elastic:4.77.0 – „4.77.0“ entspricht einer bestimmten Version für Archikart 4.77 und kann verwendet werden, wenn eine bestimmte Version benötigt wird. Dies __kann__ erforderlich sein, falls es innerhalb einer Archikart-Version zu Änderungen kommt. Eine neue Version bedeutet jedoch nicht zwangsläufig, dass auch eine bestimmte Archikart-Version benötigt wird. Die aktuellste Version (hier 4.77.0) ist immer identisch mit der entsprechenden „latest-Version" (hier also die 4.77).
  - Bitte beachten Sie, dass es auch ein echtes „latest“ (docker.archikart.de/archikart/elastic:latest) gibt, das Archikart-versionsunabhängig ist. Dieses sollte jedoch produktiv nicht verwendet werden.

## Kunden ohne Internet auf ihrem Docker Host Server

### Voraussetzungen und Allgemeines

- Benötigt wird neben dem Docker Host Rechner (ohne Internet) ein zweiter Rechner mit Internet und installierten Docker ( kann auch ein Windows 10/11 mit Docker sein) - dieser wird nur zum "pullen" (herunterladen) und speichern der Images verwendet.

- Unsere Service entsprechen den docker-compose.Service_Name.yml Dateien. Diese Dateien sind in diesem Repository zu finden. Diese Docker-Compose Dateien beschreiben zumeist mehrere zusammengefasste Docker Container die letztlich einen Service bereitstellen (auch Stack genannt). Hier werden auch die benötigten Images definiert.

- Um den Stack letztlich zu starten werden die *docker-compose.Service_Name.yml* und die *.env* (hier werden Werte - wie Ports - gesetzt) Datei benötigt und die Images müssen lokal verfügbar sein.

- Pro Stack den Sie starten wollen, benötigen Sie die entsprechende *docker-compose.Service_Name.yml* Datei und die dazugehörige *.env* Datei und die Images. Jeder Stack sollte also in einem eigenen Verzeichnis abgelegt werden.

- Die *.env* Datei ist allgemein verfasst, in dieser finden Sie auch weitere Informationen.

### Images pullen (/herunterladen) und speichern - auf dem Rechner mit Internet ausführen

#### download der docker-compose Datei und .env Datei

*Anmerkung:* Beide Dateien werden neben den Images benötigt um den Stack zu starten.

#### docker login

```sh
docker login docker.archikart.de
```

**Username und Passwort eingeben** - siehe Handbuch ( Einrichtung portainer)

#### benötigte Images pullen

*Anmerkung:* je nach Stack werden verschiedene images benötigt die "gepulled" werden müssen. Um herauszufinden welche Images benötigt öffnen Sie die entsprechende docker-compose Datei und suchen nach "image:".

```sh
docker pull <IMAGE_NAME>
```

Beispiel **alkis_v77** stack:

**<IMAGE_NAME>** mit folgende ersetzem:

- docker.archikart.de/archikart/alkis_gid7_v77:latest
- docker.archikart.de/archikart/alkis_postgis_v77:latest

was dann folgerdermassen aussieht:

```sh
docker pull docker.archikart.de/archikart/alkis_gid7_v77:latest
docker pull docker.archikart.de/archikart/alkis_postgis_v77:latest
```

#### Images speichern

*Anmerkung:* alle benötigten images abspeichern.

```sh
docker save -o <path_to_tar_file> <IMAGE_NAME>
```

Beispiel **alkis_v77** stack mit Verzeichnis **D:\images**:

```sh
docker save -o D:\images\alkis_gid7 docker.archikart.de/archikart/alkis_gid7_v77:latest
docker save -o D:\images\alkis_postgis docker.archikart.de/archikart/alkis_postgis_v77:latest
```

#### Images auf die Maschine ohne Internet übertragen

*Vorgehen:* Kopieren Sie alle gespeicherten Images, sowie die entsprechende *docker-compose* und *.env* Datei auf den Docker Host Rechner (ohne Internet).

Beispiel: Folgendermassen könnte das Verzeichnis für **alkis_v77** aussehen:

```plaintext
D:\Docker\alkis_v77
├── docker-compose.aliks_v77.yml
├── .env
├── alkis_gid7.tar
└── alkis_postgis.tar
```

### Images importieren und Stack starten - auf dem Rechner ohne Internet ausführen

#### Images importieren

*Anmerkung:* alle benötigten images importieren, beachten Sie auch die Namen mit denen die Images gespeichert wurden.

```sh
docker load -i <path_to_tar_file>
```

Beispiel **alkis_v77** stack mit Verzeichnis **D:\dockerimages**:

```sh
docker load -i D:\Docker\alkis_v77\alkis_gid7
docker load -i D:\Docker\alkis_v77\alkis_postgis
```

#### *.env* Datei anpassen

*Anmerkung:* Vorgehen und Hinweise sind in der *.env* Datei beschrieben.

Beispiel **alkis_v77** stack:

```sh
ALKIS_GID7_PORT=10722
ALKIS_POSTGIS_PORT=5432
```

#### Stack hochfahren / deployen

Im Terminal/ Konsole in das Verzeichnis wechseln in dem sich die *docker-compose* Datei befindet. Und anschliessend den Stack mit folgendem Befehl starten:

```sh
docker-compose -f DOCKER_COMPOSE_DATEI_NAME.yml up -d
```

Beispiel Alkis Stack mit .env Datei:

```sh
docker-compose -f docker-compose.alkis_v77.yml up -d
```

### Weitere Befehle

#### Stack löschen (ohne ein eventuell dazugehörendes Volume zu löschen)

```sh
docker-compose -f DOCKER_COMPOSE_DATEI_NAME.yml down
```

#### Stack löschen (und ein eventuell dazugehörendes Volume löschen)

```sh
docker-compose -f DOCKER_COMPOSE_DATEI_NAME.yml down -v
```

#### Informationen und Status der laufenden Container anzeigen

```sh
docker ps
```

und

```sh
docker stats
```
