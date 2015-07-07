<properties 
   pageTitle="Creación de un conector de SharePoint para usarlo en la aplicación lógica" 
   description="Creación de un conector de SharePoint; Uso del conector de SharePoint en una aplicación lógica" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/17/2015"
   ms.author="vagarw"/>

# Uso del conector de SharePoint en una aplicación lógica

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. El conector de Microsoft SharePoint permite conectarse a Microsoft SharePoint Server y SharePoint Online con objeto de administrar documentos y elementos de lista. Puede realizar diversas acciones tales como crear, actualizar, obtener y eliminar documentos y elementos de lista. Si SharePoint Server está instalado localmente, puede proporcionar la cadena de conexión del bus de servicio como parte de la configuración del conector e instalar el agente de escucha local para conectarse al servidor.

La aplicación de galería del conector de SharePoint Server y el conector de SharePoint Online proporciona desencadenadores y acciones como mecanismos para interactuar con SharePoint.

## Creación de un conector de SharePoint Online

Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Seleccione **Aplicaciones de API** y busque "Conector de SharePoint Online".
3. Escriba el nombre, el plan del Servicio de aplicaciones y otras propiedades.
4. Escriba la siguiente configuración del paquete:

	Nombre | Obligatorio | Descripción
--- | --- | ---
Dirección URL del sitio | Sí | Especifique la dirección URL completa del sitio web de SharePoint. Por ejemplo, escriba: *https://microsoft.sharepoint.com/teams/wabstest*. Biblioteca de documentos/Lista de URL relacionadas | Sí | Especifique las direcciones URL de las URL de listas/bibliotecas de documentos relacionadas con la dirección URL del sitio de SharePoint que puede modificar el conector. Por ejemplo, escriba: *Listas/Tareas, Documentos compartidos*.

5. Cuando haya terminado, la configuración del paquete tiene un aspecto similar al siguiente: <br/> ![][1]

Una vez hecho esto, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de SharePoint Online.

## Creación de un conector de SharePoint Server

Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Seleccione **Aplicaciones de API** y busque "Conector de SharePoint Server".
3. Escriba el nombre, el plan del Servicio de aplicaciones y otras propiedades.
4. Escriba la siguiente configuración del paquete:

	Nombre | Obligatorio | Descripción
