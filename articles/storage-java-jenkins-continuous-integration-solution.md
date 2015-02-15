<properties 
	urlDisplayName="Jenkins Continuous Integration" 
	pageTitle="Uso del Almacenamiento de Azure con una solución de integración continua Jenkins | Microsoft Azure" 
	metaKeywords="" 
	description="En este tutorial se muestra cómo usar el servicio BLOB de Azure como repositorio para artefactos de compilación creados por una solución de integración continua Jenkins." 
	metaCanonical="" 
	services="storage" 
	documentationCenter="java" 
	title="" 
	authors="rmcmurray" 
	solutions="" 
	manager="wpickett" 
	editor="mollybos" 
	scriptId="" 
	videoId=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

#Uso del Almacenamiento de Azure con una solución de integración continua Jenkins

*Por [Microsoft Open Technologies Inc..][ms-open-tech]*

La siguiente información describe cómo usar el servicio BLOB de Azure como repositorio de artefactos de compilación creados por una solución de integración continua Jenkins, o como origen de archivos descargables para su uso en un proceso de compilación. Uno de los escenarios en que esto le puede resultar útil es para codificar en un entorno de desarrollo ágil (con el uso de Java o de otros lenguajes), cuando las compilaciones se ejecutan según una integración continua y si necesita un repositorio para los artefactos de compilación, a fin de que, por ejemplo, pueda compartirlos con otros miembros de la organización, con los clientes o para mantener un archivo. Otro es cuando su trabajo de compilación propiamente dicho requiere otros archivos, por ejemplo, la descarga de dependencias como parte de la entrada de compilación.

En este tutorial va a usar el complemento de almacenamiento de Azure para la integración continua Jenkins habilitada por Microsoft Open Technologies, Inc.

## Tabla de contenido

-   [Información general sobre Jenkins][]
-   [Ventajas de usar el servicio BLOB][]
-   [Requisitos previos][]
-   [Uso del servicio BLOB con la integración continua Jenkins][]
-   [Instalación del complemento de almacenamiento de Azure][]
-   [Configuración del complemento de almacenamiento de Azure para usar la cuenta de almacenamiento][]
-   [Creación de una acción posterior a la compilación que carga los artefactos de compilación para la cuenta de almacenamiento][]
-   [Creación de un paso de compilación que descarga del almacenamiento de blobs de Azure][]
-   [Componentes usados por el servicio BLOB][]

## <a name="overview"></a>Información general sobre Jenkins ##

Jenkins permite la integración continua de un proyecto de software al hacer posible que los desarrolladores integren fácilmente los cambios de su código y cuenten con compilaciones generadas de manera automática y frecuente, lo que les permite aumentar la productividad. Las compilaciones tienen varias versiones y los artefactos de compilación se pueden cargar en varios repositorios. En este tema se describe cómo usar el almacenamiento de blobs de Azure como repositorio de los artefactos de compilación. También se mostrará cómo descargar dependencias del almacenamiento de blobs de Azure.

Puede encontrar más información acerca de Jenkins en [Presentación de Jenkins][].

## <a name="benefits"></a>Ventajas de usar el servicio BLOB ##

Las ventajas de usar el servicio BLOB para hospedar los artefactos de compilación para el desarrollo ágil son:

- Alta disponibilidad de sus artefactos de compilación o de sus dependencias descargables.
- Rendimiento si la solución de integración continua Jenkins carga los artefactos de compilación.
- Rendimiento si los clientes y socios descargan los artefactos de compilación.
- Control sobre las directivas de acceso de usuarios, con una opción entre acceso anónimo, acceso compartido basado en expiración, acceso mediante firma, acceso privado, etc.

## <a name="prerequisites"></a>Requisitos previos ##

Para usar el servicio BLOB con la solución de integración continua Jenkins, necesitará lo siguiente:

- Una solución de integración continua Jenkins.

    Si no dispone actualmente de una solución de integración continua Jenkins, puede ejecutarla mediante la siguiente técnica:

    1. En una máquina con Java habilitado, descargue jenkins.war de <http://jenkins-ci.org>.
    2. En un símbolo del sistema que se abre en la carpeta que contiene jenkins.war, ejecute:

        `java -jar jenkins.war`

    3. En el explorador, abra `http://localhost:8080/`. A continuación, se abrirá el panel de Jenkins, que usará para instalar y configurar el complemento de almacenamiento de Azure.

        Aunque una solución de integración continua Jenkins típica se configuraría para ejecutarse como un servicio, la ejecución del archivo war de Jenkins en la línea de comandos será suficiente para este tutorial.

- Una cuenta de Azure. Puede registrarse para crear una cuenta de Azure en <http://www.windowsazure.com>.

