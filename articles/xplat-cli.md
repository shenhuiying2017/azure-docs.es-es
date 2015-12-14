<properties
	pageTitle="CLI de Azure para Mac, Linux y Windows"
	description="Instalación y configuración de la CLI de Azure para Mac, Linux y Windows para administrar Servicios de Azure"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="squillace"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2015"
	ms.author="rasquill"/>

# Instalación y configuración de la interfaz de la línea de comandos (CLI) de Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Azure CLI](xplat-cli-install.md)

La CLI de Azure proporciona un conjunto de comandos de código abierto y multiplataforma para trabajar con la plataforma de Azure. La CLI de Azure proporciona muchas de las mismas funciones del Portal de Azure, como la posibilidad de administrar sitios web, máquinas virtuales y servicios móviles, Base de datos SQL y otros servicios ofrecidos por la plataforma Azure.

La CLI de Azure se escribe en JavaScript y requiere Node.js. Se implementa mediante el SDK de Azure para Node.js y se lanza con una licencia de Apache 2.0. El repositorio de proyectos se encuentra en [https://github.com/azure/azure-xplat-cli](https://github.com/azure/azure-xplat-cli).

Este documento indica cómo instalar y configurar la CLI para Mac, Linux y Windows, además de cómo usarla para realizar tareas básicas con la plataforma Azure.

<a id="install"></a>
## Instalación de la CLI de Azure

Para conocer los pasos de instalación de la CLI de Azure, lea la página [Instalación de la CLI de Azure](xplat-cli-install.md).


<a id="configure"></a>
## Conexión con su suscripción de Azure

Aunque algunos comandos proporcionados por la CLI de Azure funcionan sin una suscripción de Azure, la mayoría de ellos sí la requieren. Para aprender cómo configurar la CLI de Azure para que funcione con su suscripción, visite [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](xplat-cli-connect.md).


<a id="use"></a>
## Uso de la CLI de Azure

Se puede acceder a la CLI de Azure con el comando `azure`. Para ver una lista de los comandos disponibles, use el comando `azure` sin parámetros. Debería ver una información de ayuda similar a la siguiente:

	info:             _    _____   _ ___ ___
	info:            /_\  |_  / | | | _ \ __|
	info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
	info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
	info:       (_______ _ _)         _ ______ _)_ _
	info:              (______________ _ )   (___ _ _)
	info:
	info:    Microsoft Azure: Microsoft's Cloud Platform
	info:
	info:    Tool version 0.8.10
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

Los anteriores comandos de nivel máximo contienen comandos para trabajar con un área específica de Azure. Por ejemplo, el comando `azure account` contiene comandos relacionados con su suscripción de Azure, como las opciones `download` e `import` usadas anteriormente. Para obtener más información sobre las opciones y los comandos disponibles, consulte [Uso de la CLI de Azure para Mac, Linux y Windows].

La mayoría de los comandos tienen el formato `azure <command> <operation> [parameters]` y realizan una operación en un servicio u objeto como su configuración de cuenta. Otros comandos proporcionan subcomandos y siguen el formato `azure <command> <subcommand> <operation> [parameters]`. Los ejemplos siguientes son comandos que funcionan con su configuración de cuenta:

* Para ver las suscripciones que ha importado, utilice el siguiente:

		azure account list

* Si ha importado suscripciones, utilice el siguiente para establecer uno como predeterminado:

		azure account set <subscription>

Los parámetros `--help` o `-h` se pueden usar para ver la ayuda de comandos específicos. También puede usar el formato `azure help [command] [options]` para devolver la misma información. Por ejemplo, todos los comandos siguientes devuelven la misma información:

	azure account set --help

	azure account set -h

	azure help account set

Si duda de qué parámetros necesita un comando, consulte la ayuda usando `--help`, `-h` o `azure help [command]`.

### Establecimiento del modo de configuración

La CLI de Azure le permite realizar operaciones de administración en _recursos_ concretos, que son entidades administradas por el usuario; por ejemplo, un servidor de bases de datos, una base de datos o un sitio web. Este es el modo predeterminado de operación para la CLI de Azure y se conoce como **Administración de servicios de Azure**. No obstante, cuando tiene una solución compleja que consta de varios recursos, resulta útil poder administrar toda la solución como una sola unidad.

