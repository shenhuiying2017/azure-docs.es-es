<properties linkid="develop-java-tutorials-jenkins-continuous-integration" urlDisplayName="Jenkins Continuous Integration" pageTitle="Using Azure Storage with a Jenkins Continuous Integration Solution | Microsoft Azure" metaKeywords="" description="This tutorial show how to use the Azure blob service as a repository for build artifacts created by a Jenkins continuous integration solution." metaCanonical="" services="storage" documentationCenter="Java" title="Using Azure Storage with a Jenkins Continuous Integration solution" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

Uso del almacenamiento de Azure con una solución de integración continua Jenkins
================================================================================

*Por [Microsoft Open Technologies Inc.](http://msopentech.com)*

La siguiente información describe cómo usar el servicio BLOB de Azure como repositorio de artefactos de compilación creados por una solución de integración continua Jenkins. Uno de los escenarios en que esto le puede resultar útil es para codificar en un entorno de desarrollo ágil (con el uso de Java o de otros lenguajes), cuando las compilaciones se ejecutan según una integración continua y si necesita un repositorio para los artefactos de compilación, a fin de que, por ejemplo, pueda compartirlos con otros miembros de la organización, con los clientes o para mantener un archivo.

En este tutorial va a usar el complemento de almacenamiento de Azure para la integración continua Jenkins habilitada por Microsoft Open Technologies, Inc.

Tabla de contenido
------------------

-   [Información general sobre Jenkins](#overview)
-   [Ventajas de usar el servicio BLOB](#benefits)
-   [Requisitos previos](#prerequisites)
-   [Uso del servicio BLOB con la integración continua Jenkins](#howtouse)
-   [Instalación del complemento de almacenamiento de Azure](#howtoinstall)
-   [Configuración del complemento de almacenamiento de Azure para usar la cuenta de almacenamiento](#howtoconfigure)
-   [Creación de una acción posterior a la compilación que carga los artefactos de compilación para la cuenta de almacenamiento](#howtocreatepostbuild)
-   [Componentes usados por el servicio BLOB](#components)

Información generalInformación general sobre Jenkins
----------------------------------------------------

Jenkins permite la integración continua de un proyecto de software al hacer posible que los desarrolladores integren fácilmente los cambios de su código y cuenten con compilaciones generadas de manera automática y frecuente, lo que les permite aumentar la productividad. Las compilaciones tienen varias versiones y los artefactos de compilación se pueden cargar en varios repositorios. En este tema se describe cómo usar el almacenamiento de blobs de Azure como repositorio de los artefactos de compilación.

Puede encontrar más información acerca de Jenkins en [Meet Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

VentajasVentajas de usar el servicio BLOB
-----------------------------------------

Las ventajas de usar el servicio BLOB para hospedar los artefactos de compilación para el desarrollo ágil son:

-   Alta disponibilidad de los artefactos de compilación
-   Rendimiento si la solución de integración continua Jenkins carga los artefactos de compilación
-   Rendimiento si los clientes y socios descargan los artefactos de compilación
-   Control sobre las directivas de acceso de usuarios, con una opción entre acceso anónimo, acceso compartido basado en expiración, acceso mediante firma, acceso privado, etc.

Requisitos previosRequisitos previos
------------------------------------

Para usar el servicio BLOB con la solución de integración continua Jenkins, necesitará lo siguiente:

-   Una solución de integración continua Jenkins.

    Si no dispone actualmente de una solución de integración continua Jenkins, puede ejecutarla mediante la siguiente técnica:

    1.  En una máquina habilitada para Java, descargue jenkins.war de &lt;http://jenkins-ci.org\&gt;.
    2.  En un símbolo del sistema que se abre en la carpeta que contiene jenkins.war, ejecute:

        `java -jar jenkins.war`

    3.  En el explorador, abra `http://localhost:8080/`. A continuación, se abrirá el panel de Jenkins, que usará para instalar y configurar el complemento de almacenamiento de Azure.

        Aunque una solución de integración continua Jenkins típica se configuraría para ejecutarse como un servicio, la ejecución del archivo war de Jenkins en la línea de comandos será suficiente para este tutorial.

-   Una cuenta de Azure. Puede iniciar sesión en una cuenta de Azure en &lt;http://www.windowsazure.com\&gt;.

-   Una cuenta de almacenamiento de Azure. Si aún no tiene una cuenta de almacenamiento, puede crearla con los pasos descritos en [Creación de una cuenta de almacenamiento](http://go.microsoft.com/fwlink/?LinkId=279823).

-   Es recomendable estar familiarizado con la solución de integración continua Jenkins, pero no obligatorio, ya que a continuación se ilustrará un ejemplo básico con los pasos que debe seguir para usar el servicio BLOB como repositorio para los artefactos de compilación de integración continua Jenkins.

Uso del servicio BLOBUso del servicio BLOB con la integración continua Jenkins
------------------------------------------------------------------------------

Para usar el servicio BLOB con Jenkins, necesitará instalar el complemento de almacenamiento de Azure, configurarlo para usar la cuenta de almacenamiento y, a continuación, crear una acción posterior a la compilación que cargue los artefactos de compilación en la cuenta de almacenamiento. Estos pasos se describen en las secciones siguientes.

InstalaciónInstalación del complemento de almacenamiento de Azure
-----------------------------------------------------------------

1.  En el panel de Jenkins, haga clic en **Manage Jenkins**.
2.  En la página **Manage Jenkins**, haga clic en **Manage Plugins**.
3.  Haga clic en la pestaña **Available**.
4.  En la sección **Artifact Uploaders**, seleccione **Azure Storage plugin**.
5.  Haga clic en **Install without restart** o **Download now and install after restart**.
6.  Reinicie Jenkins.

ConfiguraciónConfiguración del complemento de almacenamiento de Azure para usar la cuenta de almacenamiento
-----------------------------------------------------------------------------------------------------------

1.  En el panel de Jenkins, haga clic en **Manage Jenkins**.
2.  En la página **Manage Jenkins**, haga clic en **Configure System**.
3.  En la sección **Azure Storage Account Configuration**:
    1.  Escriba el nombre de la cuenta de almacenamiento, que puede obtener en el portal de Azure, en &lt;https://manage.windowsazure.com\&gt;.
    2.  Escriba la clave de la cuenta de almacenamiento, que también puede obtener en el portal de Azure.
    3.  Use el valor predeterminado para **Blob Service Endpoint URL** si utiliza la nube pública de Azure. En cambio, si usa una nube de Azure distinta, utilice el extremo tal y como se especifica en el portal de administración de Azure para la cuenta de almacenamiento.
    4.  Haga clic en **Validate Storage Credentials** para validar la cuenta de almacenamiento.
    5.  [Opcional] Si tiene más cuentas de almacenamiento que desea que estén disponibles para la integración continua Jenkins, haga clic en **Add more Storage Accounts**.
    6.  Haga clic en **Save** para guardar la configuración.

Creación de una acción posterior a la compilaciónCreación de una acción posterior a la compilación que carga los artefactos de generación para la cuenta de almacenamiento
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Con el fin de facilitar instrucciones, primero necesitaremos crear un trabajo que generará varios archivos y, a continuación, agregar la acción posterior a la compilación para cargar estos archivos en la cuenta de almacenamiento.

1.  En el panel de Jenkins, haga clic en **New Job**.
2.  Asigne al trabajo el nombre **MyJob** y, a continuación, haga clic en **Build a free-style software project** y en **OK**.
3.  En la sección **Build** de la configuración del trabajo, haga clic en **Add build step** y seleccione **Execute Windows batch command**.
4.  En **Command**, use los comandos siguientes:

         md text
         cd text
         echo Hello Azure Storage from Jenkins > hello.txt
         date /t > date.txt
         time /t >> date.txt

5.  En la sección **Post-build Actions** de la configuración del trabajo, haga clic en **Add post-build action** y seleccione **Upload artifacts to Azure Blob storage**.
6.  En **Storage Account Name**, seleccione la cuenta de almacenamiento que desea usar.
7.  En **Container Name**, especifique el nombre del contenedor. (El contenedor se creará si aún no existe cuando se carguen los artefactos de compilación). Puede usar las variables de entorno, así que, para este ejemplo, escriba **\${JOB\_NAME}** como nombre del contenedor.

    **Sugerencia**

    Debajo de la sección **Command** en la que ha insertado un script para **Execute Windows batch command** hay un vínculo a las variables de entorno reconocidas por Jenkins. Haga clic en dicho vínculos para obtener acceso a las descripciones y los nombres de las variables de entorno. Tenga en cuenta que las variables de entorno que contienen caracteres especiales, como **BUILD\_URL**, no se admiten como nombre de contenedor ni como ruta de acceso virtual común.

8.  Haga clic en **Make container public** para este ejemplo. (Si desea usar un contenedor privado, también tendrá que crear una firma de acceso compartida para permitir el acceso. Eso va más allá del ámbito de este tema. Puede obtener más información acerca de las firmas de acceso compartido en [Crear y usar una firma de acceso compartido](http://go.microsoft.com/fwlink/?LinkId=279889)).
9.  En **List of Artifacts to upload**, escriba **text/\*.txt**.
10. En **Common virtual path for uploaded artifacts**, escriba **\${BUILD\_ID}/\${BUILD\_NUMBER}**.
11. Haga clic en **Save** para guardar la configuración.
12. En el panel de Jenkins, haga clic en **Build Now** para ejecutar **MyJob**. Examine el resultado de la consola para consultar el estado. Los mensajes de estado sobre el almacenamiento de Azure se incluirán en los resultados de la consola cuando la acción posterior a la compilación comience a cargar los artefactos de compilación.
13. Tras completar el trabajo satisfactoriamente, abra el blob público si desea examinar los artefactos de compilación.
    1.  Inicie sesión en el portal de administración de Azure, en &lt;https://manage.windowsazure.com\&gt;.
    2.  Haga clic en **Almacenamiento**.
    3.  Haga clic en el nombre de la cuenta de almacenamiento que ha usado para Jenkins.
    4.  Haga clic en **Containers**.
    5.  Haga clic en el contenedor llamado **myjob**, que es la versión en minúscula del nombre del trabajo asignado cuando ha creado el trabajo de Jenkins. Los nombres de los contenedores y los nombres de los blobs se guardan en minúscula en el almacenamiento de Azure y, además, distinguen mayúsculas de minúsculas. En la lista de blobs del contenedor llamado **myjob**, deben aparecer **hello.txt** y **date.txt**. Copie la URL de cualquiera de estos elementos y ábrala en el explorador. A continuación, verá el archivo de texto cargado como un artefacto de compilación.

Componentes del servicio BLOBComponentes usados por el servicio BLOB
--------------------------------------------------------------------

A continuación se ofrece información general acerca de los componentes del servicio BLOB.

-   **Cuenta de almacenamiento**: Todo el acceso al almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Se trata del nivel superior del espacio de nombres para el acceso a los blobs. Una cuenta puede contener una cantidad ilimitada de contenedores, siempre que su tamaño total no supere los 100 TB.
-   **Contenedor**: Un contenedor ofrece una agrupación de un conjunto de blobs. Todos los blobs deben residir en un contenedor. Además, una cuenta puede disponer de un número ilimitado de contenedores y un contenedor puede almacenar un número ilimitado de blobs.
-   **Blob**: Un archivo de cualquier tipo y tamaño. Existen dos tipos de blobs que pueden almacenarse en Almacenamiento de Azure: blobs en páginas y en bloques. La mayoría de los archivos son blobs en bloques. Un blob en bloques único puede tener un tamaño de hasta 200 GB. En este tutorial se usan blobs en bloques. Los blobs en páginas, que son otro tipo de blobs, pueden tener un tamaño de hasta 1 TB y son más eficaces cuando los intervalos de bytes de un archivo se modifican con frecuencia. Para obtener más información sobre los blobs, consulte [Understanding Block Blobs and Page Blobs](http://msdn.microsoft.com/es-es/library/windowsazure/ee691964.aspx).
-   **Formato de dirección URL**: Es posible dirigir los blobs con el siguiente formato de dirección URL:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    (El formato anterior se aplica a la nube pública de Azure. En cambio, si usa una nube de Azure distinta, use el extremo en el portal de administración de Azure para determinar el extremo de la URL).

    En el formato anterior, `storageaccount` representa el nombre de la cuenta de almacenamiento, `container_name` representa el nombre del contenedor y `blob_name` representa el nombre del blob, respectivamente. En el nombre del contenedor, puede tener varias rutas de acceso, separadas por una barra diagonal, **/**. El nombre de contenedor utilizado como ejemplo para este tutorial es **MyJob** y **\${BUILD\_ID}/\${BUILD\_NUMBER}** se ha usado para la ruta de acceso virtual común; como resultado, la URL del blob presenta el siguiente formato:

    `http://example.blob.core.windows.net/myjob/2013-01-28_15-00-35/2/hello.txt`


