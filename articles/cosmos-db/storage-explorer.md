---
title: Administración de Azure Cosmos DB en el Explorador de Azure Storage
description: Aprenda a administrar Azure Cosmos DB en el Explorador de Azure Storage.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: 8c584ec0c8d89a232d573399cfabe02fc8aa1c87
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer"></a>Administración de Azure Cosmos DB en el Explorador de Azure Storage

El uso de Azure Cosmos DB en el Explorador de Azure Storage permite a los usuarios administrar entidades de Azure Cosmos DB, manipular datos, actualizar procedimientos y desencadenadores almacenados, además de otras entidades de Azure, como los blobs y las colas de Storage. Ahora puede usar la misma herramienta para administrar las diferentes entidades de Azure en un solo lugar. Actualmente, el Explorador de Azure Storage admite cuentas de SQL, MongoDB, Graph y Table.


## <a name="prerequisites"></a>requisitos previos

Una cuenta de Azure Cosmos DB para SQL API <!--or MongoDB API-->. Si no tiene una cuenta, puede crear una en Azure Portal, tal y como se describe en [Azure Cosmos DB: Compilación de una aplicación web de SQL API con .NET y Azure Portal](create-sql-api-dotnet.md).

## <a name="installation"></a>Instalación

Instale los bits más recientes del Explorador de Azure Storage aquí: [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/), ahora se admite la versión de Windows, Linux y MAC.

## <a name="connect-to-an-azure-subscription"></a>Conexión a una suscripción de Azure

1. Después de instalar el **Explorador de Azure Storage**, haga clic en el icono del **complemento** de la izquierda, como se muestra en la siguiente imagen:
       
   ![Icono de complemento](./media/storage-explorer/plug-in-icon.png)
 
2. Seleccione **Add an Azure Account** (Agregar una cuenta de Azure) y haga clic en **Iniciar sesión**.

   ![Conectarse a una suscripción de Azure](./media/storage-explorer/connect-to-azure-subscription.png)

2. En el cuadro de diálogo **Inicio de sesión en Microsoft Azure**, seleccione **Iniciar sesión** y luego escriba las credenciales de Azure.

    ![Iniciar sesión](./media/storage-explorer/sign-in.png)

3. Seleccione la suscripción en la lista y luego haga clic en **Aplicar**.

    ![Aplicar](./media/storage-explorer/apply-subscription.png)

    El panel del Explorador se actualiza y muestra las cuentas en la suscripción seleccionada.

    ![Lista de cuentas](./media/storage-explorer/account-list.png)

    Se ha conectado correctamente a su **cuenta de Cosmos DB** en su suscripción de Azure.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Conectarse a Azure Cosmos DB mediante una cadena de conexión

Una manera alternativa de conectarse a Azure Cosmos DB es usar una cadena de conexión. Siga estos pasos para conectarse usando una cadena de conexión.

