---
title: "Compilación e implementación de una aplicación de API de Java en el Servicio de aplicaciones de Azure"
description: "Aprenda a crear un paquete de aplicación de API de Java y a implementarlo en el Servicio de aplicaciones de Azure."
services: app-service\api
documentationcenter: java
author: rmcmurray
manager: erikre
editor: tdykstra
ms.assetid: 8d21ba5f-fc57-4269-bc8f-2fcab936ec22
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: get-started-article
ms.date: 12/22/2016
ms.author: rachelap;robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 26f2fba953870564292ef3e986a3151373f6babf


---
# <a name="build-and-deploy-a-java-api-app-in-azure-app-service"></a>Compilación e implementación de una aplicación de API de Java en el Servicio de aplicaciones de Azure
[!INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

En este tutorial se muestra cómo crear una aplicación de Java e implementarla en las aplicaciones de API del Servicio de aplicaciones de Azure mediante [Git]. Las instrucciones que se indican en este tutorial se pueden seguir en cualquier sistema operativo que sea capaz de ejecutar Java. El código de este tutorial está compilado mediante [Maven]. [Jax-RS] se utiliza para crear el servicio RESTful y se genera a partir de la especificación de metadatos de [Swagger] mediante el [Editor de Swagger].

## <a name="prerequisites"></a>Requisitos previos
1. [Kit para desarrolladores de Java 8] \(o posterior)
2. [Maven] instalado en el equipo de desarrollo
3. [Git] instalado en el equipo de desarrollo
4. Una suscripción de [evaluación gratuita] o de pago a [Microsoft Azure]
5. Una aplicación de prueba HTTP como [Postman]

## <a name="scaffold-the-api-using-swaggerio"></a>Aplicación de la técnica scaffolding a la API mediante Swagger.IO
Con el editor en línea de swagger.io, puede escribir código JSON o YAML de Swagger que representa la estructura de su API. Cuando haya diseñado el área de superficie de API, puede exportar el código en una diversidad de plataformas y marcos. En la siguiente sección se modificará el código con scaffold para incluir la funcionalidad de simulacro. 

Esta demostración comenzará con un cuerpo JSON de Swagger que se pegará en el editor de swagger.io, que luego se utilizará para generar código mediante el uso de JAX-RS para acceder a un punto de conexión de API de REST. A continuación, modificará el código con scaffold para devolver datos ficticios y simular una API de REST creada sobre un mecanismo de persistencia de datos.  

1. Copie el siguiente código JSON de Swagger en el Portapapeles:
   
        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }
2. Vaya al [editor en línea de Swagger]. Una vez allí, haga clic en el elemento de menú **Archivo -> Pegar JSON**.
   
    ![Elemento de menú Pegar JSON][paste-json]
3. Pegue el código JSON de Swagger de lista de contactos que copió anteriormente. 
   
    ![Pegado de código JSON en Swagger][pasted-swagger]
4. Vea las páginas de documentación y el resumen de la API representado en el editor. 
   
    ![Ver documentos generados de Swagger][view-swagger-generated-docs]
5. Seleccione la opción de menú **Generar servidor -> JAX RS** para aplicar la técnica scaffolding al código del lado servidor que modificará más adelante para agregar la implementación ficticia. 
   
    ![Generar elemento de menú de código][generate-code-menu-item]
   
    Una vez que se genere el código, se le proporcionará un archivo ZIP para descargar. Este archivo contiene el código al que el generador de código de Swagger le ha aplicado la técnica scaffolding y todos los scripts de compilación asociados. Descomprima la biblioteca completa en un directorio de su estación de trabajo de desarrollo. 

## <a name="edit-the-code-to-add-api-implementation"></a>Edición del código para agregar la implementación de API
En esta sección va a reemplazar la implementación en el lado servidor del código generado por Swagger con el código personalizado. El nuevo código devolverá una listaDeMatrices de entidades de contactos para el cliente que realiza la llamada. 

1. Abra el archivo de modelo *Contact.java*, ubicado en la carpeta *src/gen/java/io/swagger/model*, mediante [código de Visual Studio] o en el editor de texto de su elección. 
   
    ![Abrir archivo de modelo de contacto][open-contact-model-file]
2. Agregue el siguiente constructor a la clase **Contact** . 
   
        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }
3. Abra el archivo de implementación del servicio *ContactsApiServiceImpl.java*, ubicado en la carpeta *src/main/java/io/swagger/api/impl*, mediante [código de Visual Studio] o en el editor de texto de su elección.
   
    ![Abrir archivo de código de servicio de contacto][open-contact-service-code-file]
4. Sobrescriba el código del archivo con este nuevo código para agregar una implementación ficticia al código de servicio. 
   
        package io.swagger.api.impl;
   
        import io.swagger.api.*;
        
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
               
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;
   
        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
   
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
   
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
   
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
   
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }
5. Abra un símbolo del sistema y cambie el directorio a la carpeta raíz de la aplicación.
6. Ejecute el siguiente comando de Maven para compilar el código y ejecutarlo localmente mediante el servidor de aplicaciones de Jetty. 
   
        mvn package jetty:run
7. Debería ver que la ventana de comandos refleja que Jetty ha iniciado el código en el puerto 8080. 
   
    ![Abrir archivo de código de servicio de contacto][run-jetty-war]
8. Utilice [Postman] para realizar una solicitud al método de API "obtener todos los contactos" en http://localhost:8080/api/contacts.
   
    ![Llamar a la API de contactos][calling-contacts-api]
9. Utilice [Postman] para realizar una solicitud al método de API "obtener contacto específico" ubicado en http://localhost:8080/api/contacts/2.
   
    ![Llamar a la API de contactos][calling-specific-contact-api]
