<properties pageTitle="Calling SQL stored procedures with a JavaScript back end" metaKeywords="stored procedures, SQL, mobile devices, Azure" description="explains how to use SQL stored procedures in a mobile services JavaScript backed." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Calling SQL stored procedures with a JavaScript back end" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Llamada a procedimientos almacenados de SQL con un backend de JavaScript

Motivos por los que querría llamar a procedimientos almacenados de SQL Server:

-   Su servicio móvil utiliza procedimientos almacenados existentes a los que necesita tener acceso.
-   La arquitectura de su aplicación requiere el uso de procedimientos almacenados para el acceso a bases de datos.
-   Preferencia personal.

En este tema se indica cómo realizar las siguientes acciones:

-   [Llamada a un procedimiento almacenado sencillo][Llamada a un procedimiento almacenado sencillo]
-   [Llamada a un procedimiento almacenado con parámetros][Llamada a un procedimiento almacenado con parámetros]
-   [Más información][Más información]

En este artículo se tratan los procedimientos almacenados en un servicio móvil con un backend de JavaScript.

Pero puede crear servicios móviles con un backend .NET, que tiene una arquitectura completamente diferente basada en Entity Framework. Para obtener más información, consulte [Funcionamiento del backend .NET de Servicios móviles de Azure][Funcionamiento del backend .NET de Servicios móviles de Azure].

## Llamada a un procedimiento almacenado sencillo

**"Mi base de datos utiliza procedimientos almacenados: ¿cómo los puedo llamar desde un servicio móvil?"**

Debe hacerlo con el [objeto mssql][objeto mssql], que permite ejecutar instrucciones *Transact-SQL* ("T-SQL") que llamen al procedimiento.

### Escritura y prueba del código Transact SQL

Supongamos que el procedimiento almacenado se denomina *GetAll* y su servicio móvil se denomina **todolist**.

1.  Seleccione el icono **Bases de datos** en la barra lateral del portal de Azure y elija su base de datos, cuyo nombre es el nombre de su servicio móvil más el sufijo "**_db**".

2.  En la página Inicio rápido de base de datos, presione el vínculo **Administrar** al final de la página.

3.  Inicie sesión en la base de datos.

4.  Presione **Nueva consulta** en la barra superior y agregue el siguiente código:

            EXEC todolist.GetAll

5.  Haga clic en **Ejecutar** y compruebe los resultados.

Observe que el nombre del procedimiento tiene como prefijo el nombre del esquema de la base de datos, que, de forma predeterminada, es el nombre del servicio móvil.

### ¿Dónde pongo la llamada al objeto mssql?

Si necesita llamar al procedimiento almacenado directamente, la forma más flexible es escribir y llamar a una [API personalizada][API personalizada].

