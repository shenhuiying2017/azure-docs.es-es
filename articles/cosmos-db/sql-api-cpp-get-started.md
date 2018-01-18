---
title: Tutorial de C++ para Azure Cosmos DB | Microsoft Docs
description: "Tutorial de C++ que crea una aplicación de consola y una base de datos de C++ mediante un SDK aprobado para C++. Azure Cosmos DB es un servicio de base de datos de escala mundial."
services: cosmos-db
documentationcenter: cpp
author: asthana86
manager: jhubbard
editor: 
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 12/25/2016
ms.author: aasthan
ms.openlocfilehash: da969e3f619c9703ea0c02a148f11a9509d6e988
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="azure-cosmos-db-c-console-application-tutorial-for-the-sql-api"></a>Azure Cosmos DB: tutorial de la aplicación de consola de C++ para la API de SQL
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js para MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)] 

Le damos la bienvenida al tutorial de C++ para el SDK aprobado de la API de SQL de Azure Cosmos DB para C++. Al terminar este tutorial, tendrá una aplicación de consola que crea recursos de Azure Cosmos DB y realiza consultas en ellos, incluida una base de datos de C++.

Esta guía de inicio rápido incluye:

* Creación de una cuenta de Azure Cosmos DB y conexión a ella
* Configuración de la aplicación
* Creación de una base de datos Azure Cosmos DB en C++
* Creación de una colección
* Creación de documentos JSON
* Consulta de la colección
* Sustitución de un documento
* Eliminación de un documento
* Eliminación de la base de datos Azure Cosmos DB en C++

