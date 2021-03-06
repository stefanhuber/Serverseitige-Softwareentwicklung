# Objektorientierte Programmierung

PHP ist eine objektorientierte Programmiersprache und besitzt alle gängigen Konzepte einer objektorientierten Programmiersprache. 

## Klassen

Klassen werden über das Schlüsselwort `class` definiert. Es gibt spezielle Methoden wie den Konstruktor (`__construct`), welche mit doppeltem Unterstrich beginnen. Alle Member sind generell `public` außer es wird explizit anders angegeben wie zB für den Member `$color`.

Mit dem Schlüsselwort `new` können Klasseninstanzen erzeugt werden. Über die Variable `$this` kann innerhalb der Klasse auf die Instanz zugegriffen werden. Zugriff auf Member der Instanz wird über `->` durchgeführt. So wird zum Beispiel die Methode `drive` auf der Instanz `$veh` über `$veh->drive()` ausgeführt.

```php
<?php

class Vehicle
{
    protected $color;

    function __construct(string $color)
    {
        $this->color = $color;
    }

    function drive()
    {
        echo $this->color . " vehicle is driving!";
    }
}

$veh = new Vehicle("red");
$veh->drive();  // red vehicle is driving!
```

## Magic Methods

Es sind einige spezielle Methoden (Magic Methods) für Klassen definiert. Die Methodennamen dieser speziellen Methoden beginnen mit `__` (zB der Konstruktor `__construct`). Eine Übersicht aller Magic Methods findet sich in der [PHP Dokumentation](https://www.php.net/manual/en/language.oop5.magic.php). Eine Auswahl dieser Methoden werden im folgenden dargestellt.

### __set und __get

Für Setter und Getter gibt es die generischen Methoden `__set` und `__get`. Falls diese Methoden implementiert werden, werden diese für jegliches Setzen bzw. Abfragen von Objekt Attributen aufgerufen.

```php
<?php

class Product
{
    protected $name;
    protected $price;

    function __set($name, $value)
    {
        if ($name == "name") {
            $this->name = (string) $value;
        } elseif ($name == "price") {
            $this->price = (float) $value;
        }
    }

    function __get($name)
    {
        if ($name == "name") {
            return $this->name;
        } elseif ($name == "price") {
            return $this->price;
        } elseif ($name == "vat") {
            return $this->price * 0.2;
        }
    }
}

$product = new Product();
$product->name = "Mein Produkt";
$product->price = 3.99;

echo $product->name . "/ Preis inkl. MWSt.: " . $product->vat . "\n";
```

### __invoke

Mit der Implementierung der Methode `__invoke` kann ein Objekt wie eine Funktion genutzt werden. Der Nutzen liegt dabei darin, dass das Objekt einen internen Zustand halten kann, welcher über mehrmalige Aufrufe erhalten bleibt. Dies soll im folgenden Beispiel demonstriert w:

```php
<?php

class FunctionObject
{
    protected $internalState = 10;

    function __invoke()
    {
        $this->internalState++;
        return $this->internalState;
    }
}

$obj = new FunctionObject();
$result = $obj(); // 11
$result = $obj(); // 12
$result = $obj(); // 13
```

### __toString

Durch überschreiben der Methode `__toString` kann bestimmt werden, wie ein Objekt in einen String überführt wird. Dies ist unter anderem zu Debugging Zwecken sehr hilfreich.

### __call

Die Methode `__call` wird aufgerufen, wenn eine nicht existierende Methode auf einem Objekt aufgerufen wird.

```php
<?php

class Vehicle
{
    function __call($methodName, $arguments)
    {
        if ($methodName == "drive") {
            echo "Das Fahrzeug fährt\n";
        } elseif($methodName == "break" || $methodName == "stop") {
            echo "Das Fahrzeug bremst\n";
        } else {
            echo "Diese Aktion führt zu nichts...\n";
        }
    }
}

$veh = new Vehicle();
$veh->drive();  // Das Fahrzeug fährt
$veh->break();  // Das Fahrzeug bremst

$veh->irgendEineMethodeMitKomischemName();  // Diese Aktion führt zu nichts...
$veh->andereMethodeOhneNutzen();            // Diese Aktion führt zu nichts...
```

## Vererbung

Über das Schlüsselwort `extends` wird die Klassenvererbung durchgeführt. PHP unterstützt keine Mehrfachvererbung.

```php
<?php

class Car extends Vehicle
{
    function drive()
    {
        echo $this->color . " CAR is driving!";
    }
}

$car = new Car("blue");
$car->drive();  // blue CAR is driving!
```

## Statische Member

Mit dem Schlüsselwort `static` können statische Member einer Klasse definiert werden. Zugriff auf statische Member wird über den `::` Operator durchgeführt (Scope Resolution Operator). Innerhalb der Klasse kann mit dem Schlüsselwort `self` auf statische Member zugegriffen werden.

```php
<?php

class Car
{
    protected static $wheel_count = 4;

    public static function checkWheels($wheel_count)
    {
        return $wheel_count == self::$wheel_count;
    }
}

echo Car::checkWheels(5) ? "richtige Reifenanzahl" : "falsche Reifenanzahl";
// Ausgabe: falsche Reifenanzahl
```

## Klassenkonstanten

Ähnlich zu statischen Member können Konstanten innerhalb einer Klasse definiert werden. Der Zugriff auf Konstanten funktioniert analog zu statischen Member über den Scope Resolution Operator (`::`).

```php
<?php

class Car
{
    const WHEELS = 4;

    function drive() {
        echo  self::WHEELS . " wheels rolling... \n";
    }
}

$car = new Car();
$car->drive(); // 4 wheels rolling...
```

## Traits

Mit Traits können Klassen einfach erweitert werden. Anders als über Vererbung kann eine Klasse von mehreren Traits erweitert werden. Von Traits können keine Instanzen gebildet werden sie dienen ausschließlich der Erweiterung von Klassen.

!!! info "Hinweis"
    `Traits` sind ein sehr nützliches Programmierkonstrukt um Klassen zu erweitern. Im Gegensatz zur klassischen Vererbung sind `Traits` sehr leichtgewichtig und können wiederkehrende Funktionalität über unterschiedliche Klassenhierarchien hinweg an Klassen bereitstellen.

Im Folgenden wird das gängige `Singleton` Design Pattern mit Hilfe eines Traits implementiert. Ein Trait wird über das Schlüsselwort `use` in die Klasse integriert:

```php
<?php

trait Singleton
{
    protected static $instance;

    public static function instance()
    {
        if (!self::$instance) {
            self::$instance = new static();
        }
        return self::$instance;
    }
}

class ExampleClass
{
    use Singleton;
}

$instance = ExampleClass::instance();
```

## Weiteres zu Klassen

PHP unterstützt noch weitere Aspekte der Objektorientierten Programmierung, welche bereits aus der Programmierung mit Java bekannt sind. Hier sind zum Beispiel `anonyme Klassen`, `abstrakte Klassen und Methoden`, `Interfaces` oder das `final` Keyword zu nennen. Die [PHP-Dokumentation](https://www.php.net/manual/en/oop5.intro.php) bietet einen guten Überblick über weitere Objektorientierte Programmiertechniken.



