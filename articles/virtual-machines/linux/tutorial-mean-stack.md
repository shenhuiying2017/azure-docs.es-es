---
title: "Creación de una pila MEAN en una máquina virtual Linux en Azure | Microsoft Docs"
description: "Aprenda a crear una pila de MongoDB, Express, AngularJS y Node.js (MEAN) en una máquina virtual Linux en Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 1d74ead08dfb63276afb08bdcb7f4e3e3db5bfd3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-vm-in-azure"></a>Creación de una pila de MongoDB, Express, AngularJS y Node.js (MEAN) en una máquina virtual Linux en Azure

Este tutorial muestra cómo implementar una pila de MongoDB, Express, AngularJS y Node.js (MEAN) en una máquina virtual Linux en Azure. La pila MEAN que crea permite agregar, eliminar y enumerar los libros en una base de datos. Aprenderá a:

> [!div class="checklist"]
> * Creación de una máquina virtual Linux
> * Instalación de Node.js
> * Instalación de MongoDB y configuración del servidor
> * Instalación de Express y configuración de rutas para el servidor
> * Acceso a las rutas con AngularJS
> * Ejecución de la aplicación

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).


## <a name="create-a-linux-vm"></a>Creación de una máquina virtual Linux

Cree un grupo de recursos con el comando [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) y una máquina virtual Linux con el comando [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

En el ejemplo siguiente, se usa la CLI de Azure para crear un grupo de recursos denominado *myResourceGroupMEAN* en la ubicación *eastus*. También se crea una máquina virtual denominada *myVM* con claves SSH, si aún no existen en una ubicación de claves predeterminada. Para utilizar un conjunto específico de claves, utilice la opción --ssh-key-value.

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

Cuando se haya creado la máquina virtual, la CLI de Azure mostrará información similar a la del ejemplo siguiente: 

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
Anote el valor de `publicIpAddress`. Esta dirección se utiliza para tener acceso a la máquina virtual.

Use el siguiente comando para crear una sesión de SSH con la máquina virtual. Asegúrese de usar la dirección IP pública correcta. En el ejemplo anterior, la dirección IP era 13.72.77.9.

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>Instalación de Node.js

[Node.js](https://nodejs.org/en/) es un entorno de tiempo de ejecución de JavaScript integrado en el motor de V8 JavaScript de Chrome. Node.js se utiliza en este tutorial para configurar las rutas de Express y los controladores de AngularJS.

En la máquina virtual, mediante el shell de bash que abrió con SSH, instale Node.js.

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>Instalación de MongoDB y configuración del servidor
[MongoDB](http://www.mongodb.com) almacena los datos en documentos flexibles, similares a JSON. Los campos de una base de datos pueden variar entre documentos y la estructura de datos puede cambiarse con el tiempo. En nuestra aplicación de ejemplo, vamos a agregar registros de libros para MongoDB que contienen el nombre del libro, el número de ISBN, el autor y el número de páginas. 

1. En la máquina virtual, con el shell de bash que abrió con SSH, establezca la clave de MongoDB.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. Actualice el administrador de paquetes con la clave.
  
    ```bash
    sudo apt-get update
    ```

3. Instale MongoDB.

    ```bash
    sudo apt-get install -y mongodb
    ```

4. Inicie el servidor.

    ```bash
    sudo service mongodb start
    ```

5. También es necesario instalar el paquete [body-parser](https://www.npmjs.com/package/body-parser-json) para ayudarnos a procesar el JSON que se pasa en las solicitudes al servidor.

    Instale el administrador de paquetes npm.

    ```bash
    sudo apt-get install npm
    ```

    Instale el paquete del analizador de cuerpos.
    
    ```bash
    sudo npm install body-parser
    ```

6. Cree una carpeta denominada *Books* y agréguele un archivo denominado *server.js* que contenga la configuración para el servidor web.

    ```node.js
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

## <a name="install-express-and-set-up-routes-to-the-server"></a>Instalación de Express y configuración de rutas para el servidor

[Express](https://expressjs.com) es una plataforma mínima y flexible para aplicaciones web que proporciona características para aplicaciones web y móviles. Express se utiliza en este tutorial para pasar la información de los libros hacia y desde la base de datos de MongoDB. [Mongoose](http://mongoosejs.com) proporciona una solución sencilla, basada en el esquema para modelar los datos de las aplicaciones. Mongoose se utiliza en este tutorial para proporcionar un esquema de libro para la base de datos.

1. Instale Express y Mongoose.

    ```bash
    sudo npm install express mongoose
    ```

2. En la carpeta *Books*, cree una carpeta denominada *apps* y agregue un archivo denominado *routes.js* con las rutas de Express definidas.

    ```node.js
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

3. En la carpeta *apps*, cree una carpeta denominada *models* y agregue un archivo denominado *books.js* con la configuración del modelo de libro definida.  

    ```node.js
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

## <a name="access-the-routes-with-angularjs"></a>Acceso a las rutas con AngularJS

[AngularJS](https://angularjs.org) proporciona una plataforma web para crear vistas dinámicas en las aplicaciones web. En este tutorial, usamos AngularJS para conectar nuestra página web con Express y realizar acciones en nuestra base de datos de libros.

1. Cambie el directorio de nuevo a *Books* (`cd ../..`) y, a continuación, cree una carpeta denominada *public* y agregue un archivo denominado *script.js* con la configuración del controlador definida.

    ```node.js
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
    
2. En la carpeta *public*, cree un archivo denominado *index.html* con la página web definida.

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

##  <a name="run-the-application"></a>Ejecución de la aplicación

1. Cambie el directorio de nuevo a *books* (`cd ..`) e inicie el servidor ejecutando este comando:

    ```bash
    nodejs server.js
    ```

2. Abra un explorador web con la dirección que registró para la máquina virtual. Por ejemplo, *http://13.72.77.9:3300*. Debe ver algo parecido a la página siguiente:

    ![Registro de libro](media/tutorial-mean/meanstack-init.png)

3. Escriba datos en los cuadros de texto y haga clic en **Agregar**. Por ejemplo:

    ![Agregar registro de libro](media/tutorial-mean/meanstack-add.png)

4. Después de actualizar la página, debería ver algo parecido a esta página:

    ![Agregar registros de libro](media/tutorial-mean/meanstack-list.png)

5. Puede hacer clic en **Eliminar** y quitar el registro de libro de la base de datos.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, creó una aplicación web que realiza un seguimiento de los registros de libros con una pila MEAN en una máquina virtual Linux. Ha aprendido a:

> [!div class="checklist"]
> * Creación de una máquina virtual Linux
> * Instalación de Node.js
> * Instalación de MongoDB y configuración del servidor
> * Instalación de Express y configuración de rutas para el servidor
> * Acceso a las rutas con AngularJS
> * Ejecución de la aplicación

Pase al siguiente tutorial para aprender a proteger servidores web con certificados SSL.

> [!div class="nextstepaction"]
> [Protección de un servidor web con SSL](tutorial-secure-web-server.md)