Para poder administrar un grupo de recursos como una sola unidad lógica, o _grupo de recursos_, hemos introducido una vista previa de **Administrador de recursos** como una nueva forma de administrar los recursos de Azure.

>[AZURE.NOTE]Resource Manager está actualmente en vista previa y no proporciona el mismo nivel de capacidades de administración que Azure Service Management.

Para obtener compatibilidad con el nuevo Administrador de recursos, la CLI de Azure le permite alternar entre estos ’modos’ de administración usando el comando `azure config mode`.

De forma predeterminada, la CLI de Azure usa el modo Administración de servicios de Azure. Para pasar al modo Resource Manager, utilice el siguiente comando:

	azure config mode arm

Para volver al modo Azure Service Management, utilice el siguiente comando:

	azure config mode asm

>[AZURE.NOTE]Los modos Resource Manager y Azure Service Management se excluyen mutuamente. Es decir, los recursos creados en un modo no se pueden administrar desde el otro.

Para obtener más información acerca de cómo trabajar con Administrador de recursos usando la CLI de Azure, consulte [Uso de la interfaz de la línea de comandos entre plataformas de Azure con Administrador de recursos][cliarm].

### Trabajo con los servicios en el modo de administración de servicios de Azure

La CLI de Azure le permite administrar fácilmente los servicios de Azure. En este ejemplo, aprenderá a usar la CLI de Azure para administrar un sitio web de Azure.

1. Utilice el comando siguiente para crear un sitio web de Azure. Reemplace **mywebsite** por un nombre único.

		azure site create mywebsite

	Se le pedirá que especifique la región en la que se creará el sitio web. Seleccione una región que esté geográficamente próxima a la suya. Cuando este comando se complete, el sitio web estará disponible en http://mywebsite.azurewebsites.net (reemplace **mywebsite** por el nombre que ha especificado).

	> [AZURE.NOTE]Si usa Git para controlar el origen del proyecto, puede especificar el parámetro `--git` para crear un repositorio Git en Azure para este sitio web. Así, también se inicializa un repositorio Git en el directorio desde el cual se ejecutó el comando, si todavía no hay ninguno. También se creará un Git remoto llamado __azure__, que se puede usar para insertar implementaciones en el sitio web de Azure usando el comando `git push azure master`.

	> [AZURE.NOTE]Si recibe un error que indica que 'site' no es un comando de azure, es muy probable que la CLI de Azure esté en modo de grupo de recursos. Para volver a cambiar al modo de recursos, use el comando `azure config mode asm`.

2. Utilice el comando siguiente para enumerar los sitios web para su suscripción:

		azure site list

	La lista debería contener el sitio creado en el paso anterior.

2. Utilice el comando siguiente para detener el sitio web. Reemplace **mywebsite** por el nombre del sitio.

		azure site stop mywebsite

	Cuando el comando se complete, podrá actualizar el explorador para verificar que el sitio se ha detenido.

3. Utilice el comando siguiente para iniciar el sitio web. Reemplace **mywebsite** por el nombre del sitio.

		azure site start mywebsite

	Cuando el comando se complete, podrá actualizar el explorador para verificar que el sitio se ha iniciado.

4. Utilice el comando siguiente para eliminar el sitio web. Reemplace **mywebsite** por el nombre del sitio.

		azure site delete mywebsite

	Cuando el comando se complete, use el comando `azure site list` para verificar que el sitio web ya no existe.

<a id="script"></a>
## Script de la CLI de Azure para Mac, Linux y Windows

Aunque puede usar la CLI de Azure para emitir comandos manualmente, también puede crear flujos de trabajo de automatización complejos aprovechando las capacidades de su intérprete de línea de comandos y otras utilidades de línea de comandos disponibles en su sistema. Por ejemplo, el comando siguiente detendrá todos los sitios web de Azure que se estén ejecutando:

	azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

Este ejemplo canaliza una lista de sitios web al comando `grep`, que inspecciona cada línea en busca de la cadena 'Running'. Todas las líneas que coincidan se canalizan al comando `awk`, que llama a `azure site stop` y usa la segunda columna que se le pasa (el nombre del sitio en ejecución) como el nombre del sitio que hay que detener.

