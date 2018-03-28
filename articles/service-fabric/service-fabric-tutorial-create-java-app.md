---
title: Creación de una aplicación Java para Service Fabric | Microsoft Docs
description: En este tutorial, aprenderá no solo a crear una aplicación Java de un servicio confiable con un front-end, sino también a crear un back-end con estado de Reliable Services con estado y a implementar la aplicación en un clúster.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: dc67de00abb2eac2eeb6e2b6bf3798e3aa210152
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-create-and-deploy-an-application-with-a-java-web-api-front-end-service-and-a-stateful-back-end-service"></a>Tutorial: Creación e implementación de una aplicación con un servicio front-end de Java Web API y un servicio back-end con estado
Este tutorial es la primera parte de una serie. Cuando termine, tendrá una aplicación de votación con un front-end web de Java que guarda los resultados de la votación en un servicio back-end con estado del clúster. Esta serie de tutoriales requiere una máquina de desarrollador de Linux o Mac OSX en funcionamiento. Si no desea crear manualmente la aplicación de votación, puede [descargar el código fuente de la aplicación terminada](https://github.com/Azure-Samples/service-fabric-java-quickstart) y pasar directamente al [Tutorial de la aplicación de ejemplo de votación](service-fabric-tutorial-create-java-app.md#walk-through-the-voting-sample-application).

![Aplicación Voting local](./media/service-fabric-tutorial-create-java-app/votingjavalocal.png)

En la primera parte de la serie, se aprende a:

> [!div class="checklist"]
> * Crear un servicio confiable de Java con estado
> * Crear un servicio de aplicación web sin estado de Java
> * Usar una comunicación remota de servicio para comunicarse con el servicio con estado
> * Implementar una aplicación en un clúster local de Service Fabric

En esta serie de tutoriales, se aprende a:
> [!div class="checklist"]
> *  Compilar una aplicación Java de Reliable Services en Service Fabric
> * [Implementar y depurar la aplicación en un clúster local](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Implementar la aplicación en un clúster de Azure](service-fabric-tutorial-java-deploy-azure.md)
> * [Configurar la supervisión y el diagnóstico para la aplicación](service-fabric-tutorial-java-elk.md)
> * [Configure CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>requisitos previos
Antes de empezar este tutorial:
- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Configure el entorno de desarrollo para [Mac](service-fabric-get-started-mac.md) o [Linux](service-fabric-get-started-linux.md). Siga las instrucciones para instalar el complemento de Eclipse, Gradle, el SDK de Service Fabric y la CLI de Service Fabric (sfctl).

## <a name="create-the-front-end-java-stateless-service"></a>Creación del servicio sin estado de Java del front-end
En primer lugar, cree el front-end web de la aplicación de votación. El servicio sin estado de Java posiciona un servidor HTTP liviano que hospeda una interfaz de usuario web con tecnología de AngularJS. Este servicio sin estado procesa las solicitudes de un usuario y las envía como una llamada a procedimiento remoto al servicio con estado para almacenar los votos. 

1. Inicie Eclipse

2. Cree un proyecto con **File (Archivo)**->**New (Nuevo)**->**Other (Otros)**->**Service Fabric**->**Service Fabric Project (Proyecto de Service Fabric)**

    ![Cuadro de diálogo de proyecto nuevo en Eclipse](./media/service-fabric-tutorial-create-java-app/create-sf-proj-wizard.png)

3. En el cuadro de diálogo **ServiceFabric Project Wizard** (Asistente para proyecto de ServiceFabric), asigne el nombre **Voting** al proyecto y haga clic en **Siguiente**

    ![Selección de un servicio sin estado de Java en el cuadro de diálogo de nuevo servicio](./media/service-fabric-tutorial-create-java-app/name-sf-proj-wizard.png) 

4. En la página **Add Service** (Agregar servicio), elija **Stateless Service** (Servicio sin estado) y asigne el nombre **VotingWeb** al servicio. Haga clic en **Finish** (Finalizar) para crear el proyecto.
   
    ![Crear un servicio sin estado]( ./media/service-fabric-tutorial-create-java-app/createvotingweb.png)

    Eclipse crea un proyecto de una aplicación y un servicio, y los muestra en el Explorador de paquetes.

    ![Explorador de paquetes de Eclipse después de la creación de una aplicación]( ./media/service-fabric-tutorial-create-java-app/eclipse-package-explorer.png)

La tabla ofrece una breve descripción de los elementos del Explorador de paquetes de la captura de pantalla anterior. 
| **Elemento del Explorador de paquetes** | **Descripción** |
| --- | --- |
| PublishProfiles | Contiene archivos JSON que describen los detalles del perfil de los clústeres tanto de Azure Service Fabric como locales. El contenido de estos archivos lo usa el complemento al implementar la aplicación. |
| Scripts | Contiene scripts de aplicaciones auxiliares que pueden utilizarse desde la línea de comandos para administrar rápidamente la aplicación con un clúster. |
| VotingApplication | Contiene la aplicación de Service Fabric que se inserta en el clúster de Service Fabric. |
| VotingWeb | Contiene los archivos de origen del servicio sin estado del front-end junto con el archivo de compilación de Gradle relacionado. |
| build.gradle | Archivo de Gradle que se usa para administrar el proyecto. |
| settings.gradle | Contiene los nombres de los proyectos de Gradle de esta carpeta. |

### <a name="add-html-and-javascript-to-the-votingweb-service"></a>Incorporación de HTML y Javascript al servicio VotingWeb
Para agregar una interfaz de usuario que pueda representar el servicio sin estado, agregue un archivo HTML en *VotingApplication/VotingWebPkg/Code*. Después, este archivo HTML lo representará el servidor HTTP liviano integrado en el servicio Java sin estado. 

1. Expanda el directorio *VotingApplication* para acceder al directorio *VotingApplication/VotingWebPkg/código*. 

2. Haga clic con el botón derecho en el directorio *Code* y, después, haga clic en **New (Nuevo)**->**Other (Otros)**

3. Cree una carpeta llamada *wwwroot* y haga clic en **Finish** (Finalizar)

    ![Crear la carpeta wwwroot en Eclipse](./media/service-fabric-tutorial-create-java-app/create-wwwroot-folder.png)

4. Agregue a **wwwroot** un archivo llamado **index.html** y pegue en él el siguiente contenido.

```html
<!DOCTYPE html>
<html>
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
<script src="http://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/0.13.4/ui-bootstrap-tpls.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
<body>

<script>
var app = angular.module('VotingApp', ['ui.bootstrap']); 
app.controller("VotingAppController", ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {
    $scope.votes = [];
    
    $scope.refresh = function () {
        $http.get('getStatelessList')
            .then(function successCallback(response) {
        $scope.votes = Object.assign(
          {},
          ...Object.keys(response.data) .
            map(key => ({[decodeURI(key)]: response.data[key]}))
        )
        },
        function errorCallback(response) {
            alert(response);
        });
    };

    $scope.remove = function (item) {            
       $http.get("removeItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };

    $scope.add = function (item) {
        if (!item) {return;}        
        $http.get("addItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });        
    };
}]);
</script>

<div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-offset-2">
                <form style="width:50% ! important;" class="center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="(key, value)  in votes">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(key)">
                            <span class="pull-left">
                                {{key}}
                            </span>
                            <span class="badge pull-right">
                                {{value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>
```

### <a name="update-the-votingwebservicejava-file"></a>Actualización del archivo VotingWebService.java
En el subproyecto **VotingWeb**, abra el archivo *VotingWeb/src/statelessservice/VotingWebService.java*. **VotingWebService** es la puerta de enlace para el servicio sin estado y es responsable de configurar el agente de escucha de comunicación de la API del front-end. 

Reemplace el contenido del método **createServiceInstanceListeners** del archivo por el siguiente y guarde los cambios.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {

    EndpointResourceDescription endpoint = this.getServiceContext().getCodePackageActivationContext().getEndpoint(webEndpointName);
    int port = endpoint.getPort();
    
    List<ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
    listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationListener(context, port)));
    return listeners;
}
```

### <a name="add-the-httpcommunicationlistenerjava-file"></a>Adición del archivo HTTPCommunicationListener.java
El agente de escucha de la comunicación HTTP actúa como el controlador que configura el servidor HTTP y expone las API que definen las acciones de votación. Haga clic con el botón derecho en el paquete *statelessservice* de la carpeta *VotingWeb/src/statelessservice* y seleccione **New->Other...->General->File** (Nuevo->Otros...->General->Archivo) y haga clic en **Next** (Siguiente).  Asignar al archivo el nombre *HttpCommunicationListener.java* y haga clic en **Finish** (Finalizar).  

Reemplace el contenido del archivo por el siguiente y guarde los cambios.  Más adelante, en [Actualización del archivo HttpCommunicationListener.java](#updatelistener_anchor), este archivo se modifica para que represente, lea y escriba los datos de votación del servicio back-end.  Por ahora, el agente de escucha simplemente devuelve el código HTML estático para la aplicación Voting.

```java
// ------------------------------------------------------------
//  Copyright (c) Microsoft Corporation.  All rights reserved.
//  Licensed under the MIT License (MIT). See License.txt in the repo root for license information.
// ------------------------------------------------------------

