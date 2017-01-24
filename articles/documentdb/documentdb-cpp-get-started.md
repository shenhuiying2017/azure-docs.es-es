---
title: Tutorial de NoSQL para C++ para DocumentDB | Microsoft Docs
description: "Tutorial de NoSQL para C++ que crea una base de datos de C++ y la aplicación de consola usando un SDK reconocido de DocumentDB para C++. DocumentDB es un servicio de base de datos NoSQL de escala mundial."
services: documentdb
documentationcenter: cpp
author: asthana86
manager: jhubbard
editor: 
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: aasthan
translationtype: Human Translation
ms.sourcegitcommit: 16bff1b5708652a75ea603f596c864901b12a88d
ms.openlocfilehash: f622b9a35c370148a3472fa6924a50933d59601e


---
# <a name="nosql-c-tutorial-documentdb-c-console-application"></a>Tutorial de NoSQL para C++: aplicación de consola de C++ con DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 
 

Bienvenido al tutorial de C++ para el SDK reconocido de Azure DocumentDB para C++. Después de seguir este tutorial, tendrá una aplicación de consola que crea recursos de DocumentDB y realiza consultas en ellos, incluida una base de datos de C++.

Describiremos:

* Creación y aprovisionamiento una cuenta de DocumentDB.
* Configuración de la aplicación
* Creación de una base de datos C++ DocumentDB
* Creación de una colección
* Creación de documentos JSON
* Consulta de la colección
* Sustitución de un documento
* Eliminación de un documento
* Eliminación de una base de datos C++ DocumentDB

