---
title: Kurz – vytvoření zásobníku MEAN na virtuálním počítači SIP v Azure
description: V tomto kurzu se dozvíte, jak vytvořit stack MongoDB, Express, AngularJS a Node.js (MEAN) na virtuálním počítači s Linuxem v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9e0ed3454f11907c5f183f08fd2ec51db3384225
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80154317"
---
# <a name="tutorial-create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-virtual-machine-in-azure"></a>Kurz: Vytvoření stacku MongoDB, Express, AngularJS a Node.js (MEAN) na virtuálním počítači s Linuxem v Azure

V tomto kurzu se dozvíte, jak implementovat stack MongoDB, Express, AngularJS a Node.js (MEAN) na virtuálním počítači s Linuxem v Azure. Stack MEAN, který vytvoříte, umožňuje přidávání, odstraňování a výpis knih v databázi. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Linuxem
> * Instalace Node.js
> * Instalace MongoDB a nastavení serveru
> * Instalace Express a nastavení tras k serveru
> * Přístup k trasám pomocí AngularJS
> * Spuštění aplikace

Tento kurz používá vynesené mezizaviny příkazového příkazové číslo v rámci [prostředí Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), které se neustále aktualizuje na nejnovější verzi. Chcete-li otevřít prostředí Cloud Shell, vyberte **Vyzkoušet** v horní části libovolného bloku kódu.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).