package statelessservice;

import com.google.gson.Gson;
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.Headers;

import java.io.File;
import java.io.OutputStream;
import java.io.FileInputStream;

import java.net.InetSocketAddress;
import java.net.URI;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.logging.Level;
import java.util.logging.Logger;

import microsoft.servicefabric.services.communication.runtime.CommunicationListener;
import microsoft.servicefabric.services.runtime.StatelessServiceContext;
import microsoft.servicefabric.services.client.ServicePartitionKey;
import microsoft.servicefabric.services.remoting.client.ServiceProxyBase;
import microsoft.servicefabric.services.communication.client.TargetReplicaSelector;
import system.fabric.CancellationToken;

public class HttpCommunicationListener implements CommunicationListener {

    private static final Logger logger = Logger.getLogger(HttpCommunicationListener.class.getName());
    
    private static final String HEADER_CONTENT_TYPE = "Content-Type";
    private static final int STATUS_OK = 200;
    private static final int STATUS_NOT_FOUND = 404; 
    private static final int STATUS_ERROR = 500;
    private static final String RESPONSE_NOT_FOUND = "404 (Not Found) \n";
    private static final String MIME = "text/html";  
    private static final String ENCODING = "UTF-8";
    
    private static final String ROOT = "wwwroot/";
    private static final String FILE_NAME = "index.html";
    private StatelessServiceContext context;
    private com.sun.net.httpserver.HttpServer server;
    private ServicePartitionKey partitionKey;
    private final int port;