1. Busque **Local and Attached** (Locales y adjuntas) en el árbol de la izquierda, haga clic con el botón derecho en **Cuentas de Cosmos DB**, elija **Connect to Cosmos DB...** (Conectar a Cosmos DB).

    ![Conectarse a Cosmos DB mediante una cadena de conexión](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Actualmente solo se admiten SQL y Table API. Elija API, pegue el valor de **Cadena de conexión**, escriba el valor de **Etiqueta de cuenta**, haga clic en **Siguiente** para consultar el resumen y, después, haga clic en **Conectar** para conectar la cuenta de Azure Cosmos DB. Para más información sobre cómo recuperar la cadena de conexión, vea [Obtener la cadena de conexión](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Cadena de conexión](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Conectarse a Azure Cosmos DB mediante un emulador local

Siga estos pasos para conectarse a una instancia de Azure Cosmos DB mediante un Emulator; actualmente solo se admite una cuenta SQL.

1. Instale el Emulador e inícielo. Para obtener información acerca de cómo instalar el Emulador, consulte [Uso del Emulador de Azure Cosmos DB para desarrollo y pruebas de forma local](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator)

2. Busque **Local and Attached** (Locales y adjuntas) en el árbol de la izquierda, haga clic con el botón derecho en **Cuentas de Cosmos DB**, elija **Connect to Cosmos DB Emulator...** (Conectar al Emulador de Cosmos DB)

    ![Conectarse a Cosmos DB mediante un Emulator](./media/storage-explorer/emulator-entry.png)

3. Actualmente solo se admite SQL API. Pegue el valor de **Cadena de conexión**, escriba el valor de **Etiqueta de cuenta**, haga clic en **Siguiente** para consultar el resumen y, después, haga clic en **Conectar** para conectar la cuenta de Azure Cosmos DB. Para más información sobre cómo recuperar la cadena de conexión, vea [Obtener la cadena de conexión](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Cuadro de diálogo Connect to Cosmos DB by Emulator (Conectarse a Cosmos DB mediante un Emulator)](./media/storage-explorer/emulator-dialog.png)


## <a name="azure-cosmos-db-resource-management"></a>Administración de recursos de Azure Cosmos DB

Realice estas operaciones para administrar una cuenta de Azure Cosmos DB:
* Abrir la cuenta en Azure Portal
* Agregar el recurso a la lista de acceso rápido
* Buscar y actualizar recursos
* Crear y eliminar bases de datos
* Crear y eliminar recopilaciones
* Crear, editar, eliminar y filtrar documentos
* Administrar procedimientos almacenados, desencadenadores y funciones definidas por el usuario

### <a name="quick-access-tasks"></a>Tareas de acceso rápido

Puede realizar muchas tareas de acción rápida haciendo clic con el botón derecho en una suscripción en el panel del Explorador:

* Haga clic con el botón derecho en una cuenta de Azure Cosmos DB o una base de datos, elija **Abrir en el portal** y administre los recursos en el explorador en Azure Portal.

     ![Abrir en el portal](./media/storage-explorer/open-in-portal.png)

* También puede agregar la cuenta, la base de datos o la colección de Azure Cosmos DB al **acceso rápido**.
* **Search from Here** (Buscar desde aquí) permite realizar búsquedas de palabras clave en la ruta de acceso seleccionada.

    ![Buscar desde aquí](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Administración de bases de datos y recopilaciones
#### <a name="create-a-database"></a>Creación de una base de datos 
-   Haga clic con el botón derecho en la cuenta de Azure Cosmos DB, elija **Crear base de datos**, escriba el nombre de la base de datos y presione **Entrar** para completar la acción.
       
    ![Crear base de datos](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Eliminación de una base de datos
- Haga clic con el botón derecho en la base de datos, elija **Eliminar base de datos** y haga clic en **Sí** en la ventana emergente. Se elimina el nodo de la base de datos y la cuenta de Azure Cosmos DB se actualiza automáticamente.

    ![Eliminar base de datos 1](./media/storage-explorer/delete-database1.png)  

    ![Eliminar base de datos 2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Creación de una colección
1. Haga clic con el botón derecho en la base de datos, elija **Crear colección** y proporcione información como **Id. de colección**, **Capacidad de almacenamiento**, etc. Haga clic en **Aceptar** para finalizar. 

    ![Crear colección 1](./media/storage-explorer/create-collection.png)

    ![Crear colección 2](./media/storage-explorer/create-collection2.png) 

2. Seleccione **Sin límite** para poder especificar una clave de partición y, después, haga clic en **Aceptar** para finalizar.

    Si se usa una clave de partición al crear una colección, una vez completada la creación no se podrá cambiar el valor de la clave de partición en la colección. Para más información sobre la configuración de claves de partición, vea [Uso de las API de Azure Cosmos DB](partition-data.md#designing-for-partitioning).

    ![Clave de partición](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>Eliminación de una colección
- Haga clic con el botón derecho en la colección, elija **Eliminar colección** y luego haga clic en **Sí** en la ventana emergente. 

    Se elimina el nodo de colección y la base de datos se actualiza automáticamente.

    ![Eliminar colección](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Administración de documentos

#### <a name="create-and-modify-documents"></a>Crear y modificar documentos
- Para crear un documento, abra **Documentos** en la ventana izquierda, elija **Nuevo documento**, edite el contenido en el panel derecho y haga clic en **Guardar**. También puede actualizar un documento existente y luego hacer clic en **Guardar**. Para descartar los cambios, haga clic en **Descartar**.

    ![Documento](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Eliminar un documento
- Haga clic en el botón **Eliminar** para eliminar el documento seleccionado.

#### <a name="query-for-documents"></a>Consulta de documentos
- Para editar el filtro de documento, escriba una [Consulta SQL](sql-api-sql-query.md) y haga clic en **Aplicar**.

    ![Filtro de documento](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>Administración de gráficos

#### <a name="create-and-modify-vertex"></a>Creación y modificación de un vértice
1. Para crear un nuevo vértice, abra **Grafo** en la ventana izquierda, haga clic en **New Vertex** (Nuevo vértice), edite el contenido y haga clic en **Aceptar**.    
2. Para modificar un vértice existente, haga clic en el icono del lápiz del panel derecho.   

    ![Grafo](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>Eliminación de un grafo
- Para eliminar un vértice, haga clic en el icono de la papelera que hay al lado del nombre.

#### <a name="filter-for-graph"></a>Filtro de grafos
- Para editar el filtro de grafo, escriba una [consulta de Gremlin](gremlin-support.md) y haga clic en **Aplicar filtro**.

    ![Filtro de grafo](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>Administración de tablas

#### <a name="create-and-modify-table"></a>Creación y modificación de una tabla
1. Para crear una tabla nueva, abra **Entidades** en la ventana izquierda, haga clic en **Agregar**, edite el contenido en el cuadro de diálogo **Agregar entidad**, agregue una propiedad haciendo clic en botón **Agregar propiedad** y haga clic en **Insertar**.
2. Para modificar una tabla, haga clic en **Editar**, modifique el contenido y haga clic en **Actualizar**.

    ![Tabla](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>Importación y exportación de una tabla
1. Para importarla, haga clic en el botón **Importar** y elija una tabla existente.
2. Para exportarla, haga clic en el botón **Exportar** y elija un destino.

    ![Importación y exportación de tablas](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>Eliminación de entidades
- Seleccione las entidades y haga clic en botón **Eliminar**.

    ![Eliminación de una tabla](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>Consulta de una tabla
- Haga clic en el botón **Consulta**, especifique la condición de la consulta y haga clic en el botón **Ejecutar consulta**. Cierre el panel Consulta, para lo que debe hacer clic en el botón **Cerrar consulta**.

    ![Consulta de tabla](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Administrar procedimientos almacenados, desencadenadores y UDF
* Para crear un procedimiento almacenado, en el árbol de la izquierda, haga clic con el botón derecho en **Procedimiento almacenado**, elija **Crear procedimiento almacenado**, escriba un nombre en la ventana de la izquierda, escriba los scripts del procedimiento almacenado en la ventana de la derecha y luego haga clic en **Crear**. 
* Si quiere editar un procedimiento almacenado existente, haga doble clic él, modifíquelo y haga clic en **Actualizar** para guardar o en **Descartar** para cancelar el cambio.

    ![Procedimiento almacenado](./media/storage-explorer/stored-procedure.png)
* Las operaciones para **Desencadenadores** y **UDF** son similares a las de los **procedimientos almacenados**.

## <a name="troubleshooting"></a>solución de problemas

[Azure Cosmos DB en el Explorador de Azure Storage](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer) es una aplicación independiente que le permite conectarse a las cuentas de Azure Cosmos DB hospedadas en Azure y nubes soberanas de Windows, macOS o Linux. Permite a los usuarios administrar entidades de Azure Cosmos DB, manipular datos, actualizar procedimientos y desencadenadores almacenados, además de otras entidades de Azure como los blobs y las colas de Storage.

Estas son soluciones a problemas comunes de Azure Cosmos DB vistos en el Explorador de Storage.

### <a name="sign-in-issues"></a>Problemas de inicio de sesión

Antes de continuar, pruebe a reiniciar la aplicación para ver si se pueden corregir los problemas.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certificado autofirmado de cadena de certificados

Existen varias razones por las que puede ver este error, las dos más comunes son:

+ Se encuentra detrás de un "proxy transparente", lo que significa que alguien (por ejemplo el departamento de TI) intercepta el tráfico HTTPS, lo descifra y después lo cifra mediante un certificado autofirmado.

+ Está ejecutando un software, como un software antivirus, que inserta un certificado SSL autofirmado en los mensajes HTTPS que recibe.

Cuando el Explorador de Storage se encuentra uno de estos "certificados autofirmados", ya no sabe si el mensaje HTTPS que recibe se ha alterado. Pero si tiene una copia del certificado autofirmado, puede hacer que el Explorador de Storage confíe en él. Si no está seguro de qué o quién está insertando el certificado, puede intentar encontrarlo realizando los pasos siguientes:

1. Instale Open SSL.
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (cualquiera de las versiones ligeras vale)
     - Mac y Linux: debe estar incluido con el sistema operativo
2. Ejecute Open SSL.
    - Windows: Vaya al directorio de instalación y después a **/bin/**, a continuación, haga doble clic en **openssl.exe**.
    - Mac y Linux: ejecute **openssl** desde un terminal
3. Ejecute `s_client -showcerts -connect microsoft.com:443`
4. Busque certificados autofirmados. Si no está seguro de cuáles son autofirmados, busque cualquier lugar en el que el asunto ("s:") y el emisor ("i:") sean el mismo.
5.  Cuando haya encontrado cualquier certificado autofirmado, copie y pegue todo el contenido desde **---BEGIN CERTIFICATE---** a **---END CERTIFICATE---** (inclusive) a un archivo .cer nuevo para cada uno de los certificados.
6.  Abra el Explorador de Storage y vaya a **Editar** > **Certificados SSL** > **Importar certificados**. Mediante el selector de archivos, busque, seleccione y abra los archivos .cer que creó.

Si no encuentra ningún certificado autofirmado siguiendo los pasos anteriores, envíenos un comentario para obtener más ayuda.

#### <a name="unable-to-retrieve-subscriptions"></a>No se pueden recuperar las suscripciones

Si no puede recuperar las suscripciones después de haber iniciado sesión correctamente en:

- Verifique que la cuenta tiene acceso a las suscripciones iniciando sesión en [Azure Portal](http://portal.azure.com/)
- Asegúrese de que ha iniciado sesión usando el entorno correcto ([Azure](http://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Alemania](https://portal.microsoftazure.de/), [Azure US Government](http://portal.azure.us/), o entorno personalizado o Azure Stack)
- Si está detrás de un proxy, asegúrese de que ha configurado correctamente el proxy del Explorador de Storage
- Pruebe a eliminar la cuenta y volver a agregarla
- Intente eliminar los siguientes archivos del directorio de inicio (como, C:\Users\ContosoUser) y, luego, vuelva a agregar la cuenta:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Revise la consola de las herramientas de desarrollo (f12) cuando inicie sesión por si aparecieran mensajes de error

![console](./media/storage-explorer/console.png)

#### <a name="unable-to-see-the-authentication-page"></a>No se puede ver la página de autenticación 

Si no puede ver la página de autenticación:

- Según la velocidad de la conexión, la página de inicio de sesión puede tardar unos instantes en cargar; espere al menos un minuto antes de cerrar el diálogo de autenticación
- Si está detrás de un proxy, asegúrese de que ha configurado correctamente el proxy del Explorador de Storage
- Abra la consola del desarrollador presionando la tecla F12. Revise las respuestas de la consola del desarrollador y vea si puede encontrar alguna pista para saber por qué no funciona la autenticación

#### <a name="cannot-remove-account"></a>No se puede quitar la cuenta

Si no puede quitar una cuenta, o si el vínculo para volver a autenticar no hace nada

- Intente eliminar los siguientes archivos del directorio de inicio y vuelva a agregar la cuenta:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Si desea quitar recursos de Storage conectados a SAS, elimine:
  - Carpeta %AppData%/StorageExplorer para Windows
  - /Users/<su_nombre>/Library/Applicaiton SUpport/StorageExplorer para Mac
  - ~/.config/StorageExplorer para Linux
  - **Tendrá que volver a escribir todas sus credenciales** si elimina estos archivos


### <a name="httphttps-proxy-issue"></a>Problema de proxy de HTTP/HTTPS

No puede mostrar los nodos de Azure Cosmos DB en el árbol de la izquierda al configurar el proxy de http/https en ASE. Es un problema conocido y se corregirá en la siguiente versión. En este momento puede usar el Explorador de datos de Azure Cosmos DB en Azure Portal como solución alternativa. 

### <a name="development-node-under-local-and-attached-node-issue"></a>Problema de nodo "Development" (Desarrollo) en nodo "Local and Attached" (Locales y conectados)

No hay ninguna respuesta tras hacer clic en el nodo "Development" (Desarrollo) en el nodo "Local and Attached" (Locales y conectados) en el árbol de la izquierda.  Este comportamiento es normal. El emulador local de Azure Cosmos DB se admitirá en la próxima versión.

![Nodo "Development" (Desarrollo)](./media/storage-explorer/development.png)

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Error al adjuntar la cuenta de Azure Cosmos DB al nodo "Local and Attached" (Locales y conectados)

Si ve el error siguiente después de adjuntar la cuenta de Azure Cosmos DB en el nodo"Local and Attached" (Locales y conectados), compruebe si está usando la cadena de conexión correcta.

![Error al adjuntar Azure Cosmos DB en "Local and Attached" (Locales y conectados)](./media/storage-explorer/attached-error.png)

### <a name="expand-azure-cosmos-db-node-error"></a>Error al expandir el nodo de Azure Cosmos DB

Puede ver error siguiente al intentar expandir los nodos del árbol de la izquierda. 

![Error de expansión](./media/storage-explorer/expand-error.png)

Pruebe con las sugerencias siguientes:

- Compruebe si la cuenta de Azure Cosmos DB está en curso de aprovisionamiento e inténtelo de nuevo cuando ya se haya creado la cuenta correctamente.
- Si la cuenta está en el nodo de "Quick Access" (Acceso rápido) o los nodos "Local and Attached" (Locales y conectados), compruebe si la cuenta se ha eliminado. Si es así, tiene que quitar manualmente el nodo.

## <a name="contact-us"></a>Ponerse en contacto con nosotros

Si ninguna de las soluciones funciona, envíe un correo electrónico al equipo de herramientas de desarrollo de Azure Cosmos DB ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) con información detallada acerca del problema para poder corregirlo.

## <a name="next-steps"></a>Pasos siguientes

* Para ver cómo se usa Azure Cosmos DB en el Explorador de Azure Storage, vea este vídeo: [Use Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be) (Usar Azure Cosmos DB en el Explorador de Azure Storage).
* Puede ver más información acerca del Explorador de Storage y conectarse a más servicios en [Introducción al Explorador de Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

