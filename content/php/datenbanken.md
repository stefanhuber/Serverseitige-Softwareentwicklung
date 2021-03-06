# Arbeiten mit Datenbanken

PHP Data Objects (PDO) ist eine Datenbankabstraktionsschicht, welche einen einheitlichen Zugriff auf unterschiedliche DBMS über eine Schnittstelle ermöglicht. Somit kann zB zur Entwicklungszeit SQLite verwendet werden und in der Produktivumgebung auf Oracle gesetzt werden. PDO bietet folgende Vorteile:

 - Sicherheit: Escaping von Queryparameter über Prepared Statements
 - Usability: Viele nützliche Funktionen um einen unproblematischen Zugriff auf das DBMS zu realisieren
 - Wiederverwendbarkeit: Einheitliche API für den Zugriff auf unterschiedliche DBMS

Für die Arbeit mit PDO werden generell 2 Klassen benötigt. Die Klasse `PDO` als Einstiegspunkt und die Klasse `PDOStatement`, welche einzelne SQL-Statements repräsentiert.


## Verbindung mit DSN

Um die Verbindung zu einem DBMS aufzubauen wird ein gültiger Data Source Name (DSN) benötigt. DSNs sind generell nach einem einfachen Schema aufgebaut. Dannach folgen unterschiedliche Verbindungsparameter als Schlüssel/Wert Paare mit `;` getrennt.

Als wichtige Verbindungsparameter sind dabei `host`, `dbname`, `port` und `charset` zu nennen. Im Folgenden findet sich ein Beispiel eines DSN für eine MySQL-Datenbank mit dem Namen `test` auf Port `3306` und einem `utf-8` Charset:

```
mysql:host=localhost;dbname=test;port=3306;charset=utf-8
```

Mit der Klasse `PDO` kann die tatsächliche Verbindung zu einem DBMS über den DSN aufgebaut werden. Je nach verwendetem DBMS sind ein gültiger Username bzw. Passwort wichtige Ergänzungen zum DSN. Im Folgenden Code-Abschnitt wird die Verbindung zu einer Mysql-Datenbank aufgebaut: 

```php
<?php

$dsn = "mysql:host=localhost;dbname=test;port=3306;charset=utf-8";
$username = "root";
$password = "geheim";

try {
    $pdo = new PDO($dsn, $username, $password);
} catch (Exception $e) {
    echo "ERROR: " . $e->getMessage();
}
```

Die oben erzeugte Variable `$pdo` wird in den weiteren Quellcode-Abschnitten dieses Textes wiederverwendet. 

## Exec und Query

Die Klasse `PDO` bietet zwei generische Methoden (`exec` und `query`) um mit einer Datenbank zu arbeiten.

Die Methode `exec` kann genutzt werden um ein SQL-Statement auszuführen. Als Ergebnis liefert die Methode "nur" die Anzahl der Zeilen, welche durch das SQL-Statement betroffen worden sind. Im folgenden Beispiel wird ein DELETE-Statement mit `exec` ausgeführt. Als Ergebnis wird die Anzahl der gelöschten Zeilen an die Variable `$count` zurückgegeben:

```php
<?php
$count = $pdo->exec("DELETE FROM products WHERE price > 10");
```

Die Methode `query` erzeugt eine Instanz der Klasse `PDOStatement`. Ein `PDOStatement` stellt unter anderem eine Ergebnismenge dar und kann iteriert werden:

```php
<?php
$sql = "SELECT * FROM products ORDER BY price";
foreach ($pdo->query($sql) as $row) {
    echo $row['name'] . " " . $row['price'] . "\n";
}
```

!!! warning
    Die Methoden `exec` und `query` sollten nur für statische SQL-Statements verwendet werden. Variable Parameter werden nicht escaped und sind daher offen für SQL-Injections.


## Prepared Statements

SQL-Queries benötigen häufig Parameter aus dem PHP-Programm. Unter anderem aus Sicherheitsgründen sollten dazu Prepared Statements genutzt werden.

Im Beispiel sollen alle Attribute eines Produktes anhand der ID des Produktes abgefragt werden. Die ID des Produktes könnte zB über den Querystring der URI an das PHP Programm übergeben werden.

