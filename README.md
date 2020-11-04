Feed
======

Library for create xml feeds
- [Heureka](http://www.heureka.cz/)
- [Zbozi](http://www.zbozi.cz/)

in [Nette](http://nette.org)

Installation
------------

The best way to install Mk/Feed is using  [Composer](http://getcomposer.org/):

Nette 3
```sh
$ composer require jakercz/feed
```
Nette 2.4
```sh
$ composer require makr/feed
```

Configuration
-------------

.neon
```yml
extensions:
	feed: Mk\Feed\DI\FeedExtension

feed:
	exportsDir: '%wwwDir%'
    exports   : 
        heureka: \App\Models\HeurekaGenerator
        zbozi  : \App\Models\ZboziGenerator
```
Config
------

For run is used [Kdyby/Console](https://github.com/kdyby/console)
Read how to setup [Kdyby/Console](https://github.com/Kdyby/Console/blob/master/docs/en/index.md)

```sh
php index.php
```

After successful installation display:

```sh
Available commands:
Feed:export     Export products
	
```

Usage
-----

For each feed (heureka, zbozi) you must create one factory (IGenerator)


```php
<?php

namespace App\Model;

use Mk\Feed\Generators\Zbozi\ExtraMessage;
use Mk\Feed\Generators\Zbozi\Generator;
use Mk\Feed\Generators\Zbozi\Item;

class ZboziGenerator extends Generator {
    function generate()
    {

        $items = [
            $this->createItem(),
            $this->createItem(),
            $this->createItem(),
            $this->createItem(),
        ];

        foreach ($items as $item) {
            $this->addItem($item);
        }
    }

    protected function createItem()
    {
        $item = new Item();
        $item->setProductName('Name')   #název nabídky, povinné, doporučená délka 70 znaků
            ->setDescription('Description') #popis nabídky, povinné (doporučená délka do 1000 znaků)
            ->setUrl('http://www.seznam.cz') #adresa nabídky v eshopu, povinné
            ->setPriceVat(10) #cena, povinné; číselná hodnota, max. dvě desetinná místa
            ->setDeliveryDate(0) #dostupnost, povinné (celé číslo nebo datum ve formátu RRRR-MM-DD)
            ->setItemId(123) #identifikátor nabídky v eshopu, nepovinné (alfanumerické znaky)
            ->setEan(87458965) #kód obchodní položky (čárový kód), nepovinné
            ->setIsbn('978-1-78038-067-4') #identifikační číslo knihy, nepovinné
            ->setProductNo('PRO1548') #produktový kód výrobce, nepovinné
            ->setItemGroupId(10) #označení skupiny nabídek, nepovinné
            ->setManufacturer('Adidas') #výrobce produktu, nepovinné
            ->setBrand('Nike') #značka produktu, nepovinné
            ->setCategoryId(1) #ID kategorie Zboží.cz, nepovinné
            ->setProduct('Cerny') #název nabídky ve výsledcích vyhledávání, např. "+ dárek zdarma", nepovinné
            ->setVisibility(true) #zobrazování nabídky na Zboží.cz
            ->setCustomLabel('neco') #dodatečné označení nabídky, vytvoří skupinu - kolekce, sezoni akce
            ->setCustomLabel1('nic') #dodatečné označení nabídky, vytvoří skupinu - kolekce, sezoni akce
            ->setMaxCpc(10) #maximální cena za proklik
            ->setMaxCpcSearch(10.2) #maximální cena za proklik pro nezařazené nabídky
            ->setProductLine('iPod | iPod Touch') #produktová řada
            ->setListPrice(999) #doporučená koncová prodejní cena
            ->setReleaseDate(new \DateTime()); #datum oficiálního zahájení prodeje v ČR

        #category text
         $item->addCategoryText('Kategorie | Subkategorie');
         $item->addCategoryText('Kategorie | Subkategorie1');

        #images
         $item->addImage('http://placehold.it/350x150'); #adresa obrázku, nepovinné, doporučujeme uvádět; značku je možné opakovat
         $item->addImage('http://placehold.it/350x150');
         $item->addImage('http://placehold.it/350x150');

        #extra messages
         $item->addExtraMessage(ExtraMessage::EXTENDED_WARRANTY); #doplňkové informace o nabídce, @see http://napoveda.seznam.cz/cz/zbozi/specifikace-xml-pro-obchody/specifikace-xml-feedu/#EXTRA_MESSAGE
         $item->addExtraMessage(ExtraMessage::FREE_DELIVERY);

        #shops
         $item->addShopDepot(1234); #výdejní místo pro okamžitý odběr
         $item->addShopDepot(5678);

        #parameters
         $item->addParameter('Barva', 'Hnědá'); #parametry nabídky
         $item->addParameter('Váha', '10', 'Kg');
        
        return $item;
    }

}
```

Categories helper
----------------------

For getting available categories you can call CategoryHelper
```

$categories = new \Mk\Feed\Generators\Zbozi\CategoriesHelper(); #first parameter can be cache storage for caching results

dump($categories->getCategories());
```

Print available config
----------------------
```sh
$ php index.php Feed:export -s
```

Execute config
----------------------
```sh
$ php index.php Feed:export
```

Inspired by [Trejjam/Export](https://github.com/Trejjam/Export)
