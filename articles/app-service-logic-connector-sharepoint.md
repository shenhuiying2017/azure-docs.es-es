<properties 
   pageTitle="Uso del conector de SharePoint en una aplicación lógica" 
   description="Uso del conector de SharePoint en una aplicación lógica" 
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
   ms.date="03/20/2015"
   ms.author="vagarw"/>

# Uso del conector de SharePoint en una aplicación lógica

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. El conector de Microsoft SharePoint permite conectarse a Microsoft SharePoint Server y SharePoint Online con objeto de administrar documentos y elementos de lista. Puede realizar diversas acciones tales como crear, actualizar, obtener y eliminar documentos y elementos de lista. Si SharePoint Server está instalado localmente, puede proporcionar la cadena de conexión del bus de servicio como parte de la configuración del conector e instalar el agente de escucha local para conectarse al servidor.

La aplicación de galería del conector de SharePoint Server y el conector de SharePoint Online proporciona desencadenadores y acciones como mecanismos para interactuar con SharePoint.

## Creación de un conector de SharePoint Online para la aplicación lógica

Para usar el conector de SharePoint Online, deberá crear primero una instancia de la aplicación de API del conector de SharePoint Online. Se puede hacer de la forma siguiente:

1. Abra Azure Marketplace mediante la opción +NUEVO en la parte inferior derecha del Portal de Azure.

2. Vaya a "Web y móvil > Aplicaciones de API" y busque "Conector de SharePoint Online".

3. Configure el conector de SharePoint Online y haga clic en Crear. A continuación, figuran los parámetros que deberá proporcionar para crear el conector:

	<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Dirección URL del sitio</td>
    <td>Sí</td>
    <td>Especifique la dirección URL completa del sitio web de SharePoint. Por ejemplo: https://microsoft.sharepoint.com/teams/wabstest </td>
  </tr>
  <tr>
    <td>Biblioteca de documentos/Lista de URL relacionadas</td>
    <td>Sí</td>
    <td>Especifique las direcciones URL de las URL de listas/bibliotecas de documentos relacionadas con la dirección URL del sitio de SharePoint que puede modificar el conector. Por ejemplo: Listas/Tareas, Documentos compartidos'.</td>
  </tr>
</table>![][1]


4. Una vez hecho esto, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de SharePoint Online.

## Creación de un conector de SharePoint Server para la aplicación lógica

Para usar el conector de SharePoint Server, deberá crear primero una instancia de la aplicación de API del conector de SharePoint Server. Se puede hacer de la forma siguiente:

1. Abra Azure Marketplace mediante la opción +NUEVO en la parte inferior derecha del Portal de Azure.

2. Vaya a "Web y móvil > Aplicaciones de API" y busque "Conector de SharePoint Server".

3. Configure el conector de SharePoint Server y haga clic en Crear. A continuación, figuran los parámetros que deberá proporcionar para crear el conector:

	<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Dirección URL del sitio</td>
    <td>Sí</td>
    <td>Especifique la dirección URL completa del sitio web de SharePoint. Por ejemplo: https://microsoft.sharepoint.com/teams/wabstest </td>
  </tr>
  <tr>
    <td>Modo de autenticación</td>
    <td>Sí</td>
    <td>Especifique el modo de autenticación para conectarse al sitio de SharePoint. Los valores permitidos son los siguientes:<br><br>
		Valor predeterminado<br>			
		WindowsAuthentication<br>
		FormBasedAuthentication.<br><br>

