# Uso de las herramientas de línea de comandos de Azure para Mac y Linux

En esta guía se describe cómo usar las herramientas de línea de comandos de Azure para Mac y Linux a fin de crear y administrar servicios en Azure. Entre las tareas que se incluyen está la **instalación de herramientas**, la **importación de su configuración de publicación**, la **creación y administración de los Sitios web Azure** y la **creación y administración de las Máquinas virtuales de Azure**. Para obtener una documentación de referencia completa, consulte [Documentación de las herramientas de línea de comandos de Azure para Mac y Linux][Documentación de las herramientas de línea de comandos de Azure para Mac y Linux].

## Tabla de contenido

-   [Qué son las herramientas de línea de comandos de Azure para Mac y Linux][Qué son las herramientas de línea de comandos de Azure para Mac y Linux]
-   [Instalación de las herramientas de línea de comandos de Azure para Mac y Linux][Instalación de las herramientas de línea de comandos de Azure para Mac y Linux]
-   [Creación de una cuenta de Azure][Creación de una cuenta de Azure]
-   [Descarga e importación de la configuración de publicación][Descarga e importación de la configuración de publicación]
-   [Creación y administración de un Sitio web Azure][Creación y administración de un Sitio web Azure]
-   [Creación y administración de una Máquina virtual de Azure][Creación y administración de una Máquina virtual de Azure]

## <span id="Overview"></span></a>Qué son las herramientas de línea de comandos de Azure para Mac y Linux

Las herramientas de línea de comandos de Azure para Mac y Linux son un conjunto de herramientas de línea de comandos que sirven para implementar y administrar los servicios de Azure.

Las tareas admitidas incluyen las siguientes:

-   Importar configuración de publicación.
-   Crear y administrar los Sitios web Azure.
-   Crear y administrar las Máquinas virtuales de Azure.

Para obtener una lista completa de los comandos admitidos, escriba `azure -help` en la línea de comandos después de instalar las herramientas, o consulte la [documentación de referencia][Documentación de las herramientas de línea de comandos de Azure para Mac y Linux].

## <span id="Download"></span>Instalación de las herramientas de línea de comandos de Azure para Mac y Linux</a>

La siguiente lista contiene información para instalar las herramientas de línea de comandos, según su sistema operativo:

-   **Mac**: Descargue el [Instalador de SDK de Azure][Instalador de SDK de Azure]. Abra el archivo .pkg descargado y complete los pasos de instalación a medida que se le solicita.

-   **Linux**: Instale la última versión de [Node.js][Node.js] (consulte [Install Node.js via Package Manager][Install Node.js via Package Manager]) y, a continuación, ejecute el siguiente comando:

        npm install azure-cli -g

    **Nota**: es posible que necesite ejecutar este comando con privilegios elevados:

        sudo npm install azure-cli -g

-   **Windows**: Ejecute el instalador de Windows (archivo .msi), que está disponible aquí: [Herramientas de línea de comandos de Azure][Herramientas de línea de comandos de Azure].

Para probar la instalación, escriba `azure` en el símbolo del sistema. Si la instalación fue correcta, verá una lista de los comandos `azure` disponibles.

## <span id="CreateAccount"></span></a>Creación de una cuenta de Azure

Para usar las Herramientas de línea de comandos de Azure para Mac y Linux, necesitará una cuenta de Azure.

