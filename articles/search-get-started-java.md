<properties 
	pageTitle="Introducción a Búsqueda de Azure en Java" 
	description="Recorra la creación de una aplicación personalizada de búsqueda de Azure mediante Java como su lenguaje de programación." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="na" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="03/30/2015" 
	ms.author="heidist"/>

#Introducción a Búsqueda de Azure en Java#

Aprenda a crear una aplicación de búsqueda de Java personalizada que utiliza Búsqueda de Azure para la experiencia de búsqueda. El tutorial utiliza la [API de REST del Servicio Búsqueda de Azure](https://msdn.microsoft.com/library/dn798935.aspx) para construir los objetos y las operaciones que se utilizan en este ejercicio.

Hemos usado el siguiente software para compilar y probar este ejemplo:

- [IDE de Eclipse para desarrolladores de Java EE](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Asegúrese de descargar la versión EE. Uno de los pasos de comprobación requiere una característica que sólo se encuentra en esta edición.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

Para ejecutar este ejemplo, debe tener un servicio Búsqueda de Azure, para el que puede iniciar sesión en el [Portal de administración de Azure](https://portal.azure.com).

> [AZURE.TIP]Descargue el código fuente para este tutorial en [demostración de Búsqueda de Azure en Java](http://go.microsoft.com/fwlink/p/?LinkId=530197) en Github.

## Acerca de los datos

Esta aplicación de ejemplo utiliza los datos del [Servicio geológico de Estados Unidos (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), filtrados por el estado de Rhode Island para reducir el tamaño del conjunto de datos. Vamos a usar estos datos para crear una aplicación de búsqueda que devuelva edificios de referencia como hospitales y escuelas, además de características geológicas como ríos, lagos y montes.

En esta aplicación, el programa **SearchServlet.java** compila y carga el índice utilizando un constructo de [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx) y obtiene el conjunto de datos filtrado de USGS desde una base de datos SQL pública de Azure. En el código del programa se proporcionan credenciales predefinidas e información de conexión al origen de datos en línea. En términos de acceso a datos, no es necesario realizar ninguna otra configuración.

> [AZURE.NOTE]Se aplicó un filtro a este conjunto de datos para no sobrepasar el límite de 10.000 documentos del nivel de precios gratuito. Si utiliza el nivel estándar, este límite no se aplica y puede modificar el código para utilizar un conjunto de datos más grande. Para obtener más información acerca de la capacidad de cada nivel de precios, consulte [Límites y restricciones](https://msdn.microsoft.com/library/azure/dn798934.aspx).

## Acerca de los archivos del programa

La lista siguiente describe los archivos que son relevantes para este ejemplo.

- Search.jsp: proporciona la interfaz de usuario
- SearchServlet.java: proporciona métodos (similar a un controlador de MVC)
- SearchServiceClient.java: controla las solicitudes HTTP
- SearchServiceHelper.java: clase auxiliar que proporciona métodos estáticos
- Document.java: proporciona el modelo de datos
- config.properties: establece la dirección URL del servicio de búsqueda y la clave de API
- Pom.xml: una dependencia de Maven


## Creación del servicio

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).

2. En la barra de salto, haga clic en **Nuevo** | **Datos + Almacenamiento** | **Búsqueda**.
 
     ![][1]

3. Configure el nombre del servicio, el nivel de precios, el grupo de recursos, la suscripción y la ubicación. Estos valores son necesarios y no se pueden cambiar una vez que se aprovisiona el servicio.

     ![][2]

	- **Nombre de servicio** debe ser único, en minúsculas, con un máximo de 15 caracteres y sin espacios. Este nombre se convierte en parte del extremo del servicio Búsqueda de Azure. Consulte [Reglas de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) para obtener más información acerca de las convenciones de nomenclatura. 
	
	- **Nivel de precios** determina la capacidad y la facturación. Los dos niveles proporcionan las mismas características, pero con niveles de recursos distintos.
	
		- **Gratuito** se ejecuta en clústeres que se comparten con otros suscriptores. Ofrece suficiente capacidad para probar tutoriales y escribir código de prueba de concepto, pero no está destinado a aplicaciones de producción. Implementar un servicio gratuito suele llevar pocos minutos.
		- **Estándar** se ejecuta en recursos dedicados y es altamente escalable. Inicialmente, se aprovisiona un servicio estándar con una réplica y una partición, pero se puede ajustar la capacidad una vez creado el servicio. Implementar un servicio estándar lleva más tiempo, normalmente unos quince minutos.
	
	- Los **grupos de recursos** son contenedores para servicios y recursos que se usan para un objetivo común. Por ejemplo, si va a compilar una aplicación de búsqueda personalizada basada en Búsqueda de Azure, Sitios web Azure, almacenamiento de blobs de Azure, puede crear un grupo de recursos que mantenga estos servicios juntos en las páginas de administración del portal.
	
	- **Suscripción** permite elegir entre varias suscripciones, si tiene más de una.
	
	- **Ubicación** es la región del centro de datos. Actualmente, todos los recursos se deben ejecutar en el mismo centro de datos. No se admite la distribución de recursos entre varios centros de datos.

4. Haga clic en **Crear** para aprovisionar el servicio.

Preste atención a las notificaciones de la barra de salto. Cuando el servicio esté listo para su uso, aparecerá un aviso.

<a id="sub-2"></a>
## Buscar el nombre del servicio y la clave de API del servicio Búsqueda de Azure

Después de crear el servicio, puede volver al portal para obtener la dirección URL y la `api-key`. Las conexiones con el servicio de búsqueda requieren que tenga la URL y una `api-key` para autenticar la llamada.

1. En la barra de salto, haga clic en **Inicio** y, a continuación, haga clic en el servicio de búsqueda para abrir el panel del servicio. 

2. En el panel del servicio verá mosaicos con información esencial, así como el icono de llave para tener acceso a las claves de administrador.

  	![][3]

3. Copie la dirección URL del servicio y una clave de administración. Las necesitará más adelante para agregarlas al archivo **config.properties**.

## Descarga de los archivos de ejemplo

1. Vaya a [AzureSearchJavaDemo](http://go.microsoft.com/fwlink/p/?LinkId=530197) en Github.

2. Haga clic en **Download ZIP**, guarde el archivo .zip en el disco y extraiga todos los archivos que contiene. Es recomendable que extraiga los archivos en su área de trabajo de Java para que le resulte más fácil encontrar el proyecto más adelante.

3. Los archivos de ejemplo son de solo lectura. Haga clic con el botón secundario en las propiedades de la carpeta y borre el atributo de sólo lectura.

Todas las modificaciones y las instrucciones de ejecución subsiguientes se realizarán en los archivos de esta carpeta.

## Importación del proyecto

1. En Eclipse, elija **File** | **Import** | **General** | **Existing Projects into Workspace** (Archivo | Importar | General | Proyectos existentes al área de trabajo).

    ![][4]

2. En **Select root directory** (Seleccionar directorio raíz), vaya a la carpeta que contiene los archivos de ejemplo. Seleccione la carpeta que contiene la carpeta .project. El proyecto debe aparecer en la lista **Projects** (Proyectos) como elemento seleccionado.

    ![][12]

3. Haga clic en **Finalizar**

4. Utilice **Project Explorer** (Explorador de proyectos) para ver y editar los archivos. Si aún no está abierto, haga clic en **Window** | **Show View** | **Project Explorer** (Ventana | Mostrar vista | Explorador de proyectos) o utilice el método abreviado para abrirlo.

## Configuración de la dirección URL del servicio y la clave de API

1. En **Project Explorer** (Explorador de proyectos), haga doble clic en **config.properties** para editar los valores de configuración que contienen el nombre del servidor y la clave de API. 
 
2. Consulte los pasos descritos anteriormente en este artículo, referentes a la obtención de la dirección URL del servicio y la clave de API en el [Portal de Azure](https://portal.azure.com), para obtener los valores que debe introducir en **config.properties**.

3. En **config.properties**, reemplace "Api Key" con la clave de API del servicio. A continuación, utilice el nombre de servicio (el primer componente de la dirección URL http://servicename.search.windows.net) para reemplazar "service name" en el mismo archivo.

	![][5]

## Configuración de los entornos de proyecto, compilación y tiempo de ejecución

1. En Eclipse, en Project Explorer, haga clic con el botón secundario en el proyecto | **Properties** | **Project Facets** (Propiedades | Facetas del proyecto).

2. Seleccione **Dynamic Web Module**, **Java**, y **JavaScript**.

    ![][6]

3. Haga clic en **Apply**.

4. Seleccione **Window** | **Preferences** | **Server** | **Runtime Environments** | **Add...** (Ventana | Preferencias | Servidor | Entornos de tiempo de ejecución | Agregar...).

5. Expanda Apache y seleccione la versión del servidor Apache Tomcat que ha instalado anteriormente. En nuestro sistema, se instala la versión 8.

	![][7]

6. En la página siguiente, especifique el directorio de instalación de Tomcat. En un equipo Windows, probablemente será C:\\Program Files\\Apache Software Foundation\\Tomcat *versión*.

6. Haga clic en **Finalizar**
 
7. Seleccione **Window** | **Preferences** | **Java** | **Installed JREs** | **Add...** (Ventana | Preferencias | Java | JRE instalados | Agregar...).

8. En **Add JRE** (Agregar JRE), seleccione **Standard VM** (VM estándar).

10. Haga clic en **Siguiente**.
 
11. En JRE Definition (Definición de JRE), en JRE home (Directorio de JRE), haga clic en **Directory** (Directorio).

12. Vaya a **Program Files** | **Java** y seleccione el JDK instalado anteriormente. Es importante seleccionar el JDK como JRE.

13. En Installed JREs, elija el **JDK**. Su configuración debería ser similar a la siguiente captura de pantalla.

    ![][9]

14. Opcionalmente, seleccione **Window** | **Web Browser** | **Internet Explorer** (Ventana | Explorador web | Internet Explorer) para abrir la aplicación en una ventana del explorador externo. Utilizar un explorador externo proporciona una mejor experiencia de aplicación web.

    ![][8]

Ahora ha completado las tareas de configuración. A continuación, podrá compilar y ejecutar el proyecto.

## Compilación del proyecto
 
1. En Project Explorer, haga clic con el botón secundario en el nombre del proyecto y elija **Run As** | **Maven build...** (Ejecutar como | Compilación de Maven...) para configurar el proyecto.

    ![][10]

8. En Edit Configuration (Editar configuración), en la sección Goals (Objetivos), escriba "clean install" y, a continuación, haga clic en **Run** (Ejecutar).
 
Los mensajes de estado se envían a la ventana de consola. Debería ver BUILD SUCCESS, lo que indica que el proyecto se ha compilado sin errores.

## Ejecución de la aplicación

En este último paso, ejecutará la aplicación en un entorno de tiempo de ejecución del servidor local.

Si todavía no ha especificado un entorno de tiempo de ejecución del servidor en Eclipse, deberá hacerlo en primer lugar.

1. En Project Explorer, expanda **WebContent** (Contenido web).

5. Haga clic con el botón secundario en **Search.jsp** | **Run As** | **Run on Server** (Search.jsp | Ejecutar como | Ejecutar en el servidor). Seleccione el servidor Apache Tomcat y, a continuación, haga clic en **Run** (Ejecutar).

> [AZURE.TIP]Si utiliza un área de trabajo distinta de la predeterminada para almacenar el proyecto, probablemente necesite modificar **Run Configuration** (Configuración de ejecución) para que apunte a la ubicación del proyecto y evitar un error de inicio del servidor. En Project Explorer, haga clic en **Search.jsp** | **Run As** | **Run Configurations** (Search.jsp | Ejecutar como | Configuraciones de ejecución). Seleccione el servidor Apache Tomcat. Haga clic en **Arguments** (Argumentos). Haga clic en **Workspace** (Área de trabajo) o en **File System** (Sistema de archivos) para definir la carpeta que contiene el proyecto.

Al ejecutar la aplicación, aparecerá una ventana del explorador que ofrece un cuadro de búsqueda para introducir términos.

Aguarde un minuto antes de hacer clic en **Search** (Buscar) para que el servicio tenga tiempo de crear y cargar el índice. Si obtiene un error HTTP 404, sólo necesitará esperar un poco más antes de intentarlo.

## Buscar en los datos de USGS

El conjunto de datos de USGS incluye los registros que son relevantes para el estado de Rhode Island. Si hace clic en **Search** en un cuadro de búsqueda vacío, obtendrá las 50 primeras entradas (es el valor predeterminado).

Escriba un término de búsqueda para que el motor de búsqueda tenga con qué trabajar. Pruebe a escribir un nombre regional. "Roger Williams" fue el primer gobernador de Rhode Island. Hay numerosos parques, edificios y escuelas que llevan su nombre.

![][11]

También puede probar con alguno de estos términos:

- Pawtucket
- Pembroke
- goose +cape

## Pasos siguientes

Este es el primer tutorial de Búsqueda de Azure basada en Java y en el conjunto de datos de USGS. Con el tiempo, ampliaremos este tutorial para mostrar otras características de búsqueda que podría querer usar en sus soluciones personalizadas.

Si ya Tiene alguna experiencia con Búsqueda de Azure, puede utilizar este ejemplo como punto de partida para experimentación adicional, por ejemplo, aumentando la [página de búsqueda](../search-pagination/), o implementando la [navegación por facetas](../search-faceted-navigation/). También puede mejorar la página de resultados de búsqueda si agrega recuentos y procesamiento por lotes de documentos para que los usuarios puedan navegar por las páginas de resultados.

¿Es la primera vez que usa Búsqueda de Azure? Le recomendamos que pruebe otros tutoriales para comprender mejor lo que puede crear. Visite nuestra [página de documentación](http://azure.microsoft.com/documentation/services/search/) para encontrar más recursos. También puede ver los vínculos en nuestra [lista de vídeos y tutoriales](https://msdn.microsoft.com/library/azure/dn798933.aspx) para tener acceso a más información.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-11.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png

<!--HONumber=54-->