- Una cuenta de almacenamiento de Azure. Si aún no tiene una cuenta de almacenamiento, puede crearla con los pasos descritos en [Creación de una cuenta de almacenamiento][].

- Es recomendable estar familiarizado con la solución de integración continua Jenkins, pero no obligatorio, ya que a continuación se ilustrará un ejemplo básico con los pasos que debe seguir para usar el servicio BLOB como repositorio para los artefactos de compilación de integración continua Jenkins.

## <a name="howtouseUso del servicio BLOB con la integración continua Jenkins"></a> ##

Para usar el servicio BLOB con Jenkins, necesitará instalar el complemento de almacenamiento de Azure, configurarlo para usar la cuenta de almacenamiento y, a continuación, crear una acción posterior a la compilación que cargue los artefactos de compilación en la cuenta de almacenamiento. Estos pasos se describen en las secciones siguientes.

## <a name="howtoinstallInstalación del complemento de almacenamiento de Azure"></a> ##

1. En el panel de Jenkins, haga clic en **Manage Jenkins** (Administrar Jenkins).
2. En la página **Manage Jenkins** (Administrar Jenkins), haga clic en **Manage Plugins** (Administrar complementos).
3. Haga clic en la pestaña **Available** (Disponible).
4. En la sección **Artifact Uploaders** (Cargadores de artefactos), marque **Microsoft Azure Storage plugin** (Complemento de almacenamiento de Microsoft Azure).
5. Haga clic en **Install without restart** (Instalar sin reiniciar) o **Download now and install after restart** (Descargar ahora e instalar después de reiniciar).
6. Reinicie Jenkins.

## <a name="howtoconfigureConfiguración del complemento de almacenamiento de Azure para usar la cuenta de almacenamiento"></a> ##

1. En el panel de Jenkins, haga clic en **Manage Jenkins** (Administrar Jenkins).
2. En la página **Manage Jenkins** (Administrar Jenkins), haga clic en **Configure System** (Configurar sistema).
3. En la sección **Microsoft Azure Storage Account Configuration** (Configuración de la cuenta de almacenamiento de Microsoft Azure):
    1. Escriba el nombre de la cuenta de almacenamiento, que puede obtener en el portal de Azure, en <https://manage.windowsazure.com>.
    2. Escriba la clave de la cuenta de almacenamiento, que también puede obtener en el portal de Azure.
    3. Use el valor predeterminado para **Blob Service Endpoint URL** (Dirección URL del extremo del servicio BLOB) si usa la nube pública de Azure. En cambio, si usa una nube de Azure distinta, utilice el extremo tal y como se especifica en el portal de administración de Azure para la cuenta de almacenamiento. 
    4. Haga clic en **Validate storage credentials** (Validar credenciales de almacenamiento) para validar la cuenta de almacenamiento. 
    5. [Opcional] Si tiene más cuentas de almacenamiento que desea que estén disponibles para la integración continua Jenkins, haga clic en **Add more Storage Accounts** (Agregar más cuentas de almacenamiento).
    6. Haga clic en **Save** (Guardar) para guardar la configuración.

## <a name="howtocreatepostbuildCreación de una acción posterior a la compilación que carga los artefactos de compilación para la cuenta de almacenamiento"></a> ##

Con el fin de facilitar instrucciones, primero necesitaremos crear un trabajo que generará varios archivos y, a continuación, agregar la acción posterior a la compilación para cargar estos archivos en la cuenta de almacenamiento.

1. En el panel de Jenkins, haga clic en **New Item** (Nuevo elemento).
2. Asigne al trabajo el nombre **MyJob**, haga clic en **Build a free-style software project** (Compilar un trabajo de software de estilo libre) y, a continuación, haga clic en **OK** (Aceptar).
3. En la sección **Build** (Compilar) de la configuración del trabajo, haga clic en **Add build step** (Agregar paso de compilación) y seleccione **Execute Windows batch command** (Ejecutar comando por lotes de Windows).
4. En **Command** (Comando), use los comandos siguientes:

        md text
        cd text
        echo Hello Azure Storage from Jenkins > hello.txt
        date /t > date.txt
        time /t >> date.txt
 
