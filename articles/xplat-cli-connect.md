<properties
	pageTitle="Inicio de sesión desde la interfaz de la línea de comandos de Azure (CLI de Azure) | Microsoft Azure"
	description="Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="danlep"/>

# Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)

La CLI de Azure es un conjunto de comandos de código abierto y multiplataforma para trabajar con la plataforma de Azure. Este documento describe cómo conectarse a su suscripción de Azure desde la CLI de Azure. Para obtener instrucciones de instalación, consulte [Instalación de la CLI de Azure](xplat-cli-install.md).

<a id="configure"></a>
## Conexión con su suscripción de Azure

La mayoría de los comandos proporcionados por la CLI de Azure requieren una conexión a una cuenta de Azure. Hay dos modos de configurar la CLI de Azure para que funcione con su suscripción:

* Inicie sesión en Azure con una cuenta profesional o educativa (también conocida como una cuenta de organización). Se usa Azure Active Directory para autenticar las credenciales.

o

* Descargar y usar un archivo de configuración de publicación. Esto instala un certificado que le permite realizar tareas de administración durante el periodo de validez de la suscripción y el certificado.

Para obtener más información acerca de la administración de la autenticación y la suscripción, consulte ["¿Cuál es la diferencia entre la autenticación basada en cuentas y la basada en certificados?"][authandsub].

En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][free-trial].

> [AZURE.NOTE]Los servicios de Azure más flexibles y avanzados utilizan el Administrador de recursos de Azure o el [modo ARM](xplat-cli-azure-resource-manager.md). El modo ARM requiere conexión a Azure utilizando una cuenta profesional o educativa, con el método de registro descrito a continuación.

### Uso del método de inicio de sesión

El método de inicio de sesión solo funciona con una cuenta profesional o educativa. Esta cuenta está administrada por su organización y definida en el Azure Active Directory de la organización.

> [AZURE.NOTE]Si actualmente no tiene una cuenta profesional o educativa y usa una cuenta personal para iniciar sesión en su suscripción de Azure, puede crear una fácilmente siguiente los pasos que se indican a continuación.
>
> 1. Inicie sesión en el [Portal de Azure][portal], y seleccione **Active Directory**.
>
> 2. Si no hay ningún directorio, seleccione **Crear su directorio** y proporcione la información que se le pida.
>
> 3. Seleccione su directorio y agregue un nuevo usuario. Este usuario nuevo es una cuenta profesional o educativa.
>
>     Durante la creación del usuario, se le proporcionará una dirección de correo electrónico para el usuario y una contraseña temporal. Guarde esta información, porque se necesitará más adelante.
>
> 4. En el portal de Azure, seleccione **Configuración** y, a continuación, **Administradores**. Seleccione **Agregar** y agregue el usuario nuevo como coadministrador. Esto permite que la cuenta profesional o educativa administre su suscripción de Azure.
>
> 5. Finalmente, cierre sesión en el portal de Azure y, a continuación, vuelva a iniciarlo usando la cuenta profesional o educativa. Si es la primera vez que inicia sesión con esta cuenta, se le pedirá que cambie la contraseña.
>
> 6. Asegúrese de ver las suscripciones cuando inicie sesión como cuenta profesional o educativa.
>
>Para obtener más información acerca de cuentas profesionales o educativas, consulte [Inicio de sesión como organización en Microsoft Azure][signuporg].

Para iniciar sesión desde la CLI de Azure con una cuenta profesional o educativa, use el comando siguiente:

	azure login -u <username>

y escriba la contraseña cuando se le solicite.

> [AZURE.NOTE]Si es la primera vez que inicia sesión con estas credenciales, se le pedirá que verifique que desea copiar en caché un token de autenticación. Este aviso también aparecerá si ha usado anteriormente el comando `azure logout` que se indica a continuación. Para omitir este aviso en escenarios de automatización, utilice el parámetro `-q` con el comando `azure login`.

Para cerrar sesión, utilice el comando siguiente:

	azure logout -u <username>

> [AZURE.NOTE]Si las suscripciones asociadas a la cuenta solo se autenticaron con Active Directory, al cerrar sesión se eliminará la información de la suscripción del perfil local. Sin embargo, si también se ha importado un archivo de configuración de publicación para las suscripciones, al cerrar sesión se eliminará solo la información relacionada con Active Directory del perfil local.