Aunque esto demuestra cómo se pueden encadenar unos comandos a otros, también puede crear scripts más elaborados usando las funciones de scripting proporcionadas por su intérprete de línea de comandos. Los diferentes intérpretes de línea de comandos tienen distintas características y sintaxis de scripting. Bash es probablemente el intérprete de línea de comandos más usado para los sistemas basados en UNIX, incluyendo Linux y OS X.

Para obtener información acerca del scripting con Bash, consulte [Advanced Bash-Scripting Guide][advanced-bash].

Para obtener más información general acerca del scripting en sistemas basados en OS X o Linux, consulte [Shell script][script].

Para obtener información acerca del scripting en los sistemas basados en Windows usando archivos por lotes, consulte [Referencia A-Z de la línea de comandos][batch].

### Interpretación de los resultados

Al crear scripts, a menudo tendrá que capturar el resultado de un comando y pasarlo a otro, o realizar una operación en el resultado, como la comprobación de un valor concreto. La CLI de Azure genera resultados para STDOUT y STDERR. Cada línea lleva como prefijo las cadenas `info:` en el caso de mensajes de información o `data:` en el caso de los datos devueltos sobre un servicio; no obstante, puede ordenar a la CLI de Azure que devuelva información más detallada usando los parámetros `--verbose` o `-v`. Esto devolverá información adicional que se indicará con el prefijo `verbose:`.

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

Observe que la información de `verbose:` parece ser datos en formato JSON. Puede usar el parámetro `--json` para devolver la información en formato JSON si trabaja con las utilidades que entienden JSON de forma nativa, como [jsawk](https://github.com/micha/jsawk) o [jq](http://stedolan.github.io/jq/). Por ejemplo:

	azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q

El comando anterior recupera una lista de sitios web como JSON, a continuación, usa jsawk para recuperar los nombres de los sitios y, finalmente, usa xargs para ejecutar un comando de eliminación en cada sitio, pasando el nombre del sitio como parámetro.

>[AZURE.NOTE]El parámetro `--json` bloquea la generación de estados o la información de datos (cadenas con los prefijos `info:` y `data:`). Por ejemplo, si se usa el parámetro `--json` con `azure site create`, no se devuelven resultados porque este comando no devuelve datos diferentes de `info:`.

### Trabajo con errores

Aunque la CLI de Azure registra la información de los errores en STDERR, la información adicional acerca de los errores también podría quedar registrada en un archivo **azure.err** en el directorio desde el que se ejecutó el script. Si la información está registrada en este archivo, se escribirá lo siguiente en STDOUT:

	info:    Error information has been recorded to azure.err

### Estado de salida

Algunos de los comandos de la CLI de Azure no devuelven un estado de salida distinto de cero si faltan los parámetros obligatorios. En lugar de ello, le pedirán al usuario que introduzca la información. Por ejemplo, al usar el comando `azure site create` para crear un sitio web, si no se especifica un nombre de sitio o el parámetro `--location`, se le pedirá que especifique estos valores.

Si crea un script basado en el estado de salida, compruebe que los comandos de la CLI de Azure que use no pedirán al usuario que introduzca información.

<a id="additional-resources"></a>

## Recursos adicionales

* [Uso de la interfaz de la línea de comandos de Azure con Administración de servicios][Using the Azure CLI]

* [Uso de la interfaz de la línea de comandos de Azure con Administrador de recursos][cliarm]

* Si desea obtener más información acerca de la CLI de Azure para descargar el código fuente, informar sobre problemas o colaborar con el proyecto, visite el [Repositorio de GitHub para la CLI de Azure](https://github.com/azure/azure-xplat-cli).

* Si tiene problemas al usar la CLI de Azure o Azure, visite los [Foros de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Para obtener más información acerca de Azure, consulte [http://azure.microsoft.com/](http://azure.microsoft.com).




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert

[Azure Web Site]: ../media/freetrial.png
[select a preview feature]: ../media/antares-iaas-preview-02.png
[select subscription]: ../media/antares-iaas-preview-03.png
[free-trial]: http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script]: http://en.wikipedia.org/wiki/Shell_script
[batch]: http://technet.microsoft.com/library/bb490890.aspx
[cliarm]: xplat-cli-azure-resource-manager.md
[portal]: https://manage.windowsazure.com
[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[Using the Azure CLI]: virtual-machines-command-line-tools.md

<!---HONumber=AcomDC_1203_2015-->