Si se elige Credenciales predeterminadas, se utilizarán las credenciales predeterminadas con las que se está ejecutando SharePoint Microservice (el nombre de usuario y la contraseña no serán necesarios). Los campos Nombre de usuario y Contraseña son obligatorios para otros tipos de autenticación. <br><br>Nota: no se admite la autenticación anónima.</td>
  </tr>
  <tr>
    <td>User Name</td>
    <td>No</td>
    <td>Especifique un nombre de usuario válido para conectarse al sitio de SharePoint, en caso de que el modo de autenticación no sea el predeterminado.</td>
  </tr>
  <tr>
    <td>Password</td>
    <td>No</td>
    <td>Especifique una contraseña válida para conectarse al sitio de SharePoint, en caso de que el modo de autenticación no sea el predeterminado.</td>
  </tr>
  <tr>
    <td>Biblioteca de documentos/Lista de URL relacionadas</td>
    <td>Sí</td>
    <td>Especifique las direcciones URL de las URL de listas/bibliotecas de documentos relacionadas con la dirección URL del sitio de SharePoint que puede modificar el conector. Por ejemplo: Listas/Tareas, Documentos compartidos'.</td>
  </tr>
  <tr>
    <td>Cadena de conexión del bus de servicio</td>
    <td>No</td>
    <td>Debe ser una cadena de conexión del espacio de nombres del bus de servicio que sea válida.<br><br>

Deberá instalar un agente de escucha en un servidor que pueda acceder a SharePoint Server. <br>Puede ir a la página de resumen de la aplicación de API y hacer clic en «Conexión híbrida» para instalar el agente.</td>
  </tr>
</table>![][2]

4. Una vez hecho esto, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de SharePoint Server.
5. Deberá instalar un agente de escucha en un servidor que pueda acceder a SharePoint Server. Puede ir a la página de resumen de la aplicación de API y hacer clic en «Conexión híbrida» para instalar el agente.

## Uso del conector de SharePoint en una aplicación lógica

Una vez creada la aplicación de API, puede usar el conector de SharePoint como un desencadenador o una acción para la aplicación lógica. Para ello, necesita lo siguiente:

1. Cree una aplicación lógica nueva y elija el mismo grupo de recursos que tiene el conector de SharePoint.

2. Abra "Desencadenadores y acciones" para abrir el diseñador de aplicaciones lógicas y configure el flujo. El conector de SharePoint aparecerá en la sección "Usados recientemente" en la galería del lado derecho. Selecciónelo.

3. Si se elige el conector de SharePoint al inicio de la aplicación lógica, este actúa como desencadenador y, además, las acciones se podrían realizar en la cuenta de SharePoint mediante el conector.

4. Se debe autenticar y autorizar las aplicaciones lógicas para realizar operaciones en su nombre si se usa el conector SharePoint Online. Para iniciar la autorización, haga clic en Autorizar en el conector de SharePoint.

	![][3]

5. Al hacer clic en Autorizar, se abre el cuadro de diálogo de autenticación de SharePoint. Proporcione los detalles de inicio de sesión de la cuenta de SharePoint en la que desea realizar las operaciones.

	![][4]
6. Conceda acceso a su cuenta a las aplicaciones lógicas para llevar a cabo la operación en su nombre. 

	![][5]

7. Si el conector de SharePoint está configurado como desencadenador, se muestran los desencadenadores, Además, se muestra la lista de acciones para que pueda elegir la operación que desee realizar.

	![][6]

	<b>Dirección URL relativa configurada para la biblioteca de documentos</b><br><br>

	![][7]

	<b>Dirección URL relativa configurada para la lista de documentos</b>

	<b>Nota:</b> para los siguientes desencadenadores, se supone que el usuario especificó ’Documentos compartidos, Listas/Tareas’ en la configuración del paquete del conector, donde ’Documentos compartidos’ es una biblioteca de documentos y ’Listas/Tareas’ es una lista.

##  Desencadenadores
Utilice desencadenadores si desea iniciar una aplicación lógica.

**Nota**: los desencadenadores se eliminarán los archivos después de leerlos. Para conservar estos archivos, proporcione un valor para la ubicación de archivado.

### 1. Nuevo documento en los documentos compartidos (JSON)
Este desencadenador se activa cuando hay un documento disponible en ’Documentos compartidos’.