--- | --- | ---
Dirección URL del sitio | Sí | Especifique la dirección URL completa del sitio web de SharePoint. Por ejemplo, escriba: *https://microsoft.sharepoint.com/teams/wabstest*. Modo de autenticación | Sí | Especifique el modo de autenticación para conectarse al sitio de SharePoint. Las opciones incluyen:<ul><li>Default</li><li>WindowsAuthentication</li><li>FormBasedAuthentication</li></ul><br/><br/>Si selecciona Default, se usan las credenciales con las que se ejecuta el conector de SharePoint; no se necesitan ni el nombre de usuario ni la contraseña. El nombre de usuario y la contraseña son necesarios para otros tipos de autenticación.<br/><br/>**Nota** No se admite la autenticación anónima.
User Name | No | Especifique un nombre de usuario válido para conectarse al sitio de SharePoint, en caso de que el modo de autenticación no sea el predeterminado.
Password | No | Especifique una contraseña válida para conectarse al sitio de SharePoint, en caso de que el modo de autenticación no sea el predeterminado.
Biblioteca de documentos/Lista de URL relacionadas | Sí | Especifique las direcciones URL de las URL de listas/bibliotecas de documentos relacionadas con la dirección URL del sitio de SharePoint que puede modificar el conector. Por ejemplo, escriba: *Listas/Tareas, Documentos compartidos*.
Cadena de conexión del bus de servicio | No | Si se conecta en un entorno local, escriba la cadena de conexión de Retransmisión de bus de servicio.<br/><br/>[Uso del Administrador de conexiones híbridas](app-service-logic-hybrid-connection-manager.md)<br/>[Precios de Bus de servicio](http://azure.microsoft.com/pricing/details/service-bus/)

5. Cuando haya terminado, la configuración del paquete tiene un aspecto similar al siguiente: <br/> ![][2]

Una vez hecho esto, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de SharePoint Server.


## Uso del conector de SharePoint en una aplicación lógica

Una vez creada la aplicación de API, puede usar el conector de SharePoint como un desencadenador o una acción para la aplicación lógica. Para ello, necesita lo siguiente:

1. Cree una aplicación lógica nueva y elija el mismo grupo de recursos que tiene el conector de SharePoint.

2. Abra **Desencadenadores y acciones** para abrir el diseñador de aplicaciones lógicas y configure el flujo. El conector de SharePoint aparece en la sección "Usados recientemente" en la galería del lado derecho. Selecciónelo.

3. Si se selecciona el conector de SharePoint al principio de la aplicación lógica, actúa como desencadenador. De lo contrario, podrían realizarse acciones en la cuenta de SharePoint mediante el conector.

4. Al usar el conector de SharePoint Online, se debe autenticar y autorizar las aplicaciones lógicas para realizar operaciones en su nombre. Para iniciar la autorización, haga clic en **Autorizar** en el conector de SharePoint:<br/> ![][3]

5. Al hacer clic en Autorizar, se abre el cuadro de diálogo de autenticación de SharePoint. Proporcione los detalles de inicio de sesión de la cuenta en la que desea realizar las operaciones: <br/> ![][4]

6. Conceda acceso a su cuenta a las aplicaciones lógicas para llevar a cabo la operación en su nombre: <br/> ![][5]

7. Si el conector de SharePoint está configurado como desencadenador, se muestran los desencadenadores. De lo contrario, se muestra una lista de acciones y puede elegir la operación apropiada que se desea realizar: <br/> ![][6] <br/> **Dirección URL relativa configurada para la biblioteca de documentos** <br/> ![][7] <br/> **Dirección URL relativa configurada para la lista de documentos** <br/>

> [AZURE.NOTE]Para los siguientes desencadenadores, se supone que ha especificado “Documentos compartidos, Listas/Tareas” en la configuración del paquete del conector, donde “Documentos compartidos” es una biblioteca de documentos y “Listas/Tareas” es una lista.

##  Desencadenadores
Utilice desencadenadores si desea iniciar una aplicación lógica.

> [AZURE.NOTE]Los desencadenadores eliminan los archivos después de leerlos. Para conservar estos archivos, proporcione un valor para la ubicación de archivado.

### 1. Nuevo documento en los documentos compartidos (JSON)
Este desencadenador se activa cuando hay un documento disponible en ’Documentos compartidos’.

#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
Nombre de vista | No | Especifique una vista válida para filtrar los documentos que se deben seleccionar. Por ejemplo, escriba “Pedidos aprobados”. Para procesar todos los documentos existentes, deje este campo en blanco. 
Ubicación del archivo | No | Especifique una dirección URL de carpeta válida, relativa al sitio de SharePoint, donde se archivan los documentos procesados.
Sobrescribir en archivo | No | Active esta opción para sobrescribir un archivo en la ruta de los archivos, en caso de que ya exista.
Consulta Caml | No, avanzado | Especifique una consulta Caml válida para filtrar documentos. Por ejemplo, escriba: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Salida

Nombre | Descripción
--- | --- 
Nombre | Nombre del documento.
Contenido | Contenido del documento.
ContentTransferEncoding | Codificación de transferencia de contenido del mensaje. ("none"o ”base64”)

**Nota**: todas las columnas del elemento de documento se muestran en la salida “Avanzadas”.


### 2. Nuevo elemento en las tareas (JSON)
Este desencadenador se activa cuando se agrega un nuevo elemento a la lista ’Tareas’.

#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
Nombre de vista | No | Especifique una vista válida utilizada para filtrar elementos en la lista. Por ejemplo, escriba “Pedidos aprobados”. Para procesar todos los nuevos elementos, deje este campo en blanco. 
Ubicación del archivo | No | Especifique una dirección URL de carpeta válida, relativa al sitio de SharePoint, donde se archivan los elementos de lista procesados.
Consulta Caml | No, avanzado | Especifique una consulta Caml válida para filtrar documentos. Por ejemplo, escriba: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Salida

Nombre | Descripción
--- | --- 
Las columnas de la lista se rellenan dinámicamente y se muestran en los parámetros de salida. | &nbsp;


### 3. Nuevo documento en los documentos compartidos (XML)

Este desencadenador se activa cuando hay un documento disponible en ’Documentos compartidos’. El nuevo documento se devuelve como un mensaje XML.

#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
Nombre de vista | No | Especifique una vista válida para filtrar los documentos que se deben seleccionar. Por ejemplo, escriba “Pedidos aprobados”. Para procesar todos los documentos existentes, deje este campo en blanco. 
Ubicación del archivo | No | Especifique una dirección URL de carpeta válida, relativa al sitio de SharePoint, donde se archivan los documentos de lista procesados.
Sobrescribir en archivo | No | Active esta opción para sobrescribir un archivo en la ruta de los archivos, en caso de que ya exista.
Consulta Caml | No, avanzado | Especifique una consulta Caml válida para filtrar documentos. Por ejemplo, escriba: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Salida

Nombre | Descripción
--- | --- 
Contenido | Contenido del documento.
ContentTransferEncoding | Codificación de transferencia de contenido del mensaje. ("none"o ”base64”)


### 4. Nuevo elemento en las tareas (XML)

Este desencadenador se activa cuando se agrega un nuevo elemento a la lista ’Tareas’. El nuevo elemento de lista se devuelve como un mensaje XML.

#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
Nombre de vista | No | Especifique una vista válida utilizada para filtrar elementos en la lista. Ejemplo: 'Pedidos aprobados'. Para procesar todos los nuevos elementos, deje este campo en blanco. 
Ubicación del archivo | No | Especifique una dirección URL de carpeta válida, relativa al sitio de SharePoint, donde se archivan los elementos de lista procesados.
Consulta Caml | No, avanzado | Especifique una consulta Caml válida para filtrar los elementos de lista. Por ejemplo, escriba `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Salida

Nombre | Descripción
--- | --- 
Contenido | Contenido del documento.
ContentTransferEncoding | Codificación de transferencia de contenido del mensaje. ("none"o ”base64”)


## Acciones
Para las acciones siguientes, se supone que el usuario especificó ’Documentos compartidos, Listas/Tareas’ en la configuración del paquete del conector, donde ’Documentos compartidos’ es una biblioteca de documentos y ’Listas/Tareas’ es una lista.

### 1. Elemento cargado en los documentos compartidos (JSON)

Esta acción carga un nuevo documento en ’Documentos compartidos’. La entrada es un objeto JSON bien definido con todos los campos de columna de la biblioteca de documentos.

#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
Nombre | Sí | Nombre del documento.
Contenido | Sí | Contenido del documento.
ContentTransferEncoding | Sí | Codificación de transferencia de contenido del mensaje. ("none"o ”base64”)
Forzar sobrescritura | Sí | Si se establece como TRUE y existe un documento con el nombre especificado, este se sobrescribirá.
ReqParam1* | Sí | Se trata de uno de los parámetros necesarios para agregar un documento a la biblioteca de documentos.
ReqParam2* | Sí | Se trata de uno de los parámetros necesarios para agregar un documento a la biblioteca de documentos.
OptionalParam1* | No. Avanzado | Se trata de uno de los parámetros opcionales para agregar un documento a la biblioteca de documentos.
OptionalParam2* | No. Avanzado | Se trata de uno de los parámetros opcionales para agregar un documento a la biblioteca de documentos.

**Nota**: todos los parámetros de la biblioteca de documentos se rellenan de forma dinámica. Los parámetros obligatorios están visibles, mientras que los parámetros opcionales se encuentran en la sección avanzada.


#### Salida

Nombre | Descripción
--- | --- 
ItemId | Id. de elemento del documento agregado a la biblioteca de documentos.
Estado | Si la carga del documento es correcta, se devuelve el código de estado 200 (correcto).
 

### 2. Obtención de elementos desde los documentos compartidos (JSON)
Esta acción obtiene el documento de la biblioteca de documentos con la URL relativa (estructura de carpetas) del documento.

#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
Dirección URL relativa del documento | No | Especifique la dirección URL del documento, relativa a “Documentos compartidos”. Por ejemplo, escriba: *myspec1,myfolder/orders*.


#### Salida

Nombre | Descripción
--- | --- 
Contenido | Contenido del documento 
ContentTransferEncoding | Codificación de transferencia de contenido del mensaje. ("none" o ”base64”)
Estado | Si la ejecución de la acción es correcta, se devuelve el código de estado 200 (correcto).
Param1* | Se trata de uno de los parámetros de un documento en la biblioteca de documentos.
Param2* | Se trata de uno de los parámetros de un documento en la biblioteca de documentos.

**Nota**: todos los parámetros de la biblioteca de documentos se rellenan de forma dinámica. Y se encuentran en la sección avanzada.

 

### 3. Eliminación de elementos de los documentos compartidos

Esta acción elimina el documento de la biblioteca de documentos con la URL relativa (estructura de carpetas) del documento.

#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
Dirección URL relativa del documento | No | Especifique la dirección URL del documento, relativa a “Documentos compartidos”. Por ejemplo, escriba: *myspec1,myfolder/orders*.

#### Salida

Nombre | Descripción
--- | --- 
Estado | Si la ejecución de la acción es correcta, se devuelve el código de estado 200 (correcto).


### 4. Inserción de elementos en las tareas (JSON)

Esta acción agrega un elemento a la lista de elementos.

#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
ReqParam1* | Sí | Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.
ReqParam2* | Sí | Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.
OptionalParam1* | No. Avanzado | Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.
OptionalParam2* | No. Avanzado | Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.

**Nota**: todos los parámetros de la “Lista” se rellenan de forma dinámica. Los parámetros obligatorios están visibles, mientras que los parámetros opcionales se encuentran en la sección avanzada.

 
#### Salida

Nombre | Descripción
--- | --- 
ItemId | Id. de elemento del elemento de la lista agregado.
Estado | Si la inserción del elemento de lista es correcta, se devuelve el código de estado 200 (correcto).


### 5. Actualización de un elemento en las tareas (JSON)

Esta acción actualiza un elemento de la lista de elementos.

#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
ItemId | Sí | Id. de elemento del elemento de la lista.
ReqParam1* | No | Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.
ReqParam2* | No | Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.
OptionalParam1* | No. Avanzado | Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.
OptionalParam2* | No. Avanzado | Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.

**Nota**: todos los parámetros de la “Lista” se rellenan de forma dinámica. Los parámetros obligatorios están visibles, mientras que los parámetros opcionales se encuentran en la sección avanzada.


#### Salida

Nombre | Descripción
--- | --- 
Estado | Si la actualización del elemento de lista es correcta, se devuelve el código de estado 200 (correcto).


### 6. Obtención de elementos desde las tareas (JSON)

Esta acción recupera un elemento de la lista de elementos.

#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
ItemId | Sí | Id. de elemento del elemento de la lista.


#### Salida

Nombre | Descripción
--- | --- 
Column1* | Se trata de uno de los parámetros en la lista.
Column2* | Se trata de uno de los parámetros en la lista.
Estado | Si la ejecución de la acción es correcta, se devuelve el código de estado 200 (correcto).

**Nota**: las columnas de la lista se rellenan de forma dinámica y se muestran en los parámetros de salida.


### 7. Eliminación de un elemento desde las tareas

Esta acción elimina un elemento de la lista de elementos.

 
#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
ItemId | Sí | Id. de elemento del elemento de la lista.


#### Salida

Nombre | Descripción
--- | --- 
Estado | Si la eliminación del elemento de lista es correcta, se devuelve el código de estado 200 (correcto).


### 8. Enumeración de elementos en los documentos compartidos (JSON)

Esta acción enumera todos los documentos incluidos en una biblioteca de documentos. Puede utilizar una vista o una consulta Caml para filtrar los documentos.

 
#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
Nombre de vista | No | Especifique una vista válida para filtrar los documentos que se deben seleccionar. Por ejemplo, escriba “Pedidos aprobados”. Para procesar todos los documentos existentes, deje este campo en blanco. 
Consulta Caml | No | Especifique una consulta Caml válida para filtrar documentos. Por ejemplo, escriba: `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Salida

Nombre | Descripción
--- | --- 
Documentos | Conjunto de todos los documentos. Cada documento tiene los siguientes campos: <ul><li>Documentos </li><li>Nombre</li><li>Id. de elemento</li><li>URL completa del elemento</li><li>Avanzado</li><li>URL de edición de elemento</li><li>Nombre de lista</li><li>URL completa de la lista</li></ul>
Estado | Si la inserción del elemento de lista es correcta, se devuelve el código de estado 200 (correcto).


### 9. Elemento cargado en los documentos compartidos (XML)

Esta acción carga un nuevo documento en ’Documentos compartidos’. El documento de entrada debe ser una carga XML. La respuesta de la acción será una carga XML.
 

#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
Nombre | Sí | Nombre del documento.
Contenido | Sí | Contenido del documento.
ContentTransferEncoding | Sí | Codificación de transferencia de contenido del mensaje. ("none" o ”base64”)
Forzar sobrescritura | Sí | Si se establece como TRUE y existe un documento con el nombre especificado, este se sobrescribe.
 

#### Salida

Nombre | Descripción
--- | --- 
XML de salida | Respuesta de la acción de carga en formato XML.
Estado | Si la carga del documento es correcta, se devuelve el código de estado 200 (correcto).

### 10. Obtención de elementos desde los documentos compartidos (XML)

Esta acción obtiene el documento de la biblioteca de documentos con la URL relativa (estructura de carpetas) del documento.

 
#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
Dirección URL relativa del documento | No | Especifique la dirección URL del documento, relativa a “Documentos compartidos”. Por ejemplo, escriba: *myspec1,myfolder/orders*.
Tipo de archivo | Sí | Especifique si el archivo es binario o de texto.

#### Salida

Nombre | Descripción
--- | --- 
XML de salida | Contenido del documento
ContentTransferEncoding | Codificación de transferencia de contenido del mensaje. ("none" o ”base64”)
Estado | Si la ejecución de la acción es correcta, se devuelve el código de estado 200 (correcto).

### 11. Inserción de elementos en las tareas (XML)

Esta acción agrega un elemento a la lista de elementos. La entrada se espera que sea una carga XML.

#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
XML de entrada | Sí | El mensaje XML que contiene los valores de los campos de elemento de lista que se va a insertar. Puede utilizar la aplicación de API de transformación para generar el mensaje XML.

**Nota**: todos los parámetros de la “Lista” se rellenan de forma dinámica. Los parámetros obligatorios están visibles, mientras que los parámetros opcionales se encuentran en la sección avanzada.

 
#### Salida

Nombre | Descripción
--- | ---
ItemId | Id. de elemento del elemento de la lista agregado.
Estado | Si la inserción del elemento de lista es correcta, se devuelve el código de estado 200 (correcto).


### 12. Actualización de un elemento en las tareas (XML)

Esta acción actualiza un elemento de la lista de elementos. La entrada se espera que sea una carga XML.

#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
ItemID | Sí | Id. de elemento del elemento de la lista.
XML de entrada | Sí | El mensaje XML que contiene los valores de los campos de elemento de lista que se va a insertar. Puede utilizar la aplicación de API de transformación para generar el mensaje XML.

**Nota**: todos los parámetros de la “Lista” se rellenan de forma dinámica. Los parámetros obligatorios están visibles, mientras que los parámetros opcionales se encuentran en la sección avanzada.
 
#### Salida

Nombre | Descripción
--- | ---
Estado | Si la actualización del elemento de lista es correcta, se devuelve el código de estado 200 (correcto).


### 13. Obtención de elementos desde las tareas (XML)

Esta acción recupera un elemento de la lista de elementos.

#### Entrada

Nombre | Obligatorio | Descripción
--- | --- | ---
ItemID | Sí | Id. de elemento del elemento de la lista.


#### Salida

Nombre | Descripción
--- | ---
XML de salida | El mensaje XML que contiene los valores de los campos de elemento de lista seleccionado.
Estado | Si la ejecución de la acción es correcta, se devuelve el código de estado 200 (correcto).


## Configuración híbrida (opcional)

> [AZURE.NOTE]Este paso solo es necesario si está utilizando SharePoint local detrás del firewall.

El Servicio de aplicaciones utiliza el Administrador de configuración híbrida para conectarse de forma segura al sistema local. Si el conector usa SharePoint Server en un entorno local, se necesita el Administrador de conexiones híbridas.

Consulte [Uso del Administrador de conexiones híbridas](app-service-logic-hybrid-connection-manager.md).

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de conectores y aplicaciones de API](../app-service-api/app-service-api-manage-in-portal.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png
 

<!---HONumber=62-->