    public HttpCommunicationListener(StatelessServiceContext context, int port) {
        this.partitionKey = new ServicePartitionKey(0); 
        this.context = context;
        this.port = port;
    }

    // Called by openAsync when the class is instantiated 
    public void start() {
        try {
            logger.log(Level.INFO, "Starting Server");
            server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(this.port), 0);
        } catch (Exception ex) {
            logger.log(Level.SEVERE, null, ex);
            throw new RuntimeException(ex);
        }
        
        // Responsible for rendering the HTML layout described in the previous step
        server.createContext("/", new HttpHandler() {
            @Override
            public void handle(HttpExchange t) {
                try {
                    File file = new File(ROOT + FILE_NAME).getCanonicalFile();
    
                    if (!file.isFile()) {
                      // Object does not exist or is not a file: reject with 404 error.
                      t.sendResponseHeaders(STATUS_NOT_FOUND, RESPONSE_NOT_FOUND.length());
                      OutputStream os = t.getResponseBody();
                      os.write(RESPONSE_NOT_FOUND.getBytes());
                      os.close();
                    } else {    
                      Headers h = t.getResponseHeaders();
                      h.set(HEADER_CONTENT_TYPE, MIME);
                      t.sendResponseHeaders(STATUS_OK, 0);              
    
                      OutputStream os = t.getResponseBody();
                      FileInputStream fs = new FileInputStream(file);
                      final byte[] buffer = new byte[0x10000];
                      int count = 0;
                      while ((count = fs.read(buffer)) >= 0) {
                        os.write(buffer,0,count);
                      }
                      
                      fs.close();
                      os.close();
                    }  
                } catch (Exception e) {
                    logger.log(Level.WARNING, null, e);
                }
            }
        });