**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Nombre de vista</td>
    <td>No</td>
    <td>Especifique una vista válida para filtrar los documentos que se deben seleccionar. Ejemplo: 'Pedidos aprobados'. Para procesar todos los documentos existentes, deje este campo en blanco. </td>
  </tr>
  <tr>
    <td>Ubicación del archivo</td>
    <td>No</td>
    <td>Especifique una dirección URL de carpeta válida, relativa al sitio de SharePoint, donde se archivan los documentos procesados. </td>
  </tr>
  <tr>
    <td>Sobrescribir en archivo</td>
    <td>No</td>
    <td>Active esta opción para sobrescribir un archivo en la ruta de los archivos, en caso de que ya exista. </td>
  </tr>
  <tr>
    <td>Consulta Caml</td>
    <td>No, avanzado</td>
    <td>Especifique una consulta Caml válida para filtrar documentos. Ejemplo: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**Salidas:** <table> <tr> <td><b>Nombre</b></td> <td><b>Descripción</b></td> </tr> <tr> <td>Nombre</td> <td>Nombre del documento.</td> </tr> <tr> <td>Contenido</td> <td>Contenido del documento.</td> </tr> <tr> <td>ContentTransferEncoding</td> <td>Codificación de transferencia de contenido del mensaje. ("none"|”base64”)</ </tr> </table>


Nota: todas las columnas del elemento de documento se muestran en la salida ’Avanzadas’.


###2. Nuevo elemento en las tareas (JSON)
Este desencadenador se activa cuando se agrega un nuevo elemento a la lista ’Tareas’.

**Entradas:** <table> <tr> <td><b>Nombre</b></td> <td><b>Obligatorio</b></td> <td><b>Descripción</b></td> </tr> <tr> <td>Nombre de vista</td> <td>No</td> <td>Especifique una vista válida utilizada para filtrar elementos en la lista. Ejemplo: 'Pedidos aprobados'. Para procesar todos los nuevos elementos, deje este campo en blanco. </td> </tr> <tr> <td>Ubicación de archivo</td> <td>No</td> <td>Especifique una dirección URL de carpeta válida, relativa al sitio de SharePoint, donde se archivan los elementos de lista procesados. </td> </tr> <tr> <td>Consulta Caml</td> <td>No, Avanzadas</td> <td>Especifique una consulta Caml válida para filtrar los elementos de la lista. Ejemplo: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td> </tr> </table>


**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Las columnas de la lista se rellenan dinámicamente y se muestran en los parámetros de salida.</td>
    <td>&#160;</td>
  </tr>

</table>


###3. Nuevo documento en los documentos compartidos (XML)

Este desencadenador se activa cuando hay un documento disponible en ’Documentos compartidos’. El nuevo documento se devuelve como un mensaje XML.

**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Nombre de vista</td>
    <td>No</td>
    <td>Especifique una vista válida para filtrar los documentos que se deben seleccionar. Ejemplo: 'Pedidos aprobados'. Para procesar todos los documentos existentes, deje este campo en blanco. </td>
  </tr>
  <tr>
    <td>Ubicación del archivo</td>
    <td>No</td>
    <td>Especifique una dirección URL de carpeta válida, relativa al sitio de SharePoint, donde se archivan los documentos procesados. </td>
  </tr>
  <tr>
    <td>Sobrescribir en archivo</td>
    <td>No</td>
    <td>Active esta opción para sobrescribir un archivo en la ruta de los archivos, en caso de que ya exista. </td>
  </tr>
  <tr>
    <td>Consulta Caml</td>
    <td>No, avanzado</td>
    <td>Especifique una consulta Caml válida para filtrar documentos. Ejemplo: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Contenido</td>
    <td>Contenido del documento.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Codificación de transferencia de contenido del mensaje. ("none"|”base64”)</td>
  </tr>
</table>


###4. Nuevo elemento en las tareas (XML)

Este desencadenador se activa cuando se agrega un nuevo elemento a la lista ’Tareas’. El nuevo elemento de lista se devuelve como un mensaje XML.

**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Nombre de vista</td>
    <td>No</td>
    <td>Especifique una vista válida utilizada para filtrar elementos en la lista. Ejemplo: 'Pedidos aprobados'. Para procesar todos los nuevos elementos, deje este campo en blanco. </td>
  </tr>
  <tr>
    <td>Ubicación del archivo</td>
    <td>No</td>
    <td>Especifique una dirección URL de carpeta válida, relativa al sitio de SharePoint, donde se archivan los elementos de lista procesados. </td>
  </tr>
  <tr>
    <td>Consulta Caml</td>
    <td>No, avanzado</td>
    <td>Especifique una consulta Caml válida para filtrar los elementos de la lista. Ejemplo: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Contenido</td>
    <td>Contenido del documento.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Codificación de transferencia de contenido del mensaje. ("none"|”base64”)</td>
  </tr>