10. Por último, compile el archivo WAR de Java (archivo Web) ejecutando el siguiente comando de Maven en la consola. 
    
         mvn package war:war
11. Una vez creado el archivo WAR, se colocará en la carpeta **target** . Vaya a la carpeta de **destino** y cambie el nombre del archivo WAR por **ROOT.war**. (Asegúrese de que las mayúsculas y minúsculas coinciden con este formato).
    
          rename swagger-jaxrs-server-1.0.0.war ROOT.war
12. Por último, ejecute los siguientes comandos desde la carpeta raíz de la aplicación para crear una carpeta **deploy** que va a utilizar para implementar el archivo WAR en Azure. 
    
          mkdir deploy
          mkdir deploy\webapps
          copy target\ROOT.war deploy\webapps
          cd deploy

## <a name="publish-the-output-to-azure-app-service"></a>Publicación del resultado en el Servicio de aplicaciones de Azure
En esta sección aprenderá a crear una nueva aplicación de API mediante el Portal de Azure, a prepararla para hospedar aplicaciones Java y a implementar el archivo WAR recién creado en el Servicio de aplicaciones de Azure para ejecutar la nueva aplicación de API. 

1. Cree una nueva aplicación de API en [Azure Portal]; para ello, haga clic en el elemento de menú **Nuevo -> Web y móvil -> Aplicación de API**, escriba los detalles de la aplicación y haga clic en **Crear**.
   
    ![Crear una nueva aplicación de API][create-api-app]
2. Una vez creada la aplicación de API, abra la hoja **Configuración** de la aplicación y, después, haga clic en el elemento de menú **Configuración de la aplicación**. Seleccione las últimas versiones de Java entre las opciones disponibles, seleccione la aplicación Tomcat más reciente en el menú **Contenedor web** y después haga clic en **Guardar**.
   
    ![Configurar Java en la hoja de aplicaciones de API][set-up-java]
3. Haga clic en el elemento **Credenciales de implementación** del menú de configuración y especifique el nombre de usuario y la contraseña que desee usar para publicar archivos en una aplicación de API. 
   
    ![Configurar credenciales de implementación][deployment-credentials]
4. Haga clic en el elemento de menú de configuración **Origen de implementación** . Una vez allí, haga clic en el botón **Elegir origen**, seleccione la opción **Repositorio de Git local** y después haga clic en **Aceptar**. Se crea un repositorio de Git que se ejecuta en Azure, que tiene una asociación con la aplicación de API. Cada vez que confirme el código en la bifurcación *principal* del repositorio de Git, el código se publicará en la instancia de aplicación de API en ejecución activa. 
   
    ![Configurar un nuevo repositorio de Git local][select-git-repo]
5. Copie la nueva URL del repositorio de Git en el Portapapeles. Guárdela, será importante dentro de poco. 
   
    ![Configurar un nuevo repositorio de Git para la aplicación][copy-git-repo-url]
6. Realice la operación de inserción de Git del archivo WAR en el repositorio en línea. Para ello, vaya a la carpeta **deploy** que creó anteriormente para que pueda confirmar fácilmente el código hasta el repositorio que se ejecuta en el Servicio de aplicaciones. Una vez que esté en la ventana de la consola y que haya ido a la carpeta donde se encuentra la carpeta de aplicaciones web, emita los siguientes comandos de Git para iniciar el proceso y lanzar una implementación. 
   
         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master
   
    Después de emitir la solicitud **push** , se le pedirá la contraseña que creó anteriormente para la credencial de la implementación. Después de escribir las credenciales, verá que en el portal se muestra que la implementación se ha llevado a cabo.
7. Si utiliza nuevamente Postman para llegar a la aplicación de API recién implementada que se ejecuta en el Servicio de aplicaciones de Azure, verá que el comportamiento es coherente y que ahora se devuelven los datos de contacto, y todo ello mediante sencillos cambios en el código Java con scaffold Swagger.io. 
   
    ![Uso de la API de REST de contactos de Java activa en Azure][postman-calling-azure-contacts]

## <a name="next-steps"></a>Pasos siguientes
En este artículo ha sido capaz de comenzar con un archivo JSON de Swagger y algún código Java con scaffold obtenido con el editor de Swagger.io. A partir de ahí, algunos sencillos cambios y un proceso de implementación de Git han dado lugar a que tengamos una aplicación de API funcional escrita en Java. El siguiente tutorial muestra cómo [consumir aplicaciones de API desde clientes JavaScript mediante CORS][App Service API CORS]. Otros tutoriales de la serie muestran cómo implementar la autenticación y autorización.

Para sacar más partido a este ejemplo, aprenda más sobre el [SDK de almacenamiento para Java] para conservar los blobs de JSON. O bien, puede usar el [SDK de Java de DocumentDB] para guardar los datos de contacto en Azure DocumentDB. 

<a name="see-also"></a>

## <a name="see-also"></a>Otras referencias
Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure].

<!-- URL List -->

[App Service API CORS]: app-service-api-cors-consume-javascript.md
[Azure Portal]: https://portal.azure.com/
[SDK de Java de DocumentDB]: ../documentdb/documentdb-java-application.md
[evaluación gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Git]: http://www.git-scm.com/
[Centro para desarrolladores de Java de Azure]: /develop/java/
[Kit para desarrolladores de Java 8]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[Jax-RS]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[editor en línea de Swagger]: http://editor.swagger.io/
[Postman]: https://www.getpostman.com/
[SDK de almacenamiento para Java]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Editor de Swagger]: http://editor.swagger.io/
[código de Visual Studio]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png



<!--HONumber=Jan17_HO1-->