5. En la sección **Post-build Actions** (Acciones posteriores a la compilación) de la configuración del trabajo, haga clic en **Add post-build action** (Agregar acción posterior a la compilación) y seleccione **Upload artifacts to Azure Blob storage** (Cargar artefactos en el almacenamiento de blobs de Microsoft Azure).
6. En **Storage account name** (Nombre de cuenta de almacenamiento), seleccione la cuenta de almacenamiento que desea usar.
7. En **Container Name** (Nombre de contenedor), especifique el nombre del contenedor. (El contenedor se creará si aún no existe cuando se carguen los artefactos de compilación). Puede usar las variables de entorno, así que, para este ejemplo, escriba **${JOB_NAME}** como nombre del contenedor.

    **Sugerencia**
    
    Debajo de la sección **Command** (Comando) en la que ha insertado un script para **Execute Windows batch command** (Ejecutar comando por lotes de Windows) hay un vínculo a las variables de entorno reconocidas por Jenkins. Haga clic en dicho vínculos para obtener acceso a las descripciones y los nombres de las variables de entorno. Tenga en cuenta que las variables de entorno que contienen caracteres especiales, como **BUILD_URL**, no se admiten como nombre de contenedor ni como ruta de acceso virtual común.

8. Haga clic en **Make new container public by default** (Hacer público el nuevo contenedor de forma predeterminada) para este ejemplo. (Si desea usar un contenedor privado, también tendrá que crear una firma de acceso compartida para permitir el acceso. Eso va más allá del ámbito de este tema. Puede obtener más información acerca de las firmas de acceso compartido en [Creación de una firma de acceso compartido](http://go.microsoft.com/fwlink/?LinkId=279889).)
9. [Opcional] Haga clic en **Clean container before uploading** (Limpiar contenedor antes de cargarlo) si desea que se borre el contenido del contenedor antes de cargar los artefactos de compilación (déjela sin marcar si no desea limpiar el contenido del contenedor).
10. En **List of Artifacts to upload** (Lista de artefactos para cargar), escriba **text/*.txt**.
11. A los efectos del presente tutorial, en **Common virtual path for uploaded artifacts** (Ruta de acceso virtual común para artefactos cargados), escriba **${BUILD\_ID}/${BUILD\_NUMBER}**.
12. Haga clic en **Save** (Guardar) para guardar la configuración.
13. En el panel de Jenkins, haga clic en **Build Now** (Compilar ahora) para ejecutar **MyJob**. Examine el resultado de la consola para consultar el estado. Los mensajes de estado sobre el almacenamiento de Azure se incluirán en los resultados de la consola cuando la acción posterior a la compilación comience a cargar los artefactos de compilación.
14. Tras completar el trabajo satisfactoriamente, abra el blob público si desea examinar los artefactos de compilación.
    1. Inicie sesión en el Portal de administración de Azure <https://manage.windowsazure.com>.
    2. Haga clic en **Storage** (Almacenamiento).
    3. Haga clic en el nombre de la cuenta de almacenamiento que ha usado para Jenkins.
    4. Haga clic en **Contenedores**.
    5. Haga clic en el contenedor llamado **myjob**, que es la versión en minúscula del nombre del trabajo asignado cuando ha creado el trabajo de Jenkins. Los nombres de los contenedores y los nombres de los blobs se guardan en minúscula en el almacenamiento de Azure y, además, distinguen mayúsculas de minúsculas. En la lista de blobs del contenedor llamado **myjob**, deben aparecer **hello.txt** y **date.txt**. Copie la URL de cualquiera de estos elementos y ábrala en el explorador. A continuación, verá el archivo de texto cargado como un artefacto de compilación.

Solo se puede crear una acción posterior a la compilación que cargue artefactos en el almacenamiento de blobs de Azure por cada trabajo. Tenga en cuenta que la acción posterior a la compilación para cargar artefactos en el almacenamiento de blobs de Azure puede especificar diferentes archivos (comodines incluidos) y rutas de acceso a los archivos en **List of Artifacts to upload** (Lista de artefactos para cargar) con el uso del punto y coma como separador. Por ejemplo, si su proceso de compilación Jenkins produce archivos JAR y TXT en la carpeta **build** del área de trabajo, y desea cargar ambos en el almacenamiento de blobs de Azure, use el siguiente código para el valor **List of Artifacts to upload** (Lista de artefactos para cargar): **build/\*.jar;build/\*.txt**. También puede usar sintaxis de dos puntos dobles para especificar una ruta de acceso dentro del nombre del blob. Por ejemplo, si desea que los archivos JAR se carguen mediante **archivos binarios** en la ruta de acceso del blob y que los TXT lo hagan por medio de **notificaciones** en esta misma ruta, use lo siguiente para el valor **List of Artifacts to upload** (Lista de artefactos para cargar): **build/\*.jar::binaries;build/\*.txt::notices**.

## <a name="howtocreatebuildstepCreación de un paso de compilación que descarga del almacenamiento de blobs de Azure"></a> ##

En los siguientes pasos se muestra cómo configurar un paso de compilación para descargar elementos del almacenamiento de blobs de Azure. Esto sería útil si desea incluir elementos en la compilación, por ejemplo, archivos JAR que mantenga en el almacenamiento de blobs de Azure.

1. En la sección **Build** (Compilar) de la configuración del trabajo, haga clic en **Add build step** (Agregar paso de compilación) y seleccione **Download from Azure Blob storage** (Descargar del almacenamiento de blobs de Azure).
2. En **Storage account name** (Nombre de cuenta de almacenamiento), seleccione la cuenta de almacenamiento que desea usar.
3. En **Container name** (Nombre de contenedor), especifique el nombre del contenedor que tiene los blobs que desea descargar. Puede usar variables de entorno.
4. En **Blob name** (Nombre de blob), especifique el nombre del blob. Puede usar variables de entorno. Además, puede usar un asterisco como comodín después de especificar las letras iniciales del nombre del blob. Por ejemplo, **project\*** especificaría todos los blobs cuyos nombres comiencen por **project**.
5. [Opcional] En **Download path** (Ruta de acceso de la descarga), especifique la ruta de acceso de la máquina de Jenkins en la que desea descargar los archivos del almacenamiento de blobs de Azure. También se pueden usar variables de entorno. (Si no proporciona un valor para **Download path**, los archivos del almacenamiento de blobs de Azure se descargarán en el área de trabajo del trabajo).

Si tiene elementos adicionales que desea descargar del almacenamiento de blobs de Azure, puede crear pasos de compilación adicionales.

Después de ejecutar una compilación, puede comprobar la salida de la consola del historial de compilación, o examinar la ubicación de descarga, para ver si los blobs que esperaba se han descargado correctamente.  

## <a name="components"></a>Componentes usados por el servicio BLOB ##

A continuación se ofrece información general acerca de los componentes del servicio BLOB.

- **Cuenta de almacenamiento**: todo el acceso al almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Se trata del nivel superior del espacio de nombres para el acceso a los blobs. Una cuenta puede contener una cantidad ilimitada de contenedores, siempre que su tamaño total no supere los 100 TB.
- **Contenedor**: un contenedor ofrece una agrupación de un conjunto de blobs. Todos los blobs deben residir en un contenedor. Además, una cuenta puede disponer de un número ilimitado de contenedores y un contenedor puede almacenar un número ilimitado de blobs.
- **Blob**: un archivo de cualquier tipo y tamaño. Existen dos tipos de blobs que pueden almacenarse en Almacenamiento de Azure: blobs en páginas y en bloques. La mayoría de los archivos son blobs en bloques. Un blob en bloques único puede tener un tamaño de hasta 200 GB. En este tutorial se usan blobs en bloques. Los blobs en páginas, que son otro tipo de blobs, pueden tener un tamaño de hasta 1 TB y son más eficaces cuando los intervalos de bytes de un archivo se modifican con frecuencia. Para obtener más información sobre los blobs, consulte [Introducción a los blobs en bloques y a los blobs en páginas](http://msdn.microsoft.com/es-es/library/windowsazure/ee691964.aspx).
- **Formato de URL**: es posible dirigir los blobs con el siguiente formato de dirección URL:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
    
    (El formato anterior se aplica a la nube pública de Azure. En cambio, si usa una nube de Azure distinta, use el extremo en el portal de administración de Azure para determinar el extremo de la URL).

    En el formato anterior,  `storageaccount` representa el nombre de la cuenta de almacenamiento,  `container_name` representa el nombre del contenedor y  `blob_name` representa el nombre del blob, respectivamente. En el nombre del contenedor, puede tener varias rutas de acceso, separadas por una barra diagonal,**/**. El nombre de contenedor utilizado como ejemplo para este tutorial es **MyJob** y **${BUILD\_ID}/${BUILD\_NUMBER}** se ha usado para la ruta de acceso virtual común; como resultado, la URL del blob presenta el siguiente formato:

    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

  [Información general sobre Jenkins]: #overview
  [Ventajas de usar el servicio BLOB]: #benefits
  [Requisitos previos]: #prerequisites
  [Uso del servicio BLOB con la integración continua Jenkins]: #howtouse
  [Instalación del complemento de almacenamiento de Azure]: #howtoinstall
  [Configuración del complemento de almacenamiento de Azure para usar la cuenta de almacenamiento]: #howtoconfigure
  [Creación de una acción posterior a la compilación que carga los artefactos de compilación para la cuenta de almacenamiento]: #howtocreatepostbuild
  [Creación de un paso de compilación que descarga del almacenamiento de blobs de Azure]: #howtocreatebuildstep
  [Componentes usados por el servicio BLOB]: #components
  [Creación de una cuenta de almacenamiento]: http://go.microsoft.com/fwlink/?LinkId=279823
  [Presentación de Jenkins]: https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins
  [ms-open-tech]: http://msopentech.com

<!--HONumber=42-->