</table>


##  Acciones
Para las acciones siguientes, se supone que el usuario especificó ’Documentos compartidos, Listas/Tareas’ en la configuración del paquete del conector, donde ’Documentos compartidos’ es una biblioteca de documentos y ’Listas/Tareas’ es una lista.

###1. Elemento cargado en los documentos compartidos (JSON)

Esta acción carga un nuevo documento en ’Documentos compartidos’. La entrada es un objeto JSON bien definido con todos los campos de columna de la biblioteca de documentos.

**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
 </tr>
  <tr>
    <td>Nombre</td>
    <td>Sí</td>
    <td>Nombre del documento.</td>
  </tr>
  <tr>
    <td>Contenido</td>
    <td>Sí</td>
    <td>Contenido del documento.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Sí</td>
    <td>Codificación de transferencia de contenido del mensaje. ("none"|”base64”)</td>
  </tr>
  <tr>
    <td>Forzar sobrescritura</td>
    <td>Sí</td>
    <td>Si se establece como TRUE y existe un documento con el nombre especificado, este se sobrescribirá.</td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>Sí</td>
    <td>Se trata de uno de los parámetros necesarios para agregar un documento a la biblioteca de documentos.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>Sí</td>
    <td>Se trata de uno de los parámetros necesarios para agregar un documento a la biblioteca de documentos.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>No. Avanzado</td>
    <td>Se trata de uno de los parámetros opcionales para agregar un documento a la biblioteca de documentos.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>No. Avanzado</td>
    <td>Se trata de uno de los parámetros opcionales para agregar un documento a la biblioteca de documentos.</td>
  </tr>
</table>

<b>Nota</b>: todos los parámetros de la biblioteca de documentos se rellenan de forma dinámica. Los parámetros obligatorios están visibles, mientras que los parámetros opcionales se encuentran en la sección avanzada.


**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Id. de elemento del documento agregado a la biblioteca de documentos.</td>
  </tr>
  <tr>
    <td>Estado</td>
    <td>Si la carga del documento es correcta, se devuelve el código de estado 200 (correcto).</td>
  </tr>
</table>


 

###2. Obtención de elementos desde los documentos compartidos (JSON)
Esta acción obtiene el documento de la biblioteca de documentos con la URL relativa (estructura de carpetas) del documento.


**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Dirección URL relativa del documento</td>
    <td>No</td>
    <td>Especifique la dirección URL del documento, relativa a 'Documentos compartidos'. Ejemplo: myspec1,myfolder/orders</td>
  </tr>
</table>


**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Contenido</td>
    <td>Contenido del documento</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Codificación de transferencia de contenido del mensaje. ("none"|”base64”)</td>
  </tr>
  <tr>
    <td>Estado</td>
    <td>Si la ejecución de la acción es correcta, se devuelve el código de estado 200 (correcto).</td>
  </tr>
  <tr>
    <td>Param1*</td>
    <td>Se trata de uno de los parámetros de un documento en la biblioteca de documentos.</td>
  </tr>
  <tr>
    <td>Param2*</td>
    <td>Se trata de uno de los parámetros de un documento en la biblioteca de documentos.</td>
  </tr>
</table>

<b>Nota</b>: todos los parámetros de la biblioteca de documentos se rellenan de forma dinámica. Y se encuentran en la sección avanzada.

 

###3. Eliminación de elementos de los documentos compartidos

Esta acción elimina el documento de la biblioteca de documentos con la URL relativa (estructura de carpetas) del documento.

**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Dirección URL relativa del documento</td>
    <td>No</td>
    <td>Especifique la dirección URL del documento, relativa a 'Documentos compartidos'. Ejemplo: myspec1,myfolder/orders</td>
  </tr>
</table>


**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Estado</td>
    <td>Si la ejecución de la acción es correcta, se devuelve el código de estado 200 (correcto).</td>
  </tr>
</table>


