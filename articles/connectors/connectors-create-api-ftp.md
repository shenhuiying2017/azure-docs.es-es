<properties
pageTitle="Aprenda a usar el conector de FTP en aplicaciones lógicas | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Conéctese a un servidor FTP para administrar sus archivos. En FTP, puede realizar diversas acciones, como cargar, actualizar, obtener y eliminar archivos."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/22/2016"
ms.author="deonhe"/>

# Introducción al conector de FTP

Use el conector de FTP para supervisar, administrar y crear archivos en un servidor FTP.

Para poder usar [un conector](./apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conexión a un FTP

Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una [conexión](./connectors-overview.md) proporciona conectividad entre una aplicación lógica y otro servicio.

### Creación de una conexión a FTP

>[AZURE.INCLUDE [Pasos para crear una conexión a FTP](../../includes/connectors-create-api-ftp.md)]

## Uso de un desencadenador de FTP

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.IMPORTANT]El conector de FTP necesita un servidor FTP que sea accesible desde Internet y que esté configurado para poder funcionar en modo PASIVO. Además, el conector de FTP **no es compatible con FTPS implícito (FTP por SSL)**. El conector de FTP solo admite FTPS explícito (FTP por SSL).

En este ejemplo, le enseñaremos a usar el desencadenador **FTP - When a file is added or modified** (FTP: cuando se agrega o modifica un archivo) para que, cuando se agregue o se modifique un archivo en un servidor FTP, se inicie el flujo de trabajo de una aplicación lógica. En un entorno empresarial, podría utilizar este desencadenador para supervisar los nuevos archivos que se agregan a una carpeta FTP y que representan los pedidos de los clientes. Podría utilizar una acción del conector de FTP, como **Get file content** (Obtener contenido del archivo), para obtener el contenido del archivo a fin de procesarlo y almacenarlo después en la base de datos de pedidos.

1. Escriba *ftp* en el cuadro de búsqueda del diseñador de aplicaciones lógicas y seleccione el desencadenador **FTP - When a file is added or modified** (FTP: cuando se agrega o modifica un archivo). ![Imagen de desencadenador de FTP 1](./media/connectors-create-api-ftp/ftp-trigger-1.png) Se abrirá el control **When a file is added or modified** (Cuando se agrega o modifica un archivo). ![Imagen de desencadenador de FTP 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)
- Seleccione la opción **...** situada a la derecha del control. Se abrirá el control de selector de carpeta. ![Imagen de desencadenador de FTP 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)
- Seleccione la opción **>** (flecha derecha) y busque la carpeta donde desea supervisar los archivos nuevos o modificados. Al seleccionar la carpeta, observará que esta aparece en el control **Carpeta**. ![Imagen de desencadenador de FTP 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)


En este punto, la aplicación lógica está configurada con un desencadenador que activará otros desencadenadores y acciones del flujo de trabajo cuando se cree o modifique un archivo en la carpeta FTP especificada.

>[AZURE.NOTE]Para que una aplicación lógica sea funcional, debe contener al menos un desencadenador y una acción. Siga los pasos que se describen en la sección siguiente para agregar una acción.



## Uso de una acción de FTP

Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información sobre las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Ahora que ha agregado un desencadenador, siga estos pasos para incorporar una acción que obtendrá el contenido del archivo nuevo o modificado detectado por el desencadenador.

1. Seleccione **+ Nuevo paso** para agregar la acción que obtendrá el contenido del archivo del servidor FTP.
- Seleccione el vínculo **Add an action** (Agregar una acción). ![Imagen de acción de FTP 1](./media/connectors-create-api-ftp/ftp-action-1.png)
- Escriba *FTP* para buscar todas las acciones relacionadas con FTP.
- Seleccione **FTP - Get file content** (FTP: obtener contenido del archivo). Esta será la acción que se ejecutará cuando se encuentre un archivo nuevo o modificado en la carpeta FTP. ![Imagen de acción de FTP 2](./media/connectors-create-api-ftp/ftp-action-2.png) Se abrirá el control **Get file content** (Obtener contenido del archivo). **Nota**: Si no lo ha hecho previamente, se le pedirá que autorice a la aplicación lógica para que pueda acceder a la cuenta del servidor FTP. ![Imagen de acción de FTP 3](./media/connectors-create-api-ftp/ftp-action-3.png)
- Seleccione el control **Archivo** (el espacio en blanco situado bajo **ARCHIVO***). Aquí, podrá utilizar cualquiera de las propiedades del archivo nuevo o modificado detectado en el servidor FTP.
- Seleccione la opción **Contenido del archivo**. ![Imagen de acción de FTP 4](./media/connectors-create-api-ftp/ftp-action-4.png)
-  El control se actualiza, lo que indica que la acción **FTP - Get file content** (FTP: obtener contenido del archivo) obtendrá el *contenido del archivo* nuevo o modificado del servidor FTP. ![Imagen de acción de FTP 5](./media/connectors-create-api-ftp/ftp-action-5.png)
- Guarde el trabajo y agregue un archivo a la carpeta FTP para comprobar el flujo de trabajo.

En este punto, la aplicación lógica está configurada con un desencadenador que supervisa una carpeta de un servidor FTP e inicia el flujo de trabajo cuando se detecta un archivo nuevo o modificado en dicho servidor.

La aplicación lógica también está configurada con una acción que obtiene el contenido del archivo nuevo o modificado.