## <a name="create-a-linux-vm"></a>Vytvoření virtuálního počítače s Linuxem

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group) a pak vytvořte virtuální počítač s Linuxem pomocí příkazu [az vm create](https://docs.microsoft.com/cli/azure/vm). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad pomocí Azure CLI vytvoří skupinu prostředků *myResourceGroupMEAN* v umístění *eastus*. Vytvoří se virtuální počítač *myVM* s klíči SSH, pokud ještě neexistují ve výchozím umístění klíčů. Pokud chcete použít konkrétní sadu klíčů, použijte možnost --ssh-key-value.

```azurecli-interactive
az group create --name myResourceGroupMEAN --location eastus
az vm create \
    --resource-group myResourceGroupMEAN \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password 'Azure12345678!' \
    --generate-ssh-keys
az vm open-port --port 3300 --resource-group myResourceGroupMEAN --name myVM
```

Po vytvoření virtuálního počítače se v Azure CLI zobrazí podobné informace jako v následujícím příkladu: 

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroupMEAN/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.72.77.9",
  "resourceGroup": "myResourceGroupMEAN"
}
```
Poznamenejte si `publicIpAddress`. Tato adresa se používá pro přístup k virtuálnímu počítači.

Pomocí následujícího příkazu vytvořte s virtuálním počítačem relaci SSH. Ujistěte se, že používáte správnou veřejnou IP adresu. V našem příkladu byla IP adresa 13.72.77.9.

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>Instalace Node.js

[Node.js](https://nodejs.org/en/) je modul runtime JavaScriptu založený na modulu JavaScript V8 v Chrome. Node.js se v tomto kurzu používá k nastavení tras Express a kontrolerů AngularJS.

Pomocí prostředí Bash, které jste otevřeli pomocí SSH, na virtuálním počítači nainstalujte Node.js.

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>Instalace MongoDB a nastavení serveru
[MongoDB](https://www.mongodb.com) ukládá data ve flexibilních dokumentech podobných formátu JSON. Pole v databázi se můžou v jednotlivých dokumentech lišit a datová struktura se může v průběhu času měnit. V naší ukázkové aplikaci přidáváme do MongoDB záznamy o knihách, které obsahují název knihy, číslo ISBN, autora a počet stran. 

1. Pomocí prostředí Bash, které jste otevřeli pomocí SSH, na virtuálním počítači nastavte klíč MongoDB.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. Aktualizujte klíč ve správci balíčků.
  
    ```bash
    sudo apt-get update
    ```

3. Nainstalujte MongoDB.

    ```bash
    sudo apt-get install -y mongodb
    ```

4. Spusťte server.

    ```bash
    sudo service mongodb start
    ```

5. Musíme nainstalovat také balíček [body-parser](https://www.npmjs.com/package/body-parser-json), který nám pomůže zpracovávat JSON předávaný v požadavcích na server.

    Nainstalujte správce balíčků npm.

    ```bash
    sudo apt-get install npm
    ```

    Nainstalujte balíček body-parser.
    
    ```bash
    sudo npm install body-parser
    ```

6. Vytvořte složku *Books* a přidejte do ní soubor *server.js*, který obsahuje konfiguraci webového serveru.

    ```javascript
    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });
    ```

## <a name="install-express-and-set-up-routes-to-the-server"></a>Instalace Express a nastavení tras k serveru

[Express](https://expressjs.com) je minimální a flexibilní architektura webových aplikací Node.js, která poskytuje funkce pro webové a mobilní aplikace. Express se v tomto kurzu používá k předávání informací o knihách do a z naší databáze MongoDB. [Mongoose](https://mongoosejs.com) poskytuje jednoduché řešení modelování dat aplikací založené na schématu. Mongoose s v tomto kurzu používá k poskytování schématu knih pro databázi.

1. Nainstalujte Express a Mongoose.

    ```bash
    sudo npm install express mongoose
    ```

2. Ve složce *Books* vytvořte složku *apps* a přidejte do ní soubor *routes.js* s definicemi tras Express.

    ```javascript
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };
    ```

3. Ve složce *apps* vytvořte složku *models* a přidejte do ní soubor *book.js* s definicí konfigurace modelu book.  

    ```javascript
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema); 
    ```

## <a name="access-the-routes-with-angularjs"></a>Přístup k trasám pomocí AngularJS

[AngularJS](https://angularjs.org) poskytuje webovou architekturu pro vytváření dynamických zobrazení ve webových aplikacích. V tomto kurzu používáme AngularJS k propojení naší webové stránky s Express a provádění akcí s naší databází knih.

1. Změňte adresář zpět *Books* na`cd ../..`Knihy ( ) a potom vytvořte složku s názvem *public* a přidejte soubor s názvem *script.js* s definovanou konfigurací řadiče.

    ```javascript
    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
        console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    ```
    
2. Ve složce *public* vytvořte soubor *index.html* s definicí webové stránky.

    ```html
    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td> 
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td> 
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>
            </tr>
            <tr ng-repeat="book in books">
              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    ```

##  <a name="run-the-application"></a>Spuštění aplikace

1. Změňte adresář zpět *Books* na`cd ..`Knihy ( ) a spusťte server spuštěním tohoto příkazu:

    ```bash
    nodejs server.js
    ```

2. Otevřete webový prohlížeč na adrese, kterou jste si pro virtuální počítač poznamenali. Například *http:\//13.72.77.9:3300*. Zobrazená stránka by měla vypadat přibližně takto:

    ![Záznam o knize](media/tutorial-mean/meanstack-init.png)

3. Do textových polí zadejte data a klikněte na **Add** (Přidat). Například:

    ![Přidání záznamu o knize](media/tutorial-mean/meanstack-add.png)

4. Po aktualizaci stránky by se měla zobrazit stránka podobná této:

    ![Výpis záznamů o knihách](media/tutorial-mean/meanstack-list.png)

5. Můžete kliknout na **Delete** (Odstranit) a odstranit záznam o knize z databáze.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pomocí stacku MEAN na virtuálním počítači s Linuxem vytvořili webovou aplikaci, která uchovává záznamy o knihách. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Linuxem
> * Instalace Node.js
> * Instalace MongoDB a nastavení serveru
> * Instalace Express a nastavení tras k serveru
> * Přístup k trasám pomocí AngularJS
> * Spuštění aplikace

Přejdete k dalšímu kurzu, kde se dozvíte, jak zabezpečit webové servery pomocí certifikátů TLS.

> [!div class="nextstepaction"]
> [Zabezpečený webový server s TLS](tutorial-secure-web-server.md)