###4. Inserción de elementos en las tareas (JSON)

Esta acción agrega un elemento a la lista de elementos.

**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>Sí</td>
    <td>Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>Sí</td>
    <td>Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>No. Avanzado</td>
    <td>Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>No. Avanzado</td>
    <td>Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.</td>
  </tr>
</table>


<b>Nota</b>: todos los parámetros de la ’Lista’ se rellenan de forma dinámica. Los parámetros obligatorios están visibles, mientras que los parámetros opcionales se encuentran en la sección avanzada.

**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Id. de elemento del elemento de la lista agregado.</td>
  </tr>
  <tr>
    <td>Estado</td>
    <td>Si la inserción del elemento de lista es correcta, se devuelve el código de estado 200 (correcto).</td>
  </tr>
</table>


###5. Actualización de un elemento en las tareas (JSON)

Esta acción actualiza un elemento de la lista de elementos.

**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Sí</td>
    <td>Id. de elemento del elemento de la lista.</td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>No</td>
    <td>Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>No</td>
    <td>Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>No. Avanzado</td>
    <td>Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>No. Avanzado</td>
    <td>Se trata de uno de los parámetros necesarios para agregar un elemento a la lista.</td>
  </tr>
</table>

<b>Nota</b>: todos los parámetros de la ’Lista’ se rellenan de forma dinámica. Los parámetros obligatorios están visibles, mientras que los parámetros opcionales se encuentran en la sección avanzada.


**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Estado  </td>
    <td>Si la actualización del elemento de lista es correcta, se devuelve el código de estado 200 (correcto).</td>
  </tr>
</table>


###6. Obtención de elementos desde las tareas (JSON)

Esta acción recupera un elemento de la lista de elementos.


**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Sí</td>
    <td>Id. de elemento del elemento de la lista.</td>
  </tr>
</table>


**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Column1*</td>
    <td>Se trata de uno de los parámetros en la lista.</td>
  </tr>
  <tr>
    <td>Column2*</td>
    <td>Se trata de uno de los parámetros en la lista.</td>
  </tr>
  <tr>
    <td>Estado</td>
    <td>Si la ejecución de la acción es correcta, se devuelve el código de estado 200 (correcto).</td>
  </tr>
</table>

<b>Nota:</b> las columnas de la lista se rellenan dinámicamente y se muestran en los parámetros de salida.


###7. Eliminación de un elemento desde las tareas

Esta acción elimina un elemento de la lista de elementos.

**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Sí</td>
    <td>Id. de elemento del elemento de la lista.</td>
  </tr>
</table>


**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Estado  </td>
    <td>Si la eliminación del elemento de lista es correcta, se devuelve el código de estado 200 (correcto).</td>
  </tr>
</table>


###8. Enumeración de elementos en los documentos compartidos (JSON)

Esta acción enumera todos los documentos incluidos en una biblioteca de documentos. Puede utilizar una vista o una consulta Caml para filtrar los documentos.

**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Nombre de vista</td>
    <td>No</td>
    <td>Especifique una vista válida para filtrar los documentos que se deben seleccionar. Ejemplo: 'Pedidos aprobados'. Para procesar todos los documentos existentes, deje este campo en blanco. </td>
  </tr>
  <tr>
    <td>Consulta Caml</td>
    <td>No</td>
    <td>Especifique una consulta Caml válida para filtrar documentos. Ejemplo: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Documentos</td>
    <td>Conjunto de todos los documentos. Cada documento tiene los siguientes campos. <br><br>
	Documentos []<br>
	Nombre<br>
	Id. de elemento<br>
	URL completa del elemento<br>
	Avanzado<br>
	URL de edición de elemento<br>
	Nombre de lista<br>
	URL completa de la lista<br>
	</td>
  </tr>
  <tr>
    <td>Estado  </td>
    <td>Si la inserción del elemento de lista es correcta, se devuelve el código de estado 200 (correcto).</td>
  </tr>
</table>


###9. Elemento cargado en los documentos compartidos (XML)

Esta acción carga un nuevo documento en ’Documentos compartidos’. El documento de entrada debe ser una carga XML. La respuesta de la acción será una carga XML.

