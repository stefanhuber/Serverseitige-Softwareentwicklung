# Arbeiten mit Arrays

Arrays sind ein wichtiger Bestandteil von Programmiersprachen. Erst mit Arrays können wichtige Algorithmen sinnvoll implementiert werden. PHP ermöglicht einen sehr flexiblen Umgang mit Arrays. Generell werden 2 Arten von Arrays unterschieden: `Index-basierte Arrays` und `assoziative Arrays`. Diese beiden Arten können in der Verwendung auch gemischt werden (Vorsicht) und jeweils auch als mehrdimensionale Arrays verwendet werden. Im Folgenden sollen die wichtigsten Aspekte zum Arbeiten mit Arrays in PHP eingeführt werden.

### Erzeugung eines leeren Arrays

```php
<?php
$mein_array_1 = [];
$mein_array_2 = array(); // alte Syntax kann noch verwendet werden
```

### Erzeugung von Arrays mit Inhalten

Wird kein Schlüssel angegeben werden Inhalte indexiert. Auch mehrdimensionale Inhalte können hinzugefügt werden:

```php
<?php
$index_array = [
    'index 0',
    123,
    [ 'mehr', 'dimensional' ]
];
```

Assoziative Arrays werden über Schlüssel/Wert Paare erzeugt:

```php
<?php
$assoziatives_array = [
    'ein_schlüssel' => 123,
    'anderer_key' => 456
];
```

Es können ohne weiters Index-basierte und assoziative Arrays gemischt werden:

```php
<?php
$array = [
    'ein_schlüssel' => 123,
    123,
    'anderer wert'
];
```

### Zugriff auf Array-Elemente

```php
<?php
$array = [
    'ein_schlüssel' => 123,
    456,
    'anderer wert'
];

echo $array[0] . "\n";
echo $array[1] . "\n";
echo $array['ein_schlüssel'] . "\n";
echo count($array);
```

Bei einer Mischung von Index-basierten und assoziativen Elementen, zählen die assoziativen Elemente nicht zu den Indizes jedoch zur Länge des Arrays. Dies ist gerade bei **Schleifen zu beachten**. Die Ausgabe des oben angeführten Programms sieht wie folgt aus:

```
456
anderer wert
123
3
```

### Arrays verändern

Neue Elemente können mittels einer leicht abgewandelten Zuweisungsoperation am Ende hinzugefügt werden. Dazu muss die Variable mit dem Suffix `[]` ausgestattet werden.

```php
<?php
$array = [];
$array[] = 'index 0';
$array[] = 'nächster Eintrag';
```

Ebenfalls kann eine Array-Position mittels Index bzw. Schlüssel angesprochen werden. Falls ein Index oder Schlüssel noch nicht besteht wird dieser neu hinzugefügt ansonsten abgeändert:

```php
<?php
$array = [];
$array[0] = 'index 0';
$array['next key'] = 'nächster Eintrag';
```

### Iteration durch Arrays (Foreach-Schleife)

Für die Iteration können Schleifen verwendet werden. Dazu können bekannte For-Schleifen, While-Schleifen oder Do-While-Schleifen verwendet werden. Neben diesen Varianten gibt es für PHP auch eine eigene `Foreach-Schleife`.

```php
<?php
$array = [];
$array[0] = 'index 0';
$array['next key'] = 'nächster Eintrag';

foreach ($array as $value) {
	echo $value . "\n";
}

foreach ($array as $key => $value) {
	echo $key . ": " . $value . "\n";
}
```

Die `Foreach-Schleife` iteriert durch jedes Element aus dem Array (Index-basierte Arrays sowie assoziative Arrays). Durch das syntaktische Konstrukt `$key => $value` kann zu jedem Element auch der Index bzw. Schlüssel abgefragt werden. Die Ausgabe des oben angeführten Programms sieht wie folgt aus:

```
index 0
nächster Eintrag
0: index 0
next key: nächster Eintrag
```

### Wichtige Array Funktionen

#### Elemente zählen

Mit der Funktion `count` oder `sizeof` (als Alias) kann die Anzahl der Elemente in einem Array gezählt werden:

```php
<?php
$arr = ["hello", "array", "count"];
echo count($arr);  // 3
echo sizeof($arr); // 3
```

#### Existenz prüfen

Mit der Funktion `isset` kann geprüft werden, ob ein Index oder Schlüssel eines Arrays gesetzt ist (bzw. existiert).

!!! warning "Vorsicht"
    Ein Zugriff auf einen undefinierten Schlüssel oder Index würde zu einem Fehler führen.

```php
<?php

$arr = [
    'key1' => 'value1',
    'key2' => 'value2'
];

if (isset($arr['key1'])) {
    echo "key1 ist gesetzt";
} else {
    echo "key1 ist nicht gesetzt";
}


if (isset($arr['key_xyz'])) {
    echo "key_xyz ist gesetzt";
} else {
    echo "key_xyz ist nicht gesetzt";
}
```

Das oben angeführte Programm führt zu folgender Ausgabe:

```
key1 ist gesetzt
key_xyz ist nicht gesetzt
```

#### Element löschen

Die Funktion `unset` kann verwendet werden um einzelne Elemente eines Arrays zu löschen.

```php
<?php

$arr = [
    "a" => "a value",
    "b" => "b value"
];

echo count($arr) . "\n"; // 2

unset($arr["a"]);

echo count($arr) . "\n"; // 1
```

#### Dokumentation

PHP verfügt über eine große Menge von Array Funktionen. Diese Funktionen sind ausführlich mit Beispielen innerhalb der [PHP-Dokumentation](https://www.php.net/manual/en/ref.array.php) zu finden.