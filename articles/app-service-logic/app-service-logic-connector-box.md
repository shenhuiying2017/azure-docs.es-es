<properties
   pageTitle="Uso del conector de Box en la aplicación lógica"
   description="Uso del conector de Box en la aplicación lógica"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/02/2015"
   ms.author="andalmia"/>

# Uso del conector de Box en la aplicación lógica



Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. Puede tener escenarios donde sea necesario trabajar con Box, que le permite compartir datos de forma segura con cualquier persona, incluso si están ubicados fuera del firewall.



La aplicación de la galería Box proporciona acciones en forma de mecanismos para la interacción con Box:



1. **Acciones**: las acciones permiten realizar acciones predefinidas en la cuenta de Box configurada con la aplicación lógica. A continuación se muestran las acciones que se pueden realizar en la cuenta de Box mediante el conector de Box:

	a. *Enumerar archivos:* esta operación devolverá la información de todos los archivos en una carpeta. A continuación se muestra una lista de los parámetros necesarios para la acción:

	<table>
  <tr>
    <td><b>Nombre de parámetro</b></td>
    <td><b>Descripción</b></td>
    <td><b>Obligatorio</b></td>
  </tr>
  <tr>
    <td>Ruta de acceso a la carpeta</td>
    <td>La ruta de acceso a la carpeta en la que se va a realizar la enumeración.</td>
    <td>Sí</td>
  </tr>
</table>>[AZURE.NOTE]No devuelve ningún contenido del archivo.



    b. *Obtener archivo:* esta operación recupera un archivo que incluye su contenido y propiedades. A continuación se muestra una lista de los parámetros necesarios para la acción:

	<table>
  <tr>
    <td><b>Nombre de parámetro</b></td>
    <td><b>Descripción</b></td>
    <td><b>Obligatorio</b></td>
  </tr>
  <tr>
    <td>Ruta de acceso de archivo</td>
    <td>La ruta de acceso a la carpeta en la que se encuentra el archivo.</td>
    <td>Sí</td>
  </tr>
  <tr>
    <td>Tipo de archivo</td>
    <td>Especifica si el archivo es texto o binario.</td>
    <td>No</td>
  </tr>
</table>>[AZURE.NOTE]Esta operación no eliminará el archivo después de su lectura.



    c. Cargar archivo: como sugiere su nombre, esta acción carga el archivo en la cuenta de Box. Si ya existe el archivo, no se sobrescribe y se produce un error. A continuación se muestra una lista de los parámetros necesarios para la acción:

	<table>
  <tr>
    <td><b>Nombre de parámetro</b></td>
    <td><b>Descripción</b></td>
    <td><b>Obligatorio</b></td>
  </tr>
  <tr>
    <td>Ruta de acceso de archivo</td>
    <td>La ruta de acceso al archivo.</td>
    <td>Sí</td>
  </tr>
  <tr>
    <td>Contenido del archivo</td>
    <td>El contenido del archivo que se va a cargar.</td>
    <td>Sí</td>
  </tr>
  <tr>
    <td>Codificación de transferencia de contenido</td>
    <td>El tipo de codificación del contenido, que puede ser Base64 o ninguna.</td>
    <td> </td>
  </tr>
</table>d. Eliminar archivo: la acción elimina el archivo especificado de una carpeta. Se produce una excepción si la carpeta o el archivo no se encuentra. A continuación se muestra una lista de los parámetros necesarios para la acción:

 	<table>
  <tr>
    <td><b>Nombre de parámetro</b></td>
    <td><b>Descripción</b></td>
    <td><b>Obligatorio</b></td>
  </tr>
  <tr>
    <td>Ruta de acceso de archivo</td>
    <td>Ruta de acceso de archivo completa, incluidas las carpetas.</td>
    <td>Sí</td>
  </tr>
</table>




## Creación de un conector de Box para la aplicación lógica

Para usar el conector de Box, deberá crear primero una instancia de la aplicación de API del conector de Box. Puede hacerlo dentro del diseñador de aplicaciones lógicas o fuera de él. Para crear fuera del diseñador, proceda como sigue:

1. Abra Azure Marketplace desde la página principal del Portal de Azure.

2. En "Todo", busque "Conector de Box".

3. Configure el conector de Box y haga clic en Crear:

	![][1]

4. Cuando haya terminado, puede crear una aplicación lógica en el mismo grupo de recursos para que utilice el conector de Box.


## Uso del conector de Box en la aplicación lógica

Una vez creada la aplicación de API, puede usar el conector de Box como acción para la aplicación lógica. Para ello, necesita lo siguiente:


1. Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de Box.

2. Abra "Desencadenadores y acciones" para abrir el diseñador de aplicaciones lógicas y configure el flujo. El conector de Box aparecerá en la sección "Usadas recientemente" de la galería, en el lado derecho. Selecciónelo.

3. Si se selecciona el conector de Box al inicio de la aplicación lógica, actuará como desencadenador o las acciones se tomarán en la cuenta de cuadro mediante el conector. Tenga en cuenta que el conector de Box no tiene ningún desencadenador en el momento en que se redacta este artículo.

4. El primer paso sería autenticar y autorizar las aplicaciones lógicas para realizar operaciones en su nombre. Para iniciar la autorización, haga clic en Autorizar en el conector de Box.

	![][2]

5. Si hace clic en Autorizar se abre el cuadro de diálogo de autenticación de Box. Proporcione los detalles de inicio de sesión de la cuenta de Box en la que desea realizar las operaciones.

	![][3]

6. Conceda acceso a su cuenta a las aplicaciones lógicas para llevar a cabo la operación en su nombre.

	![][4]

7. Se muestra la lista de acciones y puede elegir la operación apropiada que desea realizar.

	![][5]


<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg

<!---HONumber=July15_HO3-->