        /*
        [Replace this entire comment block in the 'Connect the services' section]
        */
        
        server.setExecutor(null);
        server.start();
    }
    
    //Helper method to parse raw HTTP requests
    private Map<String, String> queryToMap(String query){
        Map<String, String> result = new HashMap<String, String>();
        for (String param : query.split("&")) {
            String pair[] = param.split("=");
            if (pair.length>1) {
                result.put(pair[0], pair[1]);
            }else{
                result.put(pair[0], "");
            }
        }
        return result;
    }

    //Called closeAsync when the service is shut down
    private void stop() {
        if (null != server)
            server.stop(0);
    }

    //Called by the Service Fabric runtime when this service is created on a node
    @Override
    public CompletableFuture<String> openAsync(CancellationToken cancellationToken) {
        this.start();
                    logger.log(Level.INFO, "Opened Server");
        String publishUri = String.format("http://%s:%d/", this.context.getNodeContext().getIpAddressOrFQDN(), port);
        return CompletableFuture.completedFuture(publishUri);
    }

    //Called by the Service Fabric runtime when the service is shut down
    @Override
    public CompletableFuture<?> closeAsync(CancellationToken cancellationToken) {
        this.stop();
        return CompletableFuture.completedFuture(true);
    }

    //Called by the Service Fabric runtime to forcibly shut this listener down
    @Override
    public void abort() {
        this.stop();
    }
}
```

### <a name="configure-the-listening-port"></a>Configuración del puerto de escucha
Cuando se crea el servicio front-end VotingWebService, Service Fabric selecciona un puerto en el que el servicio realiza la escucha.  Dado que VotingWebService actúa como front-end de esta aplicación y acepta tráfico externo, vamos enlazar dicho servicio a un puerto fijo conocido. En el Explorador de paquetes, abra *VotingWebService/VotingWebServicePkg/ServiceManifest.xml*.  Busque el recurso **Endpoint** en la sección **Resources** y cambie el valor de **Port** (Puerto) a 8080, o a cualquier otro puerto. Para implementar y ejecutar la aplicación localmente, el puerto de escucha de la aplicación debe estar abierto y disponible en el equipo. Pegue el siguiente fragmento de código en la etiqueta **ServiceManifest**.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
        <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
    </Endpoints>
  </Resources>
```  

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Adición de un servicio back-end con estado a la aplicación
Una vez que se ha completado el esqueleto del servicio Java Web API, pasemos a completar el servicio back-end con estado.

Service Fabric permite almacenar de forma coherente y confiable su estado justo dentro de su servicio mediante Reliable Collections. Las colecciones confiables son un conjunto de clases de colección de gran disponibilidad y confiabilidad. Cualquiera que haya usado colecciones de Java sabrá usar estas clases.

1. En el Explorador de paquetes, haga clic con el botón derecho en **Voting** en el proyecto de la aplicación y elija **Service Fabric > Add Service Fabric Service** (Service Fabric > Agregar servicio Service Fabric).
   
2. En el cuadro de diálogo **Add Service** (Agregar servicio), elija **Stateful Service** (Servicio con estado), asigne el nombre **VotingData** al servicio y haga clic en **Add Service** (Agregar servicio). 

    ![Incorporación de un nuevo servicio a una aplicación existente](./media/service-fabric-tutorial-create-java-app/addstatefuljava.png)

    Una vez creado el proyecto de servicio, tendrá dos servicios en la aplicación. Mientras continúa la creación de la aplicación, puede agregar más servicios de la misma forma. Cada uno puede tener versiones y actualizaciones independientes.