1.  En el panel del servicio móvil, haga clic en **API** y elija **CREAR**, dele el nombre ***getall*** al script y agregue este código para llamar al procedimiento almacenado:

        exports.get = function(request, response) {
            var mssql = request.service.mssql;
            var sql = "EXEC todolist.GetAll";
            mssql.query(sql, {
                success: function(results) {                          
                        response.send(200, results); 
                }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

2.  Ponga la siguiente dirección URL en la línea de dirección de un explorador para probarlo:

        https://todolist.azure-mobile.net/api/getall

También puede poner código similar en un script de servidor *read*, *insert*, *update* o *delete* estándar de una tabla para que el código omita el comportamiento predeterminado y llame al procedimiento almacenado. Código similar puede ir dentro de un script *Scheduler*. Sin embargo, crear una API personalizada es la forma más flexible.

### Llamada al código desde el cliente

Modifique el código del cliente para que llame al método **invokeApi** en el objeto **MobileServiceClient**. La sintaxis exacta del código es específica del dispositivo cliente y se explica en los temas siguientes:

-   [C\# para Tienda Windows][C\# para Tienda Windows]
-   [JavaScript para Tienda Windows][JavaScript para Tienda Windows]
-   [Windows Phone][Windows Phone]
-   [iOS][iOS]
-   [Android][Android]
-   [HTML][HTML]

## <a name="parameters"></a>Llamada a un procedimiento almacenado con parámetros

Supongamos que el procedimiento almacenado se denomina *ItemsByStatus*, que tiene un único parámetro denominado *Status* y que queremos llamarlo desde una API personalizada.

1.  Siga los pasos de la sección anterior para escribir y probar el código T-SQL usando la página de base de datos del portal de Azure, pero utilice este código T-SQL en su lugar:

        EXEc todolist.ItemsByStatus @Status = 1

2.  Haga clic en el símbolo **Ejecutar** y compruebe los resultados.

3.  Al igual que en el paso anterior, cree una nueva API personalizada denominada **completeall** con el siguiente código que llama al procedimiento almacenado. Observe que, en el código siguiente, debe reemplazar el valor del parámetro **@Status** que utilizó en las pruebas por **"?"**, que se rellenará en tiempo de ejecución con el parámetro proporcionado.

        exports.get = function(request, response) { 
            var mssql = request.service.mssql;
            var param1 = parseInt(request.query.status);
            var sql = "EXEC todolist.ItemsByStatus @Status = ?";
            mssql.query(sql, [param1], {
                success: function(results) { 
                         response.send(200, results[0]); 
                    }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

4.  Pruebe la API con esta dirección URL en un explorador:

        https://todolist.azure-mobile.net/api/completeall?status=1

5.  Llame a la API desde el cliente como se explica en la sección anterior.

### Firmas de parámetro más complejas

El procedimiento almacenado de la sección anterior tenía un solo parámetro. Esta es la sintaxis que debe usarse para una firma más larga:

        function read(query, user, request) {
            var sql = 'EXEC MySProc @Param1 = ?, @Param2 = ?, @Param3 = ?';
            var param1 = request.parameters.first;
            var param2 = request.parameters.second;
            var param3 = request.parameters.third;
            mssql.query(sql, [param1, param2, param3], {
                success: function(results) {
                    request.respond(200, results);
                }
            });
        }

Tenga en cuenta que, en la instrucción **EXEC**, se llama a los parámetros por nombre y no por posición. Esta es la sintaxis recomendada y aísla el código de cambios mínimos en el procedimiento almacenado, como cambiar el orden de los parámetros o agregar más parámetros.

## <a name="more"></a>Más información

Este tema es solo un esbozo.

Puede consultar documentación detallada sobre el código utilizado en estos temas:

-   [objeto mssql]: información de referencia acerca del objeto central utilizado para llamar a los procedimientos almacenados en servicios móviles
-   [Trabajo con un servicio móvil back-end de JavaScript]: información más general acerca del back-end de Javascript, incluido material acerca del objeto mssql 

Estos son otros escenarios que puede encontrar:

-   [Cómo puedo hacer más de una operación de lectura con scripting][Cómo puedo hacer más de una operación de lectura con scripting] explica cómo leer condicionalmente una tabla o, como alternativa, llamar a un procedimiento almacenado. Este bloguero, Carlos Figueira, publica a menudo entradas sobre bases de datos y Servicios móviles de Azure.

-   [Acceso a un procedimiento almacenado desde un esquema diferente][Acceso a un procedimiento almacenado desde un esquema diferente] explica soluciones para problemas que puede encontrar en el acceso a procedimientos almacenados que residen en diferentes esquemas de la misma base de datos de Servicios móviles.

También puede usar el Portal de administración de Azure para administrar y crear nuevos procedimientos almacenados.

<!-- Anchors. --> 
<!-- URLs. -->

  [Llamada a un procedimiento almacenado sencillo]: #simple
  [Llamada a un procedimiento almacenado con parámetros]: #parameters
  [Más información]: #more
  [Funcionamiento del backend .NET de Servicios móviles de Azure]: http://curah.microsoft.com/64518/how-the-azure-mobile-services-net-backend-works
  [objeto mssql]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554212.aspx
  [API personalizada]: http://msdn.microsoft.com/en-us/library/windowsazure/dn280974.aspx
  [C\# para Tienda Windows]: http://azure.microsoft.com/es-es/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [JavaScript para Tienda Windows]: http://azure.microsoft.com/es-es/documentation/articles/mobile-services-windows-store-javascript-call-custom-api/
  [Windows Phone]: http://azure.microsoft.com/es-es/documentation/articles/mobile-services-windows-phone-call-custom-api/
  [iOS]: http://azure.microsoft.com/es-es/documentation/articles/mobile-services-ios-call-custom-api/
  [Android]: http://azure.microsoft.com/es-es/documentation/articles/mobile-services-android-call-custom-api/
  [HTML]: http://azure.microsoft.com/es-es/documentation/articles/mobile-services-html-call-custom-api/
  [Cómo puedo hacer más de una operación de lectura con scripting]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/fccf4ae7-f43c-4c2d-8518-32e2df84a824/how-do-i-do-more-than-1-read-operation-by-scripting?forum=azuremobile
  [Acceso a un procedimiento almacenado desde un esquema diferente]: http://blogs.msdn.com/b/jpsanders/archive/2013/05/02/windows-azure-mobile-services-accessing-a-stored-procedure-from-a-different-schema.aspx
  [Trabajo con un servicio móvil back-end de JavaScript]: http://azure.microsoft.com/es-es/documentation/articles/mobile-services-how-to-use-server-scripts/
  [mssql object]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554212.aspx