Abra un explorador web, diríjase a [http://azure.microsoft.com/es-es/](http://azure.microsoft.com/es-es/) y haga clic en **free trial** en la esquina superior derecha.

![Sitio web de Azure][Sitio web de Azure]

Siga las instrucciones para crear una cuenta.

## <span id="Account"></span></a>Descarga e importación de la configuración de publicación

Para comenzar, primero necesita descargar e importar su configuración de publicación. Esto le permitirá usar las herramientas para crear y administrar los Servicios de Azure. Para descargar la configuración de publicación, utilice el comando `account download`:

    azure account download

Esto abrirá su explorador predeterminado y le solicitará iniciar sesión en el Portal de administración. Después de iniciar sesión, se descargará su archivo `.publishsettings`. Tome nota de dónde se guarda este archivo.

A continuación, importe el archivo `.publishsettings` mediante la ejecución del siguiente comando y reemplace `{path to .publishsettings file}` por la ruta hacia el archivo `.publishsettings`:

    azure account import {path to .publishsettings file}

Puede eliminar toda la información almacenada con el comando `import` usando el comando `account clear`:

    azure account clear

Para ver una lista de opciones para los comandos `account`, use la opción `-help`:

    azure account -help

Después de importar su configuración de publicación, debe eliminar el archivo `.publishsettings` por motivos de seguridad.

<div class="dev-callout"> 
<b>Nota:</b> 
<p>Al importar la configuraci&oacute;n de publicaci&oacute;n, las credenciales para tener acceso a su suscripci&oacute;n de Azure est&aacute;n almacenadas dentro de la carpeta <code data-inline="1">user</code>. La carpeta <code data-inline="1">user</code> est&aacute; protegida por el sistema operativo. Sin embargo, se recomienda que tome medidas adicionales para cifrar la carpeta <code data-inline="1">user</code>. Puede hacerlo de las siguientes maneras:</p>

<ul>
<li>En Windows, modifique las propiedades de la carpeta o use BitLocker.</li>
<li>En Mac, active FileVault para la carpeta.</li>
<li>En Ubuntu, use la caracter&iacute;stica del directorio de inicio (home) cifrado. Otras distribuciones de Linux ofrecen caracter&iacute;sticas equivalentes.</li>
</ul>

</div>

Ahora está listo para empezar a crear y administrar los Sitios web Azure y las Máquinas virtuales de Azure.

## <span id="WebSites"></span></a>Creación y administración de un Sitio web Azure

### Creación de un Sitio web

Para crear un Sitio web Azure, cree primero un directorio vacío con el nombre `MySite` y vaya hasta ese directorio.

Luego, ejecute el siguiente comando:

    azure site create MySite --git

La salida de este comando contendrá la dirección URL predeterminada para el sitio web recién creado. La opción `--git` le permite usar git para publicar en su sitio web al crear repositorios de git tanto en su directorio de aplicaciones local como en el centro de datos de su sitio web. Tenga en cuenta que si su carpeta local ya está en un repositorio de git, el comando agregará un remoto nuevo al repositorio actual, el cual apuntará al repositorio del centro de datos de su sitio web.

Observe que puede ejecutar el comando `azure site create` con cualquiera de las siguientes opciones:

-   `--location [location name]`. Esta opción le permite especificar la ubicación del centro de datos en el que se crea su sitio web (por ejemplo, "Oeste de EE. UU"). Si omite esta opción, se le solicitará que elija una ubicación.
-   `--hostname [custom host name]`. Esta opción le permite especificar un nombre de host personalizado para su sitio web.

Posteriormente, puede agregar contenido al directorio de su sitio web. Use el flujo de git regular (`git add`, `git commit`) para confirmar su contenido. Use el siguiente comando de git para insertar el contenido de su sitio web en Azure:

    git push azure master

### Configuración de la publicación desde GitHub

Para configurar una publicación continua desde un repositorio de GitHub, use la opción `--GitHub` cuando cree un sitio:

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Si tiene un clon local de un repositorio de GitHub o si tiene un repositorio con una sola referencia remota a un repositorio de GitHub, este comando publicará código automáticamente en el repositorio de GitHub de su sitio. Desde ese momento, todo cambio que se inserte en el repositorio de GitHub se publicará automáticamente en su sitio.

Cuando configure la publicación desde GitHub, se usará de manera predeterminada la rama principal. Para especificar una rama diferente, ejecute el siguiente comando desde su repositorio local:

    azure site repository <branch name>

### Configuración de aplicaciones

La configuración de aplicaciones son pares clave-valor que están disponibles para su aplicación en el tiempo de ejecución. Cuando se establece para un Sitio web Azure, los valores de la configuración de aplicaciones reemplazan la configuración por la misma clave que se define en el archivo Web.config de su sitio. Para las aplicaciones Node.js y PHP, la configuración de aplicaciones está disponible como variables de entorno. El siguiente ejemplo muestra cómo establecer un par clave-valor:

    azure site config add <key>=<value> 

Para ver una lista de todos los pares clave-valor, use lo siguiente:

    azure site config list 

O bien, si conoce la clave y desea ver el valor, puede usar:

    azure site config get <key> 

Si desea cambiar el valor de una clave actual, primero debe borrar la clave actual y luego volver a agregarla. El comando para borrar es:

    azure site config clear <key> 

### Enumeración de sitios

Para enumerar los sitios web, utilice el comando siguiente:

    azure site list

Para obtener más información sobre un sitio, use el comando `site show`. El siguiente ejemplo muestra detalles para `MySite`:

    azure site show MySite

### Detención, inicio o reinicio de un sitio

Puede detener, iniciar o reiniciar un sitio con los comandos `site stop`, `site start` o `site restart`:

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

### Eliminación de un sitio

Finalmente, puede eliminar un sitio con el comando `site delete`:

    azure site delete MySite

Tenga en cuenta que si va a ejecutar cualquiera de los comandos desde el interior de la carpeta donde ejecutó `site create`, no es necesario que especifique el nombre del sitio `MySite` como el último parámetro.

Para ver una lista de opciones para los comandos `site`, use la opción `-help`:

    azure site -help 

## <span id="VMs"></span></a>Creación y administración de una Máquina virtual de Azure

Se crea una Máquina virtual de Azure a partir de una imagen de máquina virtual (un archivo .vhd) que usted proporciona o que está disponible en la Galería de imágenes. Para ver las imágenes que están disponibles, utilice el comando `vm image list`:

    azure vm image list

Puede aprovisionar e iniciar una máquina virtual desde una de las imágenes disponibles con el comando `vm create`. El ejemplo siguiente muestra cómo crear una máquina virtual Linux (de nombre `myVM`) desde una imagen en la Galería de imágenes (CentOS 6.2). El nombre de usuario raíz y la contraseña para la máquina virtual son `myusername` y `Mypassw0rd` respectivamente. (Tenga en cuenta que el parámetro `--location` especifica el centro de datos en el que se crea la máquina virtual. Si omite el parámetro `--location`, se le solicitará que elija una ubicación).

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-es-es-30GB.vhd myusername --location "West US"

Puede considerar pasar la marca `--ssh` (Linux) o la marca `--rdp` (Windows) a `vm create` para habilitar conexiones remotas para la máquina virtual recién creada.

Si prefiere aprovisionar una máquina virtual desde una imagen personalizada, puede crear una imagen desde un archivo .vhd con el comando `vm image create` y, a continuación, usar el comando `vm create` para aprovisionar la máquina virtual. El siguiente ejemplo muestra cómo crear una imagen de Linux (de nombre `myImage`) desde un archivo .vhd local. (El parámetro `--location` especifica los datos donde se almacena la imagen).

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

En vez de crear una imagen desde un archivo .vhd local, puede crear una imagen desde un archivo .vhd almacenado en el Almacenamiento de blobs de Azure. Puede hacer esto con el parámetro `blob-url`:

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

Después de crear una imagen, puede aprovisionar una máquina virtual desde la imagen usando `vm create`. El comando a continuación crea una máquina virtual de nombre `myVM` desde la imagen que se creó anteriormente (`myImage`).

    azure vm create myVM myImage myusername --location "West US"

Después de que haya aprovisionado una máquina virtual, puede que necesite crear extremos para permitir el acceso remoto a su máquina virtual (por ejemplo). El ejemplo siguiente usa el comando `vm create endpoint` para abrir el puerto externo 22 y el puerto local 22 en `myVM`:

    azure vm endpoint create myVM 22 22

Puede obtener información detallada sobre una máquina virtual (incluida la dirección IP, el nombre de DNS y la información del extremo) con el comando `vm show`:

    azure vm show myVM

Para apagar, iniciar o reiniciar la máquina virtual, use uno de los siguientes comandos:

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

Y finalmente, para eliminar la máquina virtual, utilice el comando `vm delete`:

    azure vm delete myVM

Para conocer una lista completa de comandos para crear y administrar máquinas virtuales, use la opción `-h`:

    azure vm -h

<!-- IMAGES --> 
<!-- LINKS -->

  [Documentación de las herramientas de línea de comandos de Azure para Mac y Linux]: http://go.microsoft.com/fwlink/?LinkId=252246
  [Qué son las herramientas de línea de comandos de Azure para Mac y Linux]: #Overview
  [Instalación de las herramientas de línea de comandos de Azure para Mac y Linux]: #Download
  [Creación de una cuenta de Azure]: #CreateAccount
  [Descarga e importación de la configuración de publicación]: #Account
  [Creación y administración de un Sitio web Azure]: #WebSites
  [Creación y administración de una Máquina virtual de Azure]: #VMs
  [Instalador de SDK de Azure]: http://go.microsoft.com/fwlink/?LinkId=252249
  [Node.js]: http://nodejs.org/
  [Install Node.js via Package Manager]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
  [Herramientas de línea de comandos de Azure]: http://go.microsoft.com/fwlink/?LinkID=275464
  [Sitio web de Azure]: ./media/crossplat-cmd-tools/freetrial.png
