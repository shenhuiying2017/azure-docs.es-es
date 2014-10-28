<properties linkid="script-xplat-intro" urlDisplayName="Azure Cross-Platform Command-Line Interface" pageTitle="The Azure Cross-Platform Command-Line Interface" title="The Azure Cross-Platform Command-Line Interface" metaKeywords="Azure cross-platform command-line interface, Azure command-line, azure command-line, azure cli" description="Install and configure the Azure Cross-Platform Command-Line Interface to manage Azure Services" metaCanonical="http://www.windowsazure.com/es-es/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" documentationCenter="" solutions="" authors="carolz" services="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="carolz"></tags>

# Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/manage/install-and-configure-windows-powershell/" title="PowerShell">PowerShell</a><a href="/es-es/manage/install-and-configure-cli/" title="CLI entre plataformas" class="current">CLI entre plataformas</a></div>

La interfaz de la línea de comandos entre plataformas de Azure (xplat-cli) proporciona un conjunto de comandos de código abierto de varias plataformas para trabajar con la plataforma Azure. La xplat-cli proporciona muchas de las mismas funciones del Portal de administración de Azure, como la posibilidad de administrar sitios web, máquinas virtuales y servicios móviles, Base de datos SQL y otros servicios ofrecidos por la plataforma Azure.

La xplat-cli se escribe en JavaScript y precisa Node.js; se implementa usando el SDK de Azure para Node.js y se lanza con la licencia de Apache 2.0. El repositorio de proyectos se encuentra en [][]<https://github.com/WindowsAzure/azure-sdk-tools-xplat></a>.

Este documento indica cómo instalar y configurar la interfaz de la línea de comandos entre plataformas de Azure, además de cómo usarla para realizar tareas básicas con la plataforma Azure.

## Este documento incluye los siguientes temas:

-   [Instalación de la interfaz de la línea de comandos entre plataformas de Azure][]
-   [Conexión con su suscripción de Azure][]
-   [Uso de la interfaz de la línea de comandos entre plataformas de Azure][]
-   [Script de la interfaz de la línea de comandos entre plataformas de Azure][]
-   [Recursos adicionales][]

## <span id="install"></span></a>Instalación de la interfaz de la línea de comandos entre plataformas de Azure

Hay dos formas de instalar la xplat-cli: usando paquetes del instalador para Windows y OS X; o bien, si tiene instalado Node.js en su sistema, el comando **npm**.

En el caso de los sistemas Linux, debe tener instalado Node.js y usar **npm** para instalar la xplat-cli como se indica a continuación, o bien crearla desde el origen. El origen está disponible en [][1]<http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409></a>. Para obtener más información acerca de la creación desde el origen, consulte el archivo INSTALL que se incluye en el archivo.

Una vez instalada la xplat-cli, podrá usar el comando **azure** desde su interfaz de la línea de comandos (Bash, Terminal, símbolo del sistema) para acceder a sus comandos.

### Uso de un instalador

Están disponibles los siguientes paquetes del instalador:

-   [Windows Installer][]

-   [Instalador de OS X][]

### Uso de npm

Si tiene instalado Node.js en su sistema, utilice el comando siguiente para instalar la xplat-cli:

    npm install azure-cli -g

> [WACOM.NOTE] Puede que tenga que usar `sudo` para ejecutar correctamente el comando **npm**.

De este modo, se instalará la xplat-cli y las dependencias necesarias. Al final de la instalación, debería ver algo parecido a esto:

    azure-cli@0.8.0 ..\node_modules\azure-cli
    |-- easy-table@0.0.1
    |-- eyes@0.1.8
    |-- xmlbuilder@0.4.2
    |-- colors@0.6.1
    |-- node-uuid@1.2.0
    |-- async@0.2.7
    |-- underscore@1.4.4
    |-- tunnel@0.0.2
    |-- omelette@0.1.0
    |-- github@0.1.6
    |-- commander@1.0.4 (keypress@0.1.0)
    |-- xml2js@0.1.14 (sax@0.5.4)
    |-- streamline@0.4.5
    |-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
    |-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
    |-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

> [WACOM.NOTE] Node.js se puede instalar desde <http://nodejs.org/>.

## <span id="Configure"></span></a>Conexión con su suscripción de Azure

