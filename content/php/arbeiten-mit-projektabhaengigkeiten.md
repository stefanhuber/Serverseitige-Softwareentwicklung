# Arbeiten mit Projekt-Abhängigkeiten

## Projekt Abhängigkeiten

Web-Applikationen werden meist nicht komplett "from scratch" programmiert. Bestehende externe Bibliotheken oder Frameworks werden innerhalb der Applikation genutzt. Diese externen Bibliotheken nennt man Projekt-Abhängigkeiten und müssen im Projekt verwaltet werden.

Für alle Programmiersprachen gibt es entsprechende Programme, welche die Verwaltung der Abhängigkeiten bewerkstelligen. Bekannt sind unter anderem `npm` für `javascript`, `gradle` für `java` oder `pip` für `python`. Für PHP gibt es `composer`.

## Bezeichnung von Abhängigkeiten

EntwicklerInnen oder Firmen können PHP-Bibliotheken als Open Source Software veröffentlichen und zur Installation über Composer bereitstellen. Die entsprechenden Bibliotheken können auf [Packagist](https://packagist.org/) veröffentlicht werden und somit in eigene Projekte integriert werden.

Den [Statistiken](https://packagist.org/statistics) ist zu entnehmen das ca. 260.000 Bibliotheken auf Packagist veröffentlicht sind. Damit Bibliotheken eindeutig identifizierbar sind und damit es zu keinen Namenskonflikten kommt, wird ein Bezeichnungsschema benötigt.

Composer Projekte werden mit einem *Vendorname* und einem *Projektname* eindeutig bezeichnet. ZB hat Facebook eine PHP-Bibliothek veröffentlicht um mit PHP-Anwendungen die Facebook Graph API abzufragen: `facebook/graph-sdk`. Dabei ist `facebook` als Vendorname und `graph-sdk` als Projektname zu verstehen.

## Versionierung von Abhängigkeiten

Software Bibliotheken werden kontinuierlich neu veröffentlicht. Der Grund dafür können zum Beispiel neue Features oder aber auch Bugfixes sein. Für Nutzer einer Bibliothek ist es wichtig zu wissen, ob neue Veröffentlichungen einer Bibliothek einfach einzubinden sind oder ob diese langwierige API-Änderungen zur Folge haben.

### Semantic Versioning

Software sollte nach dem Prinzip des [*Semantic Versioning*](https://semver.org/) versioniert werden. Dies geschieht auch für viele Projekte, welche über Composer bereitgestellt werden. Im Folgenden sollen die Konzepte des Semantic Versioning kurz zusammengefasst werden:

 - Eine Versionsnummer besteht aus 3 Bestandteilen `X.Y.Z`, dabei ist `X` die Major-Version, `Y` die Minor-Version und `Z` die Patch-Version.
 - Eine Major-Version definiert eine öffentliche API der Bibliothek, welche bei Änderungen der Minor- oder Patch-Version keine `Breaking Changes` erfährt. Zum Beispiel würde der Release `2.1.0` als Nachfolger von `2.0.21` für Nutzer der Bibliothek keine Änderung bedeuten.
 - Eine Minor-Version wird erhöht, wenn neue Funktionen zur Bibliothek hinzukommen. Die öffentliche API der Bibliothek erfährt keine Änderungen, welche die Rückwärtskompatibilität beeinträchtigen. Es werden nur neue Funktionen hinzugefügt, die bestehende API bleibt identisch.
 - Eine Patch-Version wird erhöht, falls Bugfixes oder interne Änderungen an der Bibliothek durchgeführt wurden. Die öffentliche API wird in keinster Weise abgeändert.
 - Es gibt die Möglichkeit sog. Pre-Releases durchzuführen, diese werden über einen mit Bindestrich angegebenen Bezeichner angegeben. Zum Beispiel kann eine Alpha- oder Beta-Version einer Bibliothek veröffentlicht werden: `1.0.0-alpha` oder `1.0.0-beta`.

### Versionsangabe

Die Version einer Abhängigkeit kann über unterschiedliche Varianten angegeben werden. Um nach Möglichkeit auch gültige zukünftige Versionen von Bibliotheken zu spezifizieren, können Range-Angaben verwendet werden.

#### Exakte Versionsionsangabe

Eine exakte Versionsangabe bedeutet, dass nur genau diese Version verwendet werden soll und keine andere.

Beispiele:

 - `1.0.5`: Bedeutet exakt diese Version und keine andere
 - `2.2.2`: Bedeutet exakt diese Version und keine andere

#### Angabe einer Range

Durch Vergleichsoperatoren (`<`, `>`, `<=`, `>=`, `!=`) können Ranges definiert werden. Zusätzlich können auch logische Operationen verwendet werden um mehrere Range Definitionen zu verknüpfen. Dabei ist mit Leerraum oder Beistrich (` ` oder `,`) ein logisches UND definiert und mit `||` ein logisches ODER definiert.

Beispiele:

 - `>=1.0 <2.0`: Ein Range innerhalb der Version mit mindestens `1.0` und kleiner `2.0`. Zum Beispiel also `1.0.8`, `1.11.45` oder `1.0.0`.
 - `>1.1 <=2.1 || >=3`: Der Range ab `1.2.0` bis `2.1.0` oder ab Version `3.0.0`.

#### Wildcard oder Von-Bis Angabe

Zur Vereinfachung von Versionrange Angaben kann der Wildcard-Operator oder eine Von-Bis Angabe durchgeführt werden.

Beispiele:

 - `2.1.*`: Gleichbedeutend zu `>=2.1 <2.2`
 - `1.*`: Gleichbedeutend zu `>=1 <2`
 - `1.0 - 2.0`: Gleichbedeutend zu `>=1.0.0 <2.1`
 - `1.0.0 - 2.1.0`: Gleichbedeutend zu `>=1.0.0 <=2.1.0`

#### Ähnliche Versionen

Mit `~` können ähnliche Versionen ohne die Minor-Version zu erhöhen angegeben werden.

Beispiele:

 - `~1.2.3`: Gleichbedeutend zu `>=1.2.3 <1.3.0`
 - `~2.1.1`: Gleichbedeutend zu `>=2.1.1 <2.2.0`

#### Kompatible Versionen

Mit `^` können kompatible Versionen angegeben werden (Major-Version bleibt gleich).

Beispiele:

 - `^1.2.3`: Gleichbedeutend zu `>=1.2.3 <2`
 - `^2.1.1`: Gleichbedeutend zu `>=2.1.1 <3`

## Funktionsweise von Composer

Composer ist ein Kommandozeilenprogramm, dass es ermöglicht Projekt-Abhängigkeiten aufzulösen und entsprechende Bibliotheken lokal im Projekt zu installieren. Entsprechende Bibliotheken werden innerhalb des Ordners `vendor` im Hauptverzeichnis des Projektes hinterlegt.

Für alle Klassen der installierten Bibliotheken wird ein Autoloader definiert (siehe [Class-Autoloader](quellcode-organisieren.md#class-autoloader)). Nur dieser generierte Autoloader muss eingebunden werden um die installierten Bibliotheken zu nutzen. Dieser befindet sich in der Datei `vendor/autoload.php`:

Alle Abhängigkeiten werden in der Datei `composer.json`, über Name und Version, abgelegt. Dabei wird zwischen Projekt-Abhängigkeiten (`require`) und Abhängigkeiten für die Entwicklungszeit (`require-dev`) unterschieden.

## Composer Konfigurationsdatei

Ein Projekt, welches über Composer verwaltet wird, hat eine `composer.json` Datei im Hauptverzeichnis des Projektes. Diese JSON-Datei enthält unter anderem eine Aufschlüsselung über alle Abhängigkeiten des Projektes. Im der unten angeführten Datei `composer.json`, werden zwei Projekt-Abhängigkeiten und eine Entwicklungsabhängigkeit definiert:

```json
{
    "require": {
        "monolog/monolog": "^2.0.2",
        "facebook/graph-sdk": "^5.7.0"
    },
    "require-dev": {
        "phpunit/phpunit": "^9.0.1"
    }
}
```

Um die Replizierbarkeit des Verhaltens eines Software-Projektes zu gewährleisten, müssen die exakten Versionen der installierten Bibliotheken dokumentiert werden. Dies geschieht über die Datei `composer.lock`. Diese Datei wird ausschließlich von der Composer erzeugt und sollte in keinem Fall manuell editiert werden.
 
Innerhalb der Datei `composer.json` können viele weitere Projekteigenschaften definiert werden. Eine umfassende Dokumentation darüber befindet sich in der Dokumentation über das [JSON-Schema](https://getcomposer.org/doc/04-schema.md).

!!! danger
    Innerhalb eines Version-Control Systems (VCS) wie zB Git sollen ausschließlich die Dateien `composer.json` bzw. `composer.lock` verwaltet werden. In keinem Fall soll der `vendor` Ordner mit dem Quellcode der Abhängigkeiten im VCS getrackt werden. Alle EntwicklerInnen können über Composer alle Abhängigkeiten jederzeit installieren, deshalb gibt es keinen Grund diese ins VCS zu integrieren.

## Installation von Composer

Composer kann über einen Windows-Installer installiert werden. Dieser kann über die [Composer Website](https://getcomposer.org/doc/00-intro.md#installation-windows) heruntergeladen werden. Für MacOS oder Linux gibt es entsprechende [Installationsanleitungen](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-macos).

## Nutzung von Composer

Composer ist ein Kommandozeilenprogramm und bietet unterschiedliche Kommandos zum Arbeiten mit Abhängigkeiten. Eine umfassende Beschreibung der Kommandos findet sich in der [Dokumentation](https://getcomposer.org/doc/03-cli.md) oder über das Command `composer help`.

### Abhängigkeiten hinzufügen

Mit dem Command `require` werden Abhängigkeiten in die Datei `composer.json` hinzugefügt und installiert. Dabei kann über die Option `--dev` angezeigt werden, dass es sich um eine Entwicklungsabhängigkeit handelt.

Abhängigkeiten können ohne Version angegeben werden, somit wird die neueste Version installiert und in die `composer.json` wird diese Version mit `^` gesetzt, um die Updatefähigkeit auf zukünftige kompatible Versionen anzugeben:

```bash
composer require facebook/graph-sdk
```

Es können Abhängigkeiten natürlich auch mit einer entsprechenden Versionsangabe installiert werden:

```bash
composer require "facebook/graph-sdk:>=5.7 <6"
```

### Abhängigkeiten installieren

Mit dem Command `install` werden alle Abhängigkeiten, welche innerhalb der `composer.json` definiert sind aufgelöst und lokal im Ordner `vendor` installiert. Dabei wird auch eine `composer.lock` Datei erzeugt, welche eine exakte Dokumentation über die installierten Versionen der Abhängigkeiten führt.

```bash
composer install
```

### Update der Abhängigkeiten

Mit dem Command `update` wird versucht alle Abhängigkeiten, unter Berücksichtigung der Range Angabe der Versionen, auf den neuesten Stand zu bringen. Dabei wird auch die Datei `composer.lock` entsprechend angepasst.

```bash
composer update
```
