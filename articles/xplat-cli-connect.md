<properties
	pageTitle="Inicio de sesión en Azure desde la CLI | Microsoft Azure"
	description="Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure) para Mac, Linux y Windows"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/08/2015"
	ms.author="danlep"/>

# Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)

La CLI de Azure es un conjunto de comandos de código abierto y multiplataforma para trabajar con la plataforma de Azure. En este artículo se describen distintas maneras de especificar las credenciales de su cuenta de Azure para conectar la CLI de Azure a su suscripción de Azure. Si no ha instalado aún la CLI, consulte [Instalación de la CLI de Azure](xplat-cli-install.md). Si carece de una suscripción de Azure, puede crear una [cuenta de prueba gratuita ](http://azure.microsoft.com/free/) en tan solo unos minutos.

Hay dos maneras de conectarse a su suscripción desde la CLI de Azure:

* **Inicio de sesión en Azure con una identidad de cuenta profesional o educativa o de cuenta Microsoft**: use el comando `azure login` de CLI versión 0.9.10 o posterior con cualquier tipo de identidad de cuenta para autenticarse a través de Azure Active Directory. CLI (versión 0.9.9 y posteriores) también admite la autenticación interactiva a través de un portal web para las cuentas que tienen habilitada la autenticación multifactor. Use también el comando `azure login` para autenticar una entidad de servicio de una aplicación de Azure Active Directory, algo que resulta útil para ejecutar servicios automatizados. Después de iniciar sesión con una identidad de cuenta compatible, puede usar comandos del modo de Azure Resource Manager o del modo de administración de servicios de Azure.

* **Descarga y uso de un archivo de configuración de publicación**: de este modo se instala un certificado en el equipo local que le permite realizar tareas de administración durante el período de validez de la suscripción y el certificado. Este método solo permite usar comandos del modo de administración de servicios de Azure.

>[AZURE.NOTE] Si usa una versión de CLI de Azure anterior a 0.9.10, solo podrá usar el comando `azure login` con las identidades de cuenta profesional o educativa. No funcionará con las identidades de cuenta Microsoft. Sin embargo, si quiere, puede [crear un identificador de cuenta profesional o educativa desde su identificador de cuenta Microsoft](virtual-machines/virtual-machines-windows-create-aad-work-id.md).

Para más información general sobre las diferentes identidades de cuenta y suscripciones de Azure, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

## Usar el inicio de sesión de Azure para autenticarse interactivamente a través de un portal web

Utilice el comando `azure login` --sin argumentos-- para autenticarse interactivamente con cualquiera de estas opciones:

- una cuenta profesional o educativa (también denominada *cuenta organizativa*) que requiere autenticación multifactor, o
- una identidad de cuenta Microsoft cuando quiera tener acceso a los comandos del modo de Resource Manager

> [AZURE.NOTE]  En ambos casos, la autenticación y la autorización se realizan con Azure Active Directory. Si usa una identidad de cuenta de Microsoft, el proceso de inicio de sesión obtiene acceso a su dominio predeterminado de Azure Active Directory. (Si se registró para obtener una cuenta de Azure gratuita, puede que no sepa que Azure Active Directory creó un dominio predeterminado para su cuenta).

Iniciar sesión de forma interactiva es fácil; escriba `azure login` y siga las instrucciones tal como se muestra a continuación:

	azure login                                                                                                                                                                                         
	info:    Executing command login
	info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate. If you're signing in as an Azure AD application, use the --username and --password parameters.

Copie el código que se muestra más arriba y abra http://aka.ms/devicelogin en un explorador. Indique el código y se le pedirá que escriba el nombre de usuario y la contraseña para la identidad que desea utilizar. Cuando se complete ese proceso, el shell de comandos completará el registro en curso. Sería algo parecido a lo siguiente:

	info:    Added subscription Visual Studio Ultimate with MSDN
	info:    Added subscription Azure Free Trial
	info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
	+
	info:    login command OK

## Usar el inicio de sesión de Azure con el nombre de usuario y la contraseña de una cuenta organizativa


Use el comando `azure login` con un parámetro de nombre de usuario o con un nombre de usuario y una contraseña para autenticarse cuando quiera usar una cuenta profesional o educativa que no requiere la autenticación multifactor. En el siguiente ejemplo se pasa el nombre de usuario de una cuenta organizativa:

	azure login -u ahmet@contoso.onmicrosoft.com
	info:    Executing command login
	Password: *********
	|info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

Escriba la contraseña cuando se le solicite.

Si se trata de la primera vez que inicia sesión con estas credenciales, se le pedirá que verifique si desea almacenar en caché un token de autenticación. Este aviso también aparecerá si ha usado anteriormente el comando `azure logout` (descrito más adelante en este artículo). Para omitir este aviso en escenarios de automatización, ejecute `azure login` con el parámetro `-q`.

   

## Usar el inicio de sesión de Azure con una entidad de servicio

Si ha creado una entidad de servicio para una aplicación de Active Directory y esa entidad de servicio tiene permisos en su suscripción, puede usar el comando `azure login` para autenticar dicha entidad de servicio. Según el escenario, podría proporcionar las credenciales de la entidad de servicio como parámetros explícitos del comando `azure login`, o bien a través de un código de aplicación o script de CLI. También puede usar un certificado para autenticar la entidad de servicio de forma no interactiva en escenarios de automatización. Para ver detalles y ejemplos, consulte [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](resource-group-authenticate-service-principal.md).

## Uso del método del archivo de configuración de publicación

Si solo necesita usar los comandos de la CLI del modo de administración de servicios de Azure, puede conectarse mediante un archivo de configuración de publicación.

* **Para descargar el archivo de configuración de publicación** de su cuenta, use el siguiente comando (disponible solo en el modo de administración de servicios).

		azure account download

    Esto abrirá el explorador predeterminado y le solicitará iniciar sesión en el [Portal de Azure clásico](https://manage.windowsazure.com). Después de iniciar sesión, se descarga un archivo `.publishsettings`. Tome nota de dónde se guarda este archivo.

    > [AZURE.NOTE] Si su cuenta está asociada a varios inquilinos de Azure Active Directory, puede que se le pida que seleccione para qué Active Directory desea descargar el archivo de configuración de publicación.

    Una vez seleccionado usando la página de descarga, o visitando el Portal de Azure clásico, el Active Directory seleccionado se convierte en el predeterminado que usan tanto el portal clásico como la página de descarga. Cuando se haya establecido el predeterminado, verá el texto "__click here to return to the selection page__" en la parte superior de la página de descarga. Utilice el vínculo proporcionado para volver a la página de selección.

* **Para importar el archivo de configuración de publicación**, ejecute el comando siguiente:

		azure account import <path to your .publishsettings file>

	>[AZURE.IMPORTANT]Después de importar la configuración de publicación, conviene eliminar el archivo `.publishsettings`. La CLI de Azure ya no lo necesita y supone un riesgo para la seguridad, puesto que se puede usar para obtener acceso a su suscripción.

## Varias suscripciones

Si tiene varias suscripciones de Azure, la conexión a Azure le dará acceso a todas las suscripciones asociadas a sus credenciales. Se selecciona una suscripción como predeterminada y la CLI de Azure la utilizará al realizar operaciones. Podrá ver las suscripciones, así como cuál es la predeterminada, mediante el comando `azure account list`. Este comando devuelve información similar a la siguiente:

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

En la lista anterior, la columna **Current** indica las suscripciones predeterminadas actuales como Azure-sub-1. Para cambiar la suscripción predeterminada, utilice el comando `azure account set` y especifique qué suscripción quiere que sea la predeterminada. Por ejemplo:

	azure account set Azure-sub-2

Esto cambia la suscripción predeterminada a Azure-sub-2.

> [AZURE.NOTE] El cambio de la suscripción predeterminada surte efecto de forma inmediata y es global; los nuevos comandos de la CLI de Azure, ya los ejecute desde la misma instancia de línea de comandos o desde una instancia diferente, usan la nueva suscripción predeterminada.

Si desea usar una suscripción no predeterminada con la CLI de Azure, pero no quiere cambiar la predeterminada actualmente, puede usar la opción `--subscription` para el comando y proporcionar el nombre de la suscripción que desee usar para la operación.

Una vez que esté conectado a su suscripción de Azure, puede empezar a usar los comandos de la CLI de Azure para trabajar con recursos de Azure.

## Modos de comando de CLI

La CLI de Azure proporciona dos modos de comando para trabajar con recursos de Azure, con conjuntos de comandos distintos:

* **Modo de Azure Resource Manager**: para trabajar con recursos de Azure en el modelo de implementación de Resource Manager. Para establecer este modo, ejecute `azure config mode arm`.

* **Modo de administración de servicios de Azure**: para trabajar con recursos de Azure en el modelo de implementación clásico. Para establecer este modo, ejecute `azure config mode asm`.

Al instalarla por primera vez, la CLI estará en modo de administración de servicios.

>[AZURE.NOTE]Los modos Administrador de recursos de Azure y Administración de servicios de Azure se excluyen mutuamente. Es decir, los recursos creados en un modo no se pueden administrar desde el otro.

## Almacenamiento de la configuración de la CLI

Independientemente de que inicie sesión con el comando `azure login` o importe la configuración de publicación, el perfil de CLI y los registros se almacenan en un directorio `.azure` ubicado en su directorio `user`. El sistema operativo protege su directorio `user`; no obstante, es recomendable que tome medidas adicionales para cifrar su directorio `user`. Puede hacerlo de las siguientes maneras:

* En Windows, modifique las propiedades del directorio o use BitLocker.
* En Mac, active FileVault para el directorio.
* En Ubuntu, use la característica del directorio de inicio (home) cifrado. Otras distribuciones de Linux ofrecen características similares.

## Cerrar sesión

Para cerrar sesión, utilice el comando siguiente:

	azure logout -u <username>

Si las suscripciones asociadas a la cuenta solo se autenticaron con Active Directory, al cerrar sesión se elimina la información de la suscripción del perfil local. Sin embargo, si también se ha importado un archivo de configuración de publicación para las suscripciones, al cerrar sesión se elimina solo la información relacionada con Active Directory del perfil local.
## Pasos siguientes

* Para usar los comandos de la CLI de Azure, consulte [Comandos de la CLI de Azure en el modo de Azure Resource Manager (ARM)](./virtual-machines/azure-cli-arm-commands.md) y [Comandos CLI de Azure en modo de Administración de servicios de Azure (asm)](virtual-machines-command-line-tools.md).

* Si desea obtener más información acerca de la CLI de Azure, descargar el código fuente, informar sobre problemas o colaborar con el proyecto, visite el [Repositorio de GitHub para la CLI de Azure](https://github.com/azure/azure-xplat-cli).

* Si tiene problemas al usar la CLI de Azure o Azure, visite los [Foros de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

<!---HONumber=AcomDC_0413_2016-->