¿No tiene tiempo? ¡No se preocupe! La solución completa está disponible en [GitHub](https://github.com/stalker314314/DocumentDBCpp). Consulte [Obtención de la solución completa](#GetSolution) para obtener instrucciones rápidas.

Después de haber completado el tutorial de C++, use los botones de votación en la parte inferior de esta página para enviar comentarios. 

Si quiere que nos pongamos en contacto directamente con usted, puede incluir su dirección de correo electrónico en los comentarios o, si lo prefiere, [puede ponerse usted en contacto con nosotros aquí](https://www.research.net/r/8BKRJ3Z). 

Comencemos.

## <a name="prerequisites-for-the-c-tutorial"></a>Requisitos previos para el tutorial de C++
Asegúrese de que dispone de lo siguiente:

* Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una [prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/), con los componentes del lenguaje C++ instalados.

## <a name="step-1-create-a-documentdb-account"></a>Paso 1: Creación de una cuenta de DocumentDB
Creemos una cuenta de DocumentDB. Si ya tiene una cuenta que desea usar, puede ir directamente a [Configuración de la aplicación de C++](#SetupNode).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="a-idsetupcastep-2-set-up-your-c-application"></a><a id="SetupC++"></a>Paso 2: Configuración de la aplicación de C++
1. Abra Visual Studio en el menú **Archivo**, haga clic en **Nuevo** y, a continuación, haga clic en **Proyecto**. 
2. En la ventana **Nuevo proyecto** del panel **Instalado** expanda **Visual C++**, haga clic en **Win32**, y luego en **Aplicación de consola Win32**. Llame al proyecto hellodocumentdb y, a continuación, haga clic en **Aceptar**. 
   
    ![Captura de pantalla del Asistente para nuevo proyecto](media/documentdb-cpp-get-started/hellodocumentdb.png)
3. Cuando se inicie el Asistente para aplicaciones Win32, haga clic en **Finalizar**.
4. Una vez creado el proyecto, abra el Administrador de paquetes de NuGet haciendo clic con el botón derecho en el proyecto **hellodocumentdb** en **el Explorador de soluciones** y luego haciendo clic en **Administrar paquetes de NuGet**. 
   
    ![Captura de pantalla que muestra Administrar paquetes NuGet en el menú del proyecto](media/documentdb-cpp-get-started/nuget.png)
5. En la pestaña **NuGet: hellodocumentdb** haga clic en **Examinar**, y, a continuación, busque *documentdbcpp*. En los resultados, seleccione DocumentDbCPP, tal como se muestra en la siguiente captura de pantalla. Este paquete instala las referencias a C++ REST SDK, que es una dependencia para DocumentDbCPP.  
   
    ![Captura de pantalla que muestra el paquete de DocumentDbCpp resaltado](media/documentdb-cpp-get-started/documentdbcpp.png)
   
    Una vez que los paquetes se han agregado al proyecto, ya está todo preparado para empezar a escribir código.   

## <a name="a-idconfigastep-3-copy-connection-details-from-azure-portal-for-your-documentdb-database"></a><a id="Config"></a>Paso 3: Copia de los detalles de la conexión en Azure Portal para la base de datos de DocumentDB
Abra [Azure Portal](https://portal.azure.com) y vaya hasta la cuenta de base de datos NoSQL (DocumentDB) que creó. Se necesitará el URI y la clave principal de Azure Portal en el paso siguiente para establecer una conexión desde el fragmento de código de C++. 

![URI y claves de DocumentDB en Azure Portal](media/documentdb-cpp-get-started/nosql-tutorial-keys.png)

## <a name="a-idconnectastep-4-connect-to-a-documentdb-account"></a><a id="Connect"></a>Paso 4: Conexión a una cuenta de DocumentDB
1. Agregue los siguientes encabezados y espacios de nombres al código fuente, después de `#include "stdafx.h"`.
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. A continuación, agregue el código siguiente a la función principal y reemplace la configuración de la cuenta y la clave principal para que coincidan con la configuración de DocumentDB del paso 3. 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    Ahora que tiene el código para inicializar el cliente de DocumentDB, veamos cómo trabajar con los recursos de DocumentDB.

## <a name="a-idcreatedbcollastep-5-create-a-c-database-and-collection"></a><a id="CreateDBColl"></a>Paso 5: Creación de una base de datos de C++ y una colección
Antes de que llevemos a cabo este paso, veamos cómo interactúan una base de datos, una colección y los documentos para aquellos que no estén familiarizados con DocumentDB. Una [base de datos](documentdb-resources.md#databases) es un contenedor lógico de almacenamiento de documentos en varias colecciones. Una [colección](documentdb-resources.md#collections) es un contenedor de documentos JSON y la lógica de aplicación de JavaScript asociada. Puede aprender más sobre el modelo jerárquico de recursos y conceptos de DocumentDB en [Modelo jerárquico de recursos y conceptos de DocumentDB](documentdb-resources.md).

Para crear una base de datos y la colección correspondiente agregue el código siguiente al final de la función principal. Esto crea una base de datos denominada "FamilyRegistry" y una colección denominada "FamilyCollection" usando la configuración de cliente que declaró en el paso anterior.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a name="a-idcreatedocastep-6-create-a-document"></a><a id="CreateDoc"></a>Paso 6: Creación de un documento
Los [documentos](documentdb-resources.md#documents) son contenido JSON definido por el usuario (arbitrario). Ahora puede insertar un documento en DocumentDB. Puede crear un documento copiando el código siguiente al final de la función principal. 

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

En resumen, este código crea una base de datos DocumentDB, colección y documentos que puede consultar en el Explorador de documentos en Azure Portal. 

![Tutorial C++: Diagrama que muestra la relación jerárquica entre la cuenta, la base de datos, la colección y los documentos](media/documentdb-cpp-get-started/documentdbdocs.png)

## <a name="a-idquerydbastep-7-query-documentdb-resources"></a><a id="QueryDB"></a>Paso 7: Consulta de recursos de DocumentDB
DocumentDB admite [consultas](documentdb-sql-query.md) enriquecidas contra los documentos JSON almacenados en cada colección. El siguiente código de ejemplo muestra una consulta realizada usando la sintaxis SQL de DocumentDB que se puede ejecutar con los documentos que hemos creado en el paso anterior.

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

## <a name="a-idreplaceastep-8-replace-a-document"></a><a id="Replace"></a>Paso 8: Reemplazo de un documento
DocumentDB admite el reemplazo de documentos JSON, como se muestra en el código siguiente. Agregue este código después de la función executesimplequery.

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

## <a name="a-iddeleteastep-9-delete-a-document"></a><a id="Delete"></a>Paso 9: Eliminación de un documento
DocumentDB admite la eliminación de documentos JSON, puede hacerlo copiando y pegando el código siguiente después de la función replacedocument. 

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

## <a name="a-iddeletedbastep-10-delete-a-database"></a><a id="DeleteDB"></a>Paso 10: Eliminación de una base de datos
La eliminación de la base de datos creada quitará la base de datos y todos los recursos secundarios (colecciones, documentos, etc.).

Copie y pegue el siguiente fragmento de código (función cleanup) después de la función deletedocument para quitar la base de datos y todos los recursos secundarios.

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a name="a-idrunastep-11-run-your-c-application-all-together"></a><a id="Run"></a>Paso 11: Ejecución de la aplicación C++
Hemos agregado código para crear, consultar, modificar y eliminar los diferentes recursos de DocumentDB.  Vamos ahora a conectar esto mediante la adición de llamadas a estas distintas funciones desde la función principal en hellodocumentdb.cpp junto con algunos mensajes de diagnóstico.

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

![Resultado de la aplicación de C++ de DocumentDB](media/documentdb-cpp-get-started/docdbconsole.png)

¡Enhorabuena! Ha completado el tutorial de C++ y tiene su primera aplicación de consola de DocumentDB.

## <a name="a-idgetsolutionaget-the-complete-c-tutorial-solution"></a><a id="GetSolution"></a> Obtención de la solución completa del tutorial de C++
Para compilar la solución GetStarted que contiene todos los ejemplos de este artículo, necesita lo siguiente:

* [Cuenta de DocumentDB][documentdb-create-account].
* La solución [GetStarted](https://github.com/stalker314314/DocumentDBCpp) está disponible en GitHub.

## <a name="next-steps"></a>Pasos siguientes
* Aprenda a [supervisar una cuenta de DocumentDB](documentdb-monitor-accounts.md).
* Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](https://www.documentdb.com/sql/demo).
* Obtenga más información sobre el modelo de programación en la sección sobre desarrollo de la [página de documentación de DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md





<!--HONumber=Jan17_HO1-->