Aunque algunos comandos proporcionados por la xplat-cli funcionan sin una suscripción de Azure, la mayoría de ellos sí la requieren. Para configurar la xplat-cli a fin de que funcione con su suscripción, puede hacer lo siguiente:

-   Descargar y usar un archivo de configuración de publicación.

O bien

-   Iniciar sesión en Azure usando una cuenta profesional. Al iniciar sesión, se usa Azure Active Directory para autenticar las credenciales.

Para elegir el método de autenticación más adecuado a sus necesidades, tenga en cuenta lo siguiente:

-   El método de inicio de sesión puede facilitar la administración del acceso a la suscripción, aunque puede interrumpir la automatización, ya que las credenciales podrían agotar el tiempo de espera y requerir que vuelva a iniciar sesión.

    > [WACOM.NOTE] El método de inicio de sesión solo funciona con la cuenta profesional. Una cuenta profesional es un usuario administrado por su organización y definido en su inquilino de Azure Active Directory de la organización. Si actualmente no tiene una cuenta profesional y usa una cuenta Microsoft para iniciar sesión en su suscripción de Azure, puede crear una fácilmente siguiendo los pasos que se indican a continuación.
    >
    > 1.  Inicie sesión en el [Portal de administración de Azure][] y haga clic en **Active Directory**.
    >
    > 2.  Si no hay ningún directorio, seleccione **Create your directory** y proporcione la información que se le pida.
    >
    > 3.  Seleccione su directorio y agregue un nuevo usuario. Este nuevo usuario es una cuenta profesional.
    >
    >     Durante la creación del usuario, se le proporcionará una dirección de correo electrónico para el usuario y una contraseña temporal. Guarde esta información porque la usará en otro paso.
    >
    > 4.  En el portal de administración, seleccione **Settings** y, a continuación, **Administrators**. Seleccione **Add** y agregue el nuevo usuario como coadministrador. Así permite a la cuenta profesional administrar su suscripción de Azure.
    >
    > 5.  Finalmente, cierre sesión en el portal de Azure y, a continuación, vuelva a iniciarla usando la nueva cuenta profesional. Si es la primera vez que inicia sesión con esta cuenta, se le pedirá que cambie la contraseña.
    >
    > Para obtener más información acerca de la cuenta profesional con Microsoft Azure, consulte [Inicio de sesión como organización en Microsoft Azure][].

-   El método del archivo de configuración de publicación instala un certificado que le permite realizar tareas de administración durante el periodo de validez de la suscripción y el certificado. Este método facilita el uso de la automatización para las tareas de ejecución prolongada. Una vez que descarga e importa la información, no tiene que volver a facilitarla. Sin embargo, este método dificulta la administración del acceso a una suscripción, ya que cualquier persona con acceso al certificado podría administrar la suscripción.

Para obtener más información acerca de la administración de la autenticación y la suscripción, consulte [¿Cuál es la diferencia entre la autenticación basada en cuentas y la basada en certificados?][].

En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][].

### Uso del método de inicio de sesión

Para iniciar sesión usando una cuenta profesional, utilice el comando siguiente:

    azure login [username] [password]

> [WACOM.NOTE] Si es la primera vez que inicia sesión con estas credenciales, se le pedirá que verifique que desea copiar en caché un token de autenticación. Este aviso también aparecerá si ha usado anteriormente el comando `azure logout` que se indica a continuación. Para omitir este aviso en escenarios de automatización, utilice el parámetro `-q` con el comando `azure login`.
>
> Cuando se autentique con una cuenta profesional, la información de acceso a su suscripción de Azure se almacena en un directorio `.azure` ubicado en el directorio `user`. El sistema operativo protege su directorio `user`; no obstante, es recomendable que tome medidas adicionales para cifrar su directorio `user`. Puede hacerlo de las siguientes maneras:

Para cerrar sesión, utilice el comando siguiente:

    azure logout [username]

> [WACOM.NOTE] Si las suscripciones asociadas a la cuenta solo se autenticaron con Active Directory, al cerrar sesión se eliminará la información de la suscripción del perfil local. Sin embargo, si el archivo de configuración de publicación también se ha importado para las suscripciones, al cerrar sesión se eliminará solo la información relacionada con Active Directory del perfil local.