¿No tiene tiempo? ¡No se preocupe! La solución completa está disponible en [GitHub](https://github.com/stalker314314/sql-apiCpp). Consulte [Obtención de la solución completa](#GetSolution) para obtener instrucciones rápidas.

Comencemos.

## <a name="prerequisites-for-the-c-tutorial"></a>Requisitos previos para el tutorial de C++
Asegúrese de que dispone de lo siguiente:

* Una cuenta de Azure activa. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Visual Studio 2017](https://www.visualstudio.com/downloads/), con los componentes del lenguaje C++ instalados. Si aún no tiene Visual Studio de 2017 instalado, puede descargar y usar la versión **gratis** de [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Asegúrese de que habilita **Desarrollo de Azure** durante la instalación de Visual Studio.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Paso 1: Creación de una cuenta de Azure Cosmos DB
Vamos a crear una cuenta de Azure Cosmos DB. Si ya tiene una cuenta que desea usar, puede ir directamente a [Configuración de la aplicación de C++](#SetupC++).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupC++"></a>Paso 2: Configuración de la aplicación de C++
1. Abra Visual Studio en el menú **Archivo**, haga clic en **Nuevo** y, a continuación, haga clic en **Proyecto**. 
2. En la ventana **Nuevo proyecto** del panel **Instalado** expanda **Visual C++**, haga clic en **Win32**, y luego en **Aplicación de consola Win32**. Llame al proyecto hellodocumentdb y, a continuación, haga clic en **Aceptar**. 
   
    ![Captura de pantalla del Asistente para nuevo proyecto](media/sql-api-cpp-get-started/hello.png)
3. Cuando se inicie el Asistente para aplicaciones Win32, haga clic en **Finalizar**.
4. Una vez creado el proyecto, abra el Administrador de paquetes de NuGet haciendo clic con el botón derecho en el proyecto **hellodocumentdb** en **el Explorador de soluciones** y luego haciendo clic en **Administrar paquetes de NuGet**. 
   
    ![Captura de pantalla que muestra Administrar paquetes NuGet en el menú del proyecto](media/sql-api-cpp-get-started/nuget.png)
5. En la pestaña **NuGet: hellodocumentdb** haga clic en **Examinar**, y, a continuación, busque *documentdbcpp*. En los resultados, seleccione DocumentDbCPP, tal como se muestra en la siguiente captura de pantalla. Este paquete instala las referencias a C++ REST SDK, que es una dependencia para DocumentDbCPP.  
   
    ![Captura de pantalla que muestra el paquete de DocumentDbCpp resaltado](media/sql-api-cpp-get-started/cpp.png)
   
    Una vez que los paquetes se han agregado al proyecto, ya está todo preparado para empezar a escribir código.   

## <a id="Config"></a>Paso 3: Copia de los detalles de la conexión de Azure Portal para la base de datos de Azure Cosmos DB
Abra [Azure Portal](https://portal.azure.com) y vaya hasta la cuenta de la base de datos de Azure Cosmos DB que ha creado. Se necesitará el URI y la clave principal de Azure Portal en el paso siguiente para establecer una conexión desde el fragmento de código de C++. 

![Identificador URI y claves de Azure Cosmos DB en Azure Portal](media/sql-api-cpp-get-started/nosql-tutorial-keys.png)

## <a id="Connect"></a>Paso 4: Conexión a una cuenta de Azure Cosmos DB
1. Agregue los siguientes encabezados y espacios de nombres al código fuente, después de `#include "stdafx.h"`.
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. A continuación, agregue el código siguiente a la función principal y reemplace la configuración de la cuenta y la clave principal para que coincidan con la configuración de Azure Cosmos DB del paso 3. 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    Ahora que tiene el código necesario para inicializar el cliente, se explicará cómo usar los recursos de Azure Cosmos DB.

## <a id="CreateDBColl"></a>Paso 5: Creación de una base de datos de C++ y una colección
Antes realizar este paso, vamos a explicar la forma de interacción de las bases de datos, las colecciones y los documentos para aquellos que no estén familiarizados con Azure Cosmos DB. Una [base de datos](sql-api-resources.md#databases) es un contenedor lógico de almacenamiento de documentos en varias colecciones. Una [colección](sql-api-resources.md#collections) es un contenedor de documentos JSON y la lógica de aplicación de JavaScript asociada. Para más información acerca del modelo jerárquico de recursos y de los conceptos de Azure Cosmos DB, consulte [Modelo jerárquico de recursos y conceptos básicos de DocumentDB](sql-api-resources.md).

Para crear una base de datos y la colección correspondiente agregue el código siguiente al final de la función principal. Esto crea una base de datos denominada "FamilyRegistry" y una colección denominada "FamilyCollection" usando la configuración de cliente que declaró en el paso anterior.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a id="CreateDoc"></a>Paso 6: Creación de un documento
Los [documentos](sql-api-resources.md#documents) son contenido JSON definido por el usuario (arbitrario). Ahora puede insertar un documento en Azure Cosmos DB. Puede crear un documento copiando el código siguiente al final de la función principal. 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

En resumen, este código crea una base de datos, una colección y documentos de Azure Cosmos DB que se pueden consultar en el Explorador de documentos en Azure Portal. 

![Tutorial C++: Diagrama que muestra la relación jerárquica entre la cuenta, la base de datos, la colección y los documentos](media/sql-api-cpp-get-started/docs.png)

## <a id="QueryDB"></a>Paso 7: Consulta de los recursos de Azure Cosmos DB
Azure Cosmos DB admite [consultas](sql-api-sql-query.md) enriquecidas en los documentos JSON que se almacenan en las colecciones. El siguiente código de ejemplo muestra una consulta realizada mediante la sintaxis de SQL que se puede ejecutar en los documentos creados en el paso anterior.

La función toma como argumentos el identificador único o identificador de recurso para la base de datos y la colección junto con el cliente de documento. Agregue este código antes de la función principal.

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Replace"></a>Paso 8: Reemplazo de un documento
Azure Cosmos DB admite la sustitución de documentos JSON, como se muestra en el código siguiente. Agregue este código después de la función executesimplequery.

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a id="Delete"></a>Paso 9: Eliminación de un documento
Azure Cosmos DB admite la eliminación de documentos JSON, para lo que se puede copiar y pegar el código siguiente después de la función replacedocument. 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="DeleteDB"></a>Paso 10: Eliminación de una base de datos
La eliminación de la base de datos creada quitará la base de datos y todos los recursos secundarios (colecciones, documentos, etc.).

Copie y pegue el siguiente fragmento de código (función cleanup) después de la función deletedocument para quitar la base de datos y todos los recursos secundarios.

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Run"></a>Paso 11: Ejecución de la aplicación C++
Hemos agregado código para crear, consultar, modificar y eliminar distintos recursos de Azure Cosmos DB.  Vamos ahora a conectar esto mediante la adición de llamadas a estas distintas funciones desde la función principal en hellodocumentdb.cpp junto con algunos mensajes de diagnóstico.

Para ello, reemplace la función principal de la aplicación con el código siguiente. Con esto se sobrescriben los valores de account_configuration_uri y primary_key que copió en el código en el Paso 3, por lo que debe guardar esa línea o volver a copiar los valores desde el portal. 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

Ahora podrá generar y ejecutar el código en Visual Studio presionando F5 o de forma alternativa en la ventana de terminal buscando la aplicación y ejecutando el archivo ejecutable. 

Ahora debería ver la salida de la aplicación GetStarted. El resultado debería coincidir con la siguiente captura de pantalla.

![Salida de la aplicación de C++ de Azure Cosmos DB](media/sql-api-cpp-get-started/console.png)

Felicidades. Ha completado el tutorial de C++ y tiene la primera aplicación de consola de Azure Cosmos DB.

## <a id="GetSolution"></a> Obtención de la solución completa del tutorial de C++
Para compilar la solución GetStarted que contiene todos los ejemplos de este artículo, necesita lo siguiente:

* [Cuenta de Azure Cosmos DB][create-account].
* La solución [GetStarted](https://github.com/stalker314314/DocumentDBCpp) está disponible en GitHub.

## <a name="next-steps"></a>pasos siguientes
* Información acerca de cómo [supervisar una cuenta de Azure Cosmos DB](monitor-accounts.md).
* Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](https://www.documentdb.com/sql/demo).
* Obtenga más información sobre el modelo de programación en la sección sobre desarrollo de la [página de documentación de Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

[create-account]: create-sql-api-dotnet.md#create-account