### Uso del método del archivo de configuración de publicación

> [AZURE.NOTE]Si se conecta mediante este método, solo puede utilizar los comandos de administración de servicios de Azure (o el modo ASM).

Para descargar la configuración de publicación de su cuenta, use el comando siguiente:

	azure account download

Esto abrirá su explorador predeterminado y le solicitará que inicie sesión en el [Portal de Azure][portal]. Después de iniciar sesión, se descargará un archivo `.publishsettings`. Tome nota de dónde se guarda este archivo.

> [AZURE.NOTE]Si su cuenta está asociada a varios inquilinos de Azure Active Directory, puede que se le pida que seleccione para qué Active Directory desea descargar el archivo de configuración de publicación.
>
> Una vez seleccionado usando la página de descarga, o visitando el Portal de Azure, el Active Directory seleccionado se convierte en el predeterminado que usan tanto el portal como la página de descarga. Cuando se haya establecido el predeterminado, verá el texto '__click here to return to the selection page__ en la parte superior de la página de descarga. Utilice el vínculo proporcionado para volver a la página de selección.

A continuación, importe el archivo `.publishsettings` ejecutando el siguiente comando:

	azure account import <path to your .publishsettings file>

Después de importar su configuración de publicación, debe eliminar el archivo `.publishsettings`, porque ya no lo necesita la CLI de Azure y supone un riesgo para la seguridad, ya que se puede usar para obtener acceso a su suscripción.

> [AZURE.NOTE]Al iniciar una sesión en una cuenta profesional o educativa o importar la configuración de publicación, la información de acceso a su suscripción de Azure se almacena en un directorio `.azure` ubicado en su directorio `user`. El sistema operativo protege su directorio `user`; no obstante, es recomendable que tome medidas adicionales para cifrar su directorio `user`. Puede hacerlo de las siguientes maneras:
>
> * En Windows, modifique las propiedades del directorio o use BitLocker.
> * En Mac, active FileVault para el directorio.
> * En Ubuntu, use la característica del directorio de inicio (home) cifrado. Otras distribuciones de Linux ofrecen características equivalentes.

### Varias suscripciones

Si tiene varias suscripciones de Azure, la conexión a Azure le dará acceso a todas las suscripciones asociadas a sus credenciales. Se seleccionará una suscripción como predeterminada y la CLI de Azure la utilizará al realizar operaciones. Podrá ver las suscripciones, así como cuál es la predeterminada, mediante el comando `azure account list`. Este comando devolverá información similar a la siguiente:

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

En la lista anterior, la columna **Current** indica las suscripciones predeterminadas actuales como Azure-sub-1. Para cambiar la suscripción predeterminada, utilice el comando `azure account set` y especifique qué suscripción quiere que sea la predeterminada. Por ejemplo:

	azure account set Azure-sub-2

Esto cambiará la suscripción predeterminada a Azure-sub-2.

> [AZURE.NOTE]El cambio de la suscripción predeterminada surte efecto de forma inmediata y es global; los nuevos comandos de la CLI de Azure, ya los ejecute desde la misma instancia de línea de comandos o desde una instancia diferente, usarán la nueva suscripción predeterminada.

Si desea usar una suscripción no predeterminada con la CLI de Azure, pero no quiere cambiar la predeterminada actualmente, puede usar la opción `--subscription` para el comando y proporcionar el nombre de la suscripción que desee usar para la operación.

Una vez que esté conectado a su suscripción de Azure, puede empezar a usar los comandos de la CLI de Azure. Para obtener más información, consulte [Uso de la CLI de Azure](xplat-cli.md).

<a id="additional-resources"></a>
## Recursos adicionales

* [Uso de la CLI de Azure con los comandos de administración de servicios (o modo ASM)][cliasm]

* [Uso de la CLI de Azure con los comandos de administración de recursos (o modo ARM)][cliarm]

* Si desea obtener más información acerca de la CLI de Azure para descargar el código fuente, informar sobre problemas o colaborar con el proyecto, visite el [Repositorio de GitHub para la CLI de Azure](https://github.com/azure/azure-xplat-cli).

* Si tiene problemas al usar la CLI de Azure o Azure, visite los [Foros de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Para obtener más información acerca de Azure, consulte [http://azure.microsoft.com/](http://azure.microsoft.com).





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: xplat-cli-azure-resource-manager.md

<!---HONumber=58_postMigration-->