> [WACOM.NOTE] Los comandos siguientes no funcionarán correctamente al autenticarse usando una cuenta:
>
> -   `azure vm`
> -   `azure network`
> -   `azure mobile`
>
> Si tiene que trabajar con estos comandos, use un archivo de configuración de publicación para autenticarse en Azure como se indica en la sección siguiente.

### Uso del método del archivo de configuración de publicación

Para descargar la configuración de publicación de su cuenta, use el comando siguiente:

    azure account download

Esto abrirá su explorador predeterminado y le solicitará que inicie sesión en el Portal de administración de Azure. Después de iniciar sesión, se descargará un archivo `.publishsettings`. Tome nota de dónde se guarda este archivo.

> [WACOM.NOTE] Si su cuenta está asociada a varios inquilinos de Azure Active Directory, puede que se le pida que seleccione para qué Active Directory desea descargar el archivo de configuración de publicación.
>
> Una vez seleccionado usando la página de descarga, o visitando el Portal de administración de Azure, el Active Directory seleccionado se convierte en el predeterminado que usan tanto el portal como la página de descarga. Cuando se haya establecido el predeterminado, verá el texto "**click here to return to the selection page**" en la parte superior de la página de descarga. Utilice el vínculo proporcionado para volver a la página de selección.

A continuación, importe el archivo `.publishsettings` mediante la ejecución del siguiente comando y reemplace `[path to .publishsettings file]` por la ruta hacia el archivo `.publishsettings`:

    azure account import [path to .publishsettings file]

> [WACOM.NOTE] Al importar la configuración de publicación, la información de acceso a su suscripción de Azure se almacena en un directorio `.azure` ubicado en su directorio `user`. El sistema operativo protege su directorio `user`; no obstante, es recomendable que tome medidas adicionales para cifrar su directorio `user`. Puede hacerlo de las siguientes maneras:
>
> -   En Windows, modifique las propiedades del directorio o use BitLocker.
> -   En Mac, active FileVault para el directorio.
> -   En Ubuntu, use la característica del directorio de inicio (home) cifrado. Otras distribuciones de Linux ofrecen características equivalentes.

Después de importar su configuración de publicación, debe eliminar el archivo `.publishsettings`, porque ya no se necesitan las herramientas de línea de comandos y suponen un riesgo para la seguridad, ya que se puede usar para obtener acceso a su suscripción.

### Varias suscripciones

Si tiene varias suscripciones de Azure, iniciar sesión le dará acceso a todas las suscripciones asociadas a sus credenciales. Si utiliza un archivo de configuración de publicación, el archivo `.publishsettings` contendrá la información de todas las suscripciones. Con cualquiera de los métodos, se seleccionará una suscripción como predeterminada para su uso por parte de xplat-cli al realizar operaciones. Podrá ver las suscripciones, así como cuál es la predeterminada, pero no podrá usar el comando `azure account list`. Este comando devolverá información similar a la siguiente:

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
    data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

En la lista de arriba, la columna **Current** indica las suscripciones predeterminadas actuales como Azure-sub-1. Para cambiar la suscripción predeterminada, utilice el comando `azure account set` y especifique qué suscripción quiere que sea la predeterminada. Por ejemplo:

    azure account set Azure-sub-2

Esto cambiará la suscripción predeterminada a Azure-sub-2.

> [WACOM.NOTE] El cambio de la suscripción predeterminada surte efecto de forma inmediata y es global; la nueva xplat-commands, ya se ejecute desde la misma instancia de línea de comandos o desde una diferente, usará la nueva suscripción predeterminada.

Si desea usar una suscripción no predeterminada con la xplat-cli, pero no quiere cambiar la que es predeterminada actualmente, puede usar la opción `--subscription` y proporcionar el nombre de la suscripción que desee usar para la operación.

## <span id="use"></span></a>Uso de la interfaz de la línea de comandos entre plataformas de Azure

Se puede acceder a la xplat-cli usando el comando `azure`. Para ver una lista de los comandos disponibles, use el comando `azure` sin parámetros. Debería ver una información de ayuda similar a la siguiente:

    info:             _    _____   _ ___ ___
    info:            /_\  |_  / | | | _ \ __|
    info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
    info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
    info:       (_______ _ _)         _ ______ _)_ _
    info:              (______________ _ )   (___ _ _)
    info:
    info:    Windows Azure: Microsoft's Cloud Platform
    info:
    info:    Tool version 0.8.0
    help:
    help:    Display help for a given command
    help:      help [options] [command]
    help:
    help:    Opens the portal in a browser
    help:      portal [options]
    help:
    help:    Commands:
    help:      account        Commands to manage your account information and publish settings
    help:      config         Commands to manage your local settings
    help:      hdinsight      Commands to manage your HDInsight accounts
    help:      mobile         Commands to manage your Mobile Services
    help:      network        Commands to manage your Networks
    help:      sb             Commands to manage your Service Bus configuration
    help:      service        Commands to manage your Cloud Services
    help:      site           Commands to manage your Web Sites
    help:      sql            Commands to manage your SQL Server accounts
    help:      storage        Commands to manage your Storage objects
    help:      vm             Commands to manage your Virtual Machines
    help:
    help:    Options:
    help:      -h, --help     output usage information
    help:      -v, --version  output the application version

Los anteriores comandos de nivel máximo contienen comandos para trabajar con un área específica de Azure. Por ejemplo, el comando `azure account` contiene comandos relacionados con su suscripción de Azure, como las configuraciones `download` e `import` usadas anteriormente.

La mayoría de los comandos tienen el formato `azure <command> <operation> [parameters]` y realizan una operación en un servicio u objeto como su configuración de cuenta. Otros comandos proporcionan subcomandos y siguen el formato `azure <command> <subcommand> <operation> [parameters]`. Los ejemplos siguientes son comandos que funcionan con su configuración de cuenta:

-   Para ver las suscripciones que ha importado, utilice el siguiente:

        azure account list

-   Si ha importado suscripciones, utilice el siguiente para establecer uno como predeterminado:

        azure account set <subscription>

Los parámetros `--help` o `-h` se pueden usar para ver la ayuda de comandos específicos. También puede usar el formato `azure help [command] [options]` para devolver la misma información. Por ejemplo, todos los comandos siguientes devuelven la misma información:

    azure account set --help

    azure account set -h

    azure help account set

Si duda de qué parámetros necesita un comando, consulte la ayuda usando `--help`, `-h` o `azure help [command]`.

### Establecimiento del modo de configuración

La xplat-cli le permite realizar operaciones de administración en *recursos* concretos, que son entidades administradas por el usuario, por ejemplo un servidor de bases de datos, una base de datos o un sitio web. Este es el modo predeterminado de operación para la xplat-cli y se conoce como **Azure Service Management**. No obstante, cuando tiene una solución compleja que consta de varios recursos, resulta útil poder administrar toda la solución como una sola unidad.

Para poder administrar un grupo de recursos como una sola unidad lógica, o *grupo de recursos*, hemos introducido una vista previa de **Resource Manager** como una nueva forma de administrar los recursos de Azure.

> [WACOM.NOTE] Resource Manager está actualmente en vista previa y no proporciona el mismo nivel de capacidades de administración que Azure Service Management.

Para obtener compatibilidad con el nuevo Resource Manager, la xplat-cli le permite alternar entre estos "modos" de administración usando el comando `azure config mode`.

La xplat-cli propone de forma predeterminada el modo Azure Service Management. Para pasar al modo Resource Manager, utilice el siguiente comando:

    azure config mode arm

Para volver al modo Azure Service Management, utilice el siguiente comando:

    azure config mode asm 

> [WACOM.NOTE] Los modos Resource Manager y Azure Service Management se excluyen mutuamente. Es decir, los recursos creados en un modo no se pueden administrar desde el otro.

Para obtener más información acerca de cómo trabajar con Resource Manager usando la xplat-cli, consulte [Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager][].

### Trabajo con los servicios en el modo de administración de servicios de Azure

La xplat-cli le permite administrar fácilmente los servicios de Azure. En este ejemplo, aprenderá a usar la xplat-cli para administrar un sitio web de Azure.

1.  Utilice el comando siguiente para crear un sitio web de Azure. Reemplace **mywebsite** por un nombre exclusivo.

        azure site create mywebsite

    Se le pedirá que especifique la región en la que se creará el sitio web. A continuación, seleccione una región que esté geográficamente próxima a la suya. Cuando este comando se complete, el sitio web estará disponible en <http://mywebsite.azurewebsites.net> (reemplace **mywebsite** por el nombre que ha especificado).

    > [WACOM.NOTE] Si usa Git para controlar el origen del proyecto, puede especificar el parámetro `--git` para crear un repositorio Git en Azure para este sitio web. Así, también se inicializa un repositorio Git en el directorio desde el cual se ejecutó el comando, si todavía no hay ninguno. También se creará un Git remoto llamado **azure**, que se puede usar para insertar implementaciones en el sitio web de Azure usando el comando `git push azure master`.

    > [WACOM.NOTE] Si recibe un error que indica que 'site' no es un comando de azure, es muy probable que la xplat-cli esté en modo de grupo de recursos. Para volver a cambiar al modo de recursos, use el comando `azure config mode asm`.

2.  Utilice el comando siguiente para enumerar los sitios web para su suscripción:

        azure site list

    La lista debería contener el sitio creado en el paso anterior.

3.  Utilice el comando siguiente para detener el sitio web: Reemplace **mywebsite** por el nombre del sitio.

        azure site stop mywebsite

    Cuando el comando se complete, podrá actualizar el explorador para verificar que el sitio se ha detenido.

4.  Utilice el comando siguiente para iniciar el sitio web: Reemplace **mywebsite** por el nombre del sitio.

        azure site start mywebsite

    Cuando el comando se complete, podrá actualizar el explorador para verificar que el sitio se ha iniciado.

5.  Utilice el comando siguiente para eliminar el sitio web. Reemplace **mywebsite** por el nombre del sitio.

        azure site delete mywebsite

    Cuando el comando se complete, utilice el comando `azure site list` para verificar que el sitio web ya no existe.

## <span id="script"></span></a>Script de la interfaz de la línea de comandos entre plataformas de Azure

Aunque puede usar la xplat-cli para emitir comandos manualmente, también puede crear flujos de trabajo de automatización complejos sacando provecho de las capacidades de su intérprete de línea de comandos y otras utilidades de línea de comandos disponibles en su sistema. Por ejemplo, el comando siguiente detendrá todos los sitios web de Azure que se estén ejecutando:

    azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

Este ejemplo proporciona una lista de sitios web para el comando `grep`, que inspecciona cada línea en busca de la cadena 'Running'. Todas las líneas que coincidan se canalizan al comando `awk`, que llama azure site `azure site stop` y usa la segunda columna que pasa a él (el nombre del sitio en ejecución) como el nombre del sitio que hay que detener.

Aunque esto demuestra cómo se pueden encadenar unos comandos a otros, también puede crear scripts más elaborados usando las funciones de scripting proporcionadas por su intérprete de línea de comandos. Los diferentes intérpretes de línea de comandos tienen distintas características y sintaxis de scripting. Bash es probablemente el intérprete de línea de comandos más usado para los sistemas basados en UNIX, incluyendo Linux y OS X.

Para obtener información acerca del scripting con Bash, consulte [Advanced Bash-Scripting Guide][].

Para obtener información más general acerca del scripting de sistemas basados en OS X o Linux, consulte [Shell script][].

Para obtener información acerca del scripting de los sistemas basados en Windows usando archivos por lotes, consulte [Command-line reference A-Z][].

### Interpretación de los resultados

Al crear scripts, a menudo tendrá que capturar el resultado de un comando y pasarlo a otro, o realizar una operación en el resultado, como la comprobación de un valor concreto. La xplat-cli genera resultados para STDOUT y STDERR. Cada línea lleva como prefijo las cadenas `info:` en el caso de mensajes de información o `data:` en el caso de los datos devueltos sobre un servicio; no obstante, puede ordenar a la xplat-cli que devuelva información más detallada usando los parámetros `--verbose` o `-v`. El prefijo de la cadena `verbose:`: devolverá información adicional.

Por ejemplo, el resultado siguiente se devuelve desde el comando `azure site list`:

    info:    Executing command site list
    + Enumerating sites
    data:    Name           Status   Mode  Host names
    data:    -------------  -------  ----  -------------------------------
    data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
    info:    site list command OK