**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Nombre</td>
    <td>Sí</td>
    <td>Nombre del documento.</td>
  </tr>
  <tr>
    <td>Contenido</td>
    <td>Sí</td>
    <td>Contenido del documento.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Sí</td>
    <td>Codificación de transferencia de contenido del mensaje. ("none"|”base64”)</td>
  </tr>
  <tr>
    <td>Forzar sobrescritura</td>
    <td>Sí</td>
    <td>Si se establece como TRUE y existe un documento con el nombre especificado, este se sobrescribirá.</td>
  </tr>
</table>
 

**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>XML de salida</td>
    <td>Respuesta de la acción de carga en formato XML.</td>
  </tr>
  <tr>
    <td>Estado  </td>
    <td>Si la carga del documento es correcta, se devuelve el código de estado 200 (correcto).</td>
  </tr>
</table>

###10. Obtención de elementos desde los documentos compartidos (XML)

Esta acción obtiene el documento de la biblioteca de documentos con la URL relativa (estructura de carpetas) del documento.

**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Dirección URL relativa del documento</td>
    <td>No</td>
    <td>Especifique la dirección URL del documento, relativa a 'Documentos compartidos'. Ejemplo: myspec1,myfolder/orders</td>
  </tr>
  <tr>
    <td>Tipo de archivo</td>
    <td>Sí</td>
    <td>Especifique si el archivo es binario o de texto.</td>
  </tr>
</table>


**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>XML de salida</td>
    <td>Contenido del documento</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Codificación de transferencia de contenido del mensaje. ("none"|”base64”)</td>
  </tr>
  <tr>
    <td>Estado</td>
    <td>Si la ejecución de la acción es correcta, se devuelve el código de estado 200 (correcto).</td>
  </tr>
</table>

###11. Inserción de elementos en las tareas (XML)

Esta acción agrega un elemento a la lista de elementos. La entrada se espera que sea una carga XML.

**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>XML de entrada</td>
    <td>Sí</td>
    <td>El mensaje XML que contiene los valores de los campos de elemento de lista que se va a insertar. Puede utilizar la aplicación de API de transformación para generar el mensaje XML.</td>
  </tr>
</table>
<b>Nota</b>: todos los parámetros de la ’Lista’ se rellenan de forma dinámica. Los parámetros obligatorios están visibles, mientras que los parámetros opcionales se encuentran en la sección avanzada.

**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Id. de elemento del elemento de la lista agregado.</td>
  </tr>
  <tr>
    <td>Estado  </td>
    <td>Si la inserción del elemento de lista es correcta, se devuelve el código de estado 200 (correcto).</td>
  </tr>
</table>


###12. Actualización de un elemento en las tareas (XML)

Esta acción actualiza un elemento de la lista de elementos. La entrada se espera que sea una carga XML.


**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Sí</td>
    <td>Id. de elemento del elemento de la lista.</td>
  </tr>
  <tr>
    <td>XML de entrada</td>
    <td>Sí</td>
    <td>El mensaje XML que contiene los valores de los campos de elemento de lista que se va a insertar. Puede utilizar la aplicación de API de transformación para generar el mensaje XML.</td>
  </tr>
</table>

<b>Nota</b>: todos los parámetros de la ’Lista’ se rellenan de forma dinámica. Los parámetros obligatorios están visibles, mientras que los parámetros opcionales se encuentran en la sección avanzada.

**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Estado  </td>
    <td>Si la actualización del elemento de lista es correcta, se devuelve el código de estado 200 (correcto).</td>
  </tr>
</table>


###13. Obtención de elementos desde las tareas (XML)

Esta acción recupera un elemento de la lista de elementos.


**Entradas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Sí</td>
    <td>Id. de elemento del elemento de la lista.</td>
  </tr>
</table>

**Salidas:**

<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>XML de salida</td>
    <td>El mensaje XML que contiene los valores de los campos de elemento de lista seleccionado. </td>
  </tr>
  <tr>
    <td>Estado  </td>
    <td>Si la ejecución de la acción es correcta, se devuelve el código de estado 200 (correcto).</td>
  </tr>
</table>


<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png

<!--HONumber=54-->