3. Eclipse crea un proyecto de un servicio y lo muestra en el Explorador de paquetes.

    ![Explorador de soluciones](./media/service-fabric-tutorial-create-java-app/packageexplorercompletejava.png)

### <a name="add-the-votingdataservicejava-file"></a>Agregue el archivo VotingDataService.java

El archivo *VotingDataService.java* tiene los métodos que contienen la lógica para recuperar, agregar y quitar votos de las colecciones confiables. Agregue los siguientes métodos a la clase **VotingDataService** del archivo *VotingDataService/src/statefulservice/VotingDataService.java* creado.

```java
package statefulservice;

import java.util.HashMap;
import java.util.ArrayList;
import java.util.List; 
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.atomic.AtomicInteger;

import microsoft.servicefabric.services.communication.runtime.ServiceReplicaListener;

import microsoft.servicefabric.services.runtime.StatefulService;

import microsoft.servicefabric.services.remoting.fabrictransport.runtime.FabricTransportServiceRemotingListener;

import microsoft.servicefabric.data.ReliableStateManager;
import microsoft.servicefabric.data.Transaction;
import microsoft.servicefabric.data.collections.ReliableHashMap;
import microsoft.servicefabric.data.utilities.AsyncEnumeration;
import microsoft.servicefabric.data.utilities.KeyValuePair;

import system.fabric.StatefulServiceContext; 

import rpcmethods.VotingRPC; 

class VotingDataService extends StatefulService implements VotingRPC {
    private static final String MAP_NAME = "votesMap";
    private ReliableStateManager stateManager;
    
    protected VotingDataService (StatefulServiceContext statefulServiceContext) {
        super (statefulServiceContext);
    }

    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        this.stateManager = this.getReliableStateManager();
        ArrayList<ServiceReplicaListener> listeners = new ArrayList<>();
        
        listeners.add(new ServiceReplicaListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        
        return listeners;
    }
    
    // Method that will be invoked via RPC from the front end to retrieve the complete set of votes in the map
    public CompletableFuture<HashMap<String,String>> getList() {
        HashMap<String, String> tempMap = new HashMap<String, String>();

        try {                    

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();
                        
            Transaction tx = stateManager.createTransaction();
            AsyncEnumeration<KeyValuePair<String, String>> kv = votesMap.keyValuesAsync(tx).get();
            while (kv.hasMoreElementsAsync().get()) {
                KeyValuePair<String, String> k = kv.nextElementAsync().get();
                tempMap.put(k.getKey(), k.getValue()); 
            }
            
            tx.close();                    
            

        } catch (Exception e) {
            e.printStackTrace();
        }  
        
        return CompletableFuture.completedFuture(tempMap);
    }
    
    // Method that will be invoked via RPC from the front end to add an item to the votes list or to increase the
    // vote count for a particular item
    public CompletableFuture<Integer> addItem(String itemToAdd) {
        AtomicInteger status = new AtomicInteger(-1); 

        try {
            
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();                    
            
            Transaction tx = stateManager.createTransaction();
            votesMap.computeAsync(tx, itemToAdd, (k, v) -> {
                if (v == null) {
                    return "1";
                }
                else {
                    int numVotes = Integer.parseInt(v);
                    numVotes = numVotes + 1;                            
                    return Integer.toString(numVotes);
                }
            }).get(); 
            
            tx.commitAsync().get();
            tx.close(); 

            status.set(1);                            
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }
    
    // Method that will be invoked via RPC from the front end to remove an item
    public CompletableFuture<Integer> removeItem(String itemToRemove) {
        AtomicInteger status = new AtomicInteger(-1); 
        try {
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();
            
            Transaction tx = stateManager.createTransaction();
            votesMap.removeAsync(tx, itemToRemove).get();
            tx.commitAsync().get();
            tx.close();                    
            
            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }
        
        return CompletableFuture.completedFuture(new Integer(status.get()));
    }
    
}
```

## <a name="create-the-communication-interface-to-your-application"></a>Creación de la interfaz de comunicación con la aplicación 
Ahora se crea el esqueleto del servicio front-end sin estado y del servicio back-end. El siguiente paso es conectar los dos servicios. Tanto el servicio front-end como el back-end usan una interfaz denominada VotingRPC que define las operaciones de la aplicación Voting. Esta interfaz la implementan los servicios front-end y back-end para habilitar las llamadas a procedimiento remoto (RPC) entre ambos servicios. Dado que Eclipse no admite la adición de subproyectos de Gradle, el paquete que contiene esta interfaz debe agregarse manualmente. 

1. Haga clic con el botón derecho en el proyecto **Voting** en el Explorador de paquetes y haga clic en **New -> Other...** (Nuevo -> Otros...)

2. En el asistente, haga clic en **General -> Folder** (General -> Folder) y asigne un nombre a la carpeta **VotingRPC/src/rpcmethods** 

    ![Crear paquete VotingRPC](./media/service-fabric-tutorial-create-java-app/createvotingrpcpackage.png)

3. En *Voting/VotingRPC/src/rpcmethods*, cree un archivo llamado *VotingRPC.java* y pegue en él el siguiente contenido. 

    ```java
    package rpcmethods; 
    
    import java.util.ArrayList;
    import java.util.concurrent.CompletableFuture;
    import java.util.List;
    import java.util.HashMap;
    
    import microsoft.servicefabric.services.remoting.Service;
    
    public interface VotingRPC extends Service {
        CompletableFuture<HashMap<String, String>> getList();
    
        CompletableFuture<Integer> addItem(String itemToAdd);
    
        CompletableFuture<Integer> removeItem(String itemToRemove);
    }
    ``` 