Si se han especificado los parámetros `--verbose` o `-v`, se devolverá información similar a la siguiente:

    info:    Executing command site list
    verbose: Subscription ####################################
    verbose: Enumerating sites
    verbose: [
    verbose:     {
    verbose:         ComputeMode: 'Shared',
    verbose:         HostNameSslStates: {
    verbose:             HostNameSslState: [
    verbose:                 {
    verbose:                     IpBasedSslResult: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     SslState: 'Disabled',
    verbose:                     ToUpdateIpBasedSsl: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     VirtualIP: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     Thumbprint: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     ToUpdate: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     Name: 'myawesomesite.azurewebsites.net'
    verbose:                 },
    ...
    verbose:     }
    verbose: ]
    data:    Name           Status   Mode  Host names
    data:    -------------  -------  ----  -------------------------------
    data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
    info:    site list command OK

Observe que la información de `verbose:` parece ser datos en formato JSON. Puede usar el parámetro `--json` para devolver la información en formato JSON si trabaja con las utilidades que entienden de forma nativa JSON, como [jsawk][] o [jq][]. Por ejemplo:

    azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q 

El comando anterior recupera una lista de sitios web como JSON, a continuación, usa jsawk para recuperar los nombres de los sitios y, finalmente, usa xargs para ejecutar un comando de eliminación en cada sitio, pasando el nombre del sitio como parámetro.

> [WACOM.NOTE] El parámetro `--json` bloquea la generación de estados o la información de datos (cadenas con prefijos `info:` y `data:`). Por ejemplo, si se usa el parámetro `--json` con `azure site create` site create, no se devuelven resultados porque este comando no devuelve datos diferentes de `info:`:.

### Trabajo con errores

Aunque la xplat-cli registra la información de los errores en STDERR, la información adicional acerca de los errores también podría quedar registrada en un archivo **azure.err** del directorio desde el que se ejecutó el script. Si la información está registrada en este archivo, se escribirá lo siguiente en STDOUT:

    info:    Error information has been recorded to azure.err

### Estado de salida

Algunos de los comandos de xplat-cli no devuelven un estado de salida distinto de cero en caso de que falten los parámetros obligatorios. En lugar de ello, le pedirán al usuario que introduzca la información. Por ejemplo, al usar el comando `azure site create` para crear un sitio web, si no se especifica un nombre de sitio o parámetro `--location`, se le pedirá que especifique estos valores.

Si crea un script basado en el estado de salida, verifique que los comandos de xplat-cli que use no pidan al usuario que introduzca información.

## <span id="additional-resources"></span></a>Recursos adicionales

-   Para obtener más información acerca de la xplat-cli, para descargar el código fuente, informar de problemas o colaborar con el proyecto, visite el [Repositorio de GitHub para la interfaz de la línea de comandos entre plataformas de Azure][].

-   Si tiene problemas al usar la xplat-cli, o Azure, visite los [Foros de Azure][].

-   Para obtener más información acerca de Azure, consulte [][2]<http://azure.microsoft.com/></a>.

  [PowerShell]: /es-es/manage/install-and-configure-windows-powershell/ "PowerShell"
  [CLI entre plataformas]: /es-es/manage/install-and-configure-cli/ "CLI entre plataformas"
  []: https://github.com/WindowsAzure/azure-sdk-tools-xplat
  [Instalación de la interfaz de la línea de comandos entre plataformas de Azure]: #install
  [Conexión con su suscripción de Azure]: #configure
  [Uso de la interfaz de la línea de comandos entre plataformas de Azure]: #use
  [Script de la interfaz de la línea de comandos entre plataformas de Azure]: #script
  [Recursos adicionales]: #additional-resources
  [1]: http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409
  [Windows Installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
  [Instalador de OS X]: http://go.microsoft.com/fwlink/?LinkId=252249
  [Portal de administración de Azure]: https://manage.windowsazure.com
  [Inicio de sesión como organización en Microsoft Azure]: http://www.windowsazure.com/es-es/documentation/articles/sign-up-organization/
  [¿Cuál es la diferencia entre la autenticación basada en cuentas y la basada en certificados?]: http://msdn.microsoft.com/es-es/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A7171371E
  [Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager]: /es-es/documentation/articles/xplat-cli-azure-resource-manager/
  [Advanced Bash-Scripting Guide]: http://tldp.org/LDP/abs/html/
  [Shell script]: http://en.wikipedia.org/wiki/Shell_script
  [Command-line reference A-Z]: http://technet.microsoft.com/es-es/library/bb490890.aspx
  [jsawk]: https://github.com/micha/jsawk
  [jq]: http://stedolan.github.io/jq/
  [Foros de Azure]: http://social.msdn.microsoft.com/Forums/windowsazure/es-es/home
  [2]: http://azure.microsoft.com