Die Variable `$pdo` stellt eine gültige Datenbankverbindung dar (siehe oben). Folgende Schritte werden im Skript durchgeführt:

 - Es wird ein Prepared Statement erzeugt (Methode `prepare`). Mit `?` werden Platzhalter für variable Bestandteile der SQL-Query definiert.
 - Das erzeugte Prepared Statement kann über die Methode `execute` ausgeführt werden. Dabei wird ein Array übergeben, dass alle definierten Platzhalter der Reihe nach ersetzt. Alle übergebenen Werte werden escaped, sodass es zu keiner SQL-Injection kommen kann.
 - Die Methode `fetch` kann genutzt werden um das Query-Resultat zeilenweise abzufragen. `fetch` gibt `false` zurück, falls das Ergebnis leer ist.

```php
<?php

$statement = $pdo->prepare('SELECT * FROM products WHERE id = ?');

$statement->execute([$_GET['id']]);

$product = $statement->fetch();
```

## INSERT Statement

```php
<?php
$statement = $pdo->prepare("INSERT INTO products (name, description, price) VALUES (?, ?, ?)");
$statement->execute(["Tolles Produkt", "Ein schönes Produkt mit vielen Extras...", 5.99]);
```

## UPDATE Statement

```php
<?php
$statement = $pdo->prepare("UPDATE products SET name = ? WHERE id = ?");
$statement->execute(["Neuer Produktname", 13]);
```

## DELETE Statement

```php
<?php
$statement = $pdo->prepare("DELETE FROM products WHERE id = ?");
$statement->execute([13]);
```

## CREATE Statement

```php
<?php

$create = "CREATE TABLE IF NOT EXISTS products (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    description TEXT NOT NULL,
    image TEXT NOT NULL,
    price REAL
)";

$pdo->exec($create);
```

## Details zu Fetch

### Zeilenweises Auslesen

Die Methode `fetch` liefert immer nur eine Ergebniszeile je Aufruf. Die Methode fungiert dabei wie ein Cursor und springt die Ergebnismenge zeilenweise durch. Mit einer Schleife kann somit durch die Ergebnismenge iteriert werden. Falls die Ergenbismenge leer ist oder der Cursor am Ende angekommen ist, liefert die Methode `fetch` `false` als Rückgabewert.

```php
<?php

$stmt = $pdo->prepare('SELECT name, price FROM products');
$stmt->execute();

while ($product = $stmt->fetch())
{
    echo $product['name'] . ": € " . $product['price'] . "\n";
}
```

### Fetch Ergebniszeile

Die Repräsentation einer Ergebniszeile kann über den sog. `Fetch-Style` angepasst werden. PDO bietet 11 verschiedene Fetch-Styles. Die wichtigsten sollen kurz zusammengefasst werden:

 - `FETCH_ASSOC`: Das Ergebnis wird als assoziatives Array repräsentiert.
 - `FETCH_NUM`: Das Ergebnis wird als indexiertes Array repräsentiert.
 - `FETCH_BOTH`: Dies ist die default Einstellung. Eine Kombination aus `FETCH_ASSOC` und `FETCH_NUM`.
 - `FETCH_OBJ`: Das Ergebnis wird als "anonymes" Objekt (der Klasse `stdClass`) repräsentiert. Die Spalten werden auf Objekteigenschaften gemappt.
 - `FETCH_LAZY`: Ähnlich zu `FETCH_OBJ`, die Attribute des Objektes werden jedoch erst zugewiesen, wenn sie abgefragt werden.
 - `FETCH_CLASS`: Das Ergebnis wird innerhalb eines Objektes der entsprechenden Klasse repräsentiert.

Die Methode `fetchObject` kann komfortabel anstelle der Angabe des `FETCH_OBJ` oder `FETCH_CLASS` Style verwendet werden.

### Beispiel fetchObject

```php
<?php

class Product
{
    protected $id;
    protected $name;
    protected $price;

    // ... getter/setter
}

$statement = $pdo->prepare("SELECT id, name, price FROM products");
$statement->execute();

// $product ist eine Instanz der Klasse Product:
$product = $statement->fetchObject(Product::class);
```

## Weitere Ressourcen

 - Die PHP-Dokumentation bietet zu [PDO](https://www.php.net/manual/en/book.pdo.php) und allen Klassen und Methoden eine ausführliche Beschreibung.
 - Die Website [phpdelusions.net](https://phpdelusions.net/) bietet ein umfangreiches [Tutorial zu PDO](https://phpdelusions.net/pdo).