4. Cree un archivo denominado *build.gradle* en el directorio *Voting/VotingRPC* y pegue en él lo siguiente. Este archivo de Gradle se usa para generar y crear el archivo jar que importan los otros servicios. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/out"
        delete "${projectDir}/VotingRPC.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
                attributes(
                'Main-Class': 'rpcmethods.VotingRPC')
            baseName "VotingRPC"
            destinationDir = file('./')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar'
    ```

5. En el archivo *Voting/settings.gradle*, agregue una línea para incluir el proyecto recién creado en la compilación. 

    ```gradle 
    include ':VotingRPC'
    ```

6. En el archivo *Voting/VotingWebService/src/statelessservice/HttpCommunicationListener.java*, reemplace el bloque de comentario por lo siguiente.  

    ```java
    server.createContext("/getStatelessList", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {                    
                t.sendResponseHeaders(STATUS_OK,0);
                OutputStream os = t.getResponseBody();
                
                HashMap<String,String> list = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").getList().get();
                String json = new Gson().toJson(list);
                os.write(json.getBytes(ENCODING));                   
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    
    server.createContext("/removeItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                OutputStream os = t.getResponseBody();
                URI r = t.getRequestURI();     
    
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToRemove = params.get("item");                    
                
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").removeItem(itemToRemove).get();
                
                if (num != 1) 
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
    
        }
    });
    
    server.createContext("/addItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                URI r = t.getRequestURI();
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToAdd = params.get("item");
                
                OutputStream os = t.getResponseBody();
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").addItem(itemToAdd).get();
                if (num != 1) 
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    ```
7. Agregue la instrucción de importación pertinente al principio del archivo *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java*. 

    ```java
    import rpcmethods.VotingRPC; 
    ```

En esta fase, la funcionalidad de las interfaces de RPC, back-end y front-end está completa. La siguiente fase consiste en configurar correctamente los scripts de Gradle antes de implementar en un clúster de Service Fabric. 

## <a name="walk-through-the-voting-sample-application"></a>Tutorial de la aplicación de ejemplo de votación
La aplicación de votación consta de dos servicios:
- Servicio front-end web (VotingWeb): un servicio front-end de Java que ofrece servicio a la página web y expone las API para comunicarse con el servicio back-end.
- Servicio back-end (VotingDataService) - servicio de web de Java que define los métodos que se invocan a través de llamadas a procedimiento remoto (RPC) para conservar los votos.

![Diagrama de la aplicación](./media/service-fabric-tutorial-create-java-app/walkthroughjavavoting.png)

Al realizar una acción en la aplicación (agregar un elemento, votar o quitar un elemento) se producen los siguientes eventos:
1. JavaScript envía la solicitud pertinente a la API web del servicio front-end web en forma de solicitud HTTP.

2. El servicio front-end web usa la funcionalidad integrada de comunicación remota de Service Fabric para localizar y reenviar la solicitud al servicio back-end. 

3. El servicio back-end define métodos que actualizan el resultado en un diccionario confiable. El contenido de este diccionario se replica en varios nodos del clúster y se conserva en el disco. Todos los datos de la aplicación se almacenan en el clúster. 

## <a name="configure-gradle-scripts"></a>Configuración de scripts de Gradle 

En esta sección se configuran los scripts de Gradle del proyecto. 

1. Reemplace el contenido del archivo *Voting/build.gradle* por el siguiente.

    ```gradle 
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    subprojects {
        apply plugin: 'java'
    } 
        
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

2. Reemplace el contenido del archivo *Voting/VotingWeb/build.gradle*.

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/VotingWeb.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
        
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingWebPkg/Code/lib")
            include('lib*.so')
        }
    }
    
    compileJava.dependsOn(explodeDeps)
    
    jar {
        from configurations.compile.collect {(it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
            attributes(
                'Main-Class': 'statelessservice.VotingWebServiceHost')
            baseName "VotingWeb"
            destinationDir = file('../VotingApplication/VotingWebPkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ``` 

3. Reemplace el contenido del archivo *Voting/VotingData/build.gradle*. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/VotingDataService.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
        
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    compileJava.dependsOn(explodeDeps)
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingDataServicePkg/Code/lib")
            include('lib*.so')
        }
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
            attributes('Main-Class': 'statefulservice.VotingDataServiceHost')
    
            baseName "VotingDataService"
            destinationDir = file('../VotingApplication/VotingDataServicePkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

## <a name="deploy-application-to-local-cluster"></a>Implementación de una aplicación en un clúster local
En este momento, la aplicación está lista para implementarse en un clúster local de Service Fabric.

1. En el explorador de paquetes, haga clic con el botón derecho en el proyecto **Voting** y haga clic en **Service Fabric -> Build Application** (Service Fabric -> Compilar Application).

2. Ejecute el clúster local de Service Fabric. Este paso depende del entorno de desarrollo (Mac o Linux).

    Si utiliza un equipo Mac, ejecute el clúster local con el siguiente comando: reemplace el comando que se usa en el **- v** parámetro por la ruta de acceso a su propia área de trabajo.

    ```bash
    docker run -itd -p 19080:19080 -p 8080:8080 -p --name sfonebox servicefabricoss/service-fabric-onebox
    ``` 

    Sin embargo, si usa un equipo Linux, inicie el clúster local con el siguiente comando: 

    ```bash 
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

4. En el Explorador de paquetes de Eclipse, haga clic con el botón derecho en el proyecto **Voting** y haga clic en **Service Fabric -> Publish Application (Publicar aplicación)...** 
5. En la ventana **Publish Application** (Publicar aplicación), seleccione **Local.json** en la lista desplegable y haga clic en **Publish** (Publicar).
6. Desde al explorador web acceda a **http://localhost:8080** para ver la aplicación en ejecución en el clúster local de Service Fabric. 

## <a name="next-steps"></a>Pasos siguientes
En esta parte del tutorial, ha aprendido a:

> [!div class="checklist"]
> * Crear un servicio Java en forma de servicio confiable con estado
> * Crear un servicio Java en forma de servicio confiable sin estado
> * Agregar una interfaz de Java para controlar las llamadas a procedimiento remoto (RPC) entre los servicios
> * Configurar scripts de Gradle
> * Compilar e implementar una aplicación en un clúster local de Service Fabric

Avance hasta el siguiente tutorial:
> [!div class="nextstepaction"]
> [Depuración y registro de aplicaciones en un clúster local](service-fabric-tutorial-debug-log-local-cluster.md)