Puede agregar otra acción; por ejemplo, [SQL Server - insert row](./connectors-create-api-sqlazure.md#insert-row) (SQL Server: insertar fila), para insertar el contenido del archivo nuevo o modificado en una tabla de base de datos SQL.

## Detalles técnicos

Estos son los detalles sobre los desencadenadores, las acciones y las respuestas compatibles con esta conexión:

## Desencadenadores de FTP

FTP dispone de los siguientes desencadenadores:

|Desencadenador | Descripción|
|--- | ---|
|[When a file is added or modified](connectors-create-api-ftp.md#when-a-file-is-added-or-modified) (Cuando se agrega o modifica un archivo)|Esta operación desencadena un flujo cuando se agrega o modifica un archivo en una carpeta.|


## Acciones de FTP

FTP dispone de las siguientes acciones:


|Acción|Descripción|
|--- | ---|
|[Obtención de metadatos de archivo](connectors-create-api-ftp.md#get-file-metadata)|Esta operación obtiene los metadatos de un archivo.|
|[Actualizar archivo](connectors-create-api-ftp.md#update-file)|Esta operación actualiza un archivo.|
|[Eliminar archivo](connectors-create-api-ftp.md#delete-file)|Esta operación elimina un archivo.|
|[Obtener metadatos de archivo mediante la ruta de acceso](connectors-create-api-ftp.md#get-file-metadata-using-path)|Esta operación obtiene los metadatos de un archivo mediante la ruta de acceso.|
|[Obtener contenido de archivo mediante la ruta de acceso](connectors-create-api-ftp.md#get-file-content-using-path)|Esta operación obtiene el contenido de un archivo mediante la ruta de acceso.|
|[Obtener contenido de archivo](connectors-create-api-ftp.md#get-file-content)|Esta operación obtiene el contenido de un archivo.|
|[Crear archivo](connectors-create-api-ftp.md#create-file)|Esta operación crea un archivo.|
|[Copiar archivo](connectors-create-api-ftp.md#copy-file)|Esta operación copia un archivo en un servidor FTP.|
|[Enumerar archivos de la carpeta](connectors-create-api-ftp.md#list-files-in-folder)|Esta operación obtiene la lista de archivos y subcarpetas de una carpeta.|
|[Enumerar archivos de la carpeta raíz](connectors-create-api-ftp.md#list-files-in-root-folder)|Esta operación obtiene la lista de archivos y subcarpetas de la carpeta raíz.|
|[Extraer carpeta](connectors-create-api-ftp.md#extract-folder)|Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip).|
### Detalles de la acción

Estos son los detalles de las acciones y desencadenadores de este conector, junto con sus respuestas:



### Obtención de metadatos de archivo
Esta operación obtiene los metadatos de un archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### Actualizar archivo
Esta operación actualiza un archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Archivo|Seleccionar un archivo|
|body*|Contenido del archivo|Contenido del archivo|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### Eliminar archivo
Esta operación elimina un archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.




### Obtener metadatos de archivo mediante la ruta de acceso
Esta operación obtiene los metadatos de un archivo mediante la ruta de acceso.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|path*|Ruta de acceso del archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### Obtener contenido de archivo mediante la ruta de acceso
Esta operación obtiene el contenido de un archivo mediante la ruta de acceso.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|path*|Ruta de acceso del archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.




### Obtener contenido de archivo
Esta operación obtiene el contenido de un archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.




### Crear archivo
Esta operación crea un archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|folderPath*|Ruta de acceso a la carpeta|Seleccionar una carpeta|
|name*|Nombre de archivo|Nombre del archivo|
|body*|Contenido del archivo|Contenido del archivo|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### Copiar archivo
Esta operación copia un archivo en un servidor FTP.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|source*|Dirección URL de origen|Dirección URL al archivo de origen|
|destination*|Ruta de acceso del archivo de destino|Ruta de acceso al archivo de destino, incluido el nombre del archivo de destino|
|overwrite|¿Sobrescribir?|Sobrescribe el archivo de destino si está establecido en 'true'|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### When a file is added or modified (Cuando se agrega o modifica un archivo)
Esta operación desencadena un flujo cuando se agrega o modifica un archivo en una carpeta.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|folderId*|Carpeta|Seleccionar una carpeta|

El símbolo * indica que la propiedad es obligatoria.




### Enumerar archivos de la carpeta
Esta operación obtiene la lista de archivos y subcarpetas de una carpeta.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Carpeta|Seleccionar una carpeta|

El símbolo * indica que la propiedad es obligatoria.



#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### Enumerar archivos de la carpeta raíz
Esta operación obtiene la lista de archivos y subcarpetas de la carpeta raíz.


No hay parámetros para esta llamada

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### Extraer carpeta
Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip).


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|source*|Ruta de acceso del archivo de origen|Ruta de acceso al archivo de almacenamiento|
|destination*|Ruta de acceso a la carpeta de destino|Ruta de acceso a la carpeta de destino|
|overwrite|¿Sobrescribir?|Sobrescribe los archivos de destino si está establecido en 'true'|

El símbolo * indica que la propiedad es obligatoria.



#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|



## Respuestas HTTP

Las acciones y los desencadenadores anteriores pueden devolver uno o varios de los siguientes códigos de estado HTTP:

|Name|Descripción|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido.|
|default|Error en la operación.|







## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->