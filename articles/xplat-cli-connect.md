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
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/18/2015"
	ms.author="danlep"/>

# Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)

La CLI de Azure es un conjunto de comandos de código abierto y multiplataforma para trabajar con la plataforma de Azure. En este artículo se describe cómo conectarse a su suscripción de Azure desde la CLI de Azure para utilizar todos los comandos de la CLI. Si no ha instalado aún la CLI, consulte [Instalación de la CLI de Azure](xplat-cli-install.md).

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-include.md)]En este artículo se trata la creación y administración de recursos con el modelo de implementación del Administrador de recursos o el modelo de implementación clásica.

Hay dos maneras de conectarse a su suscripción desde la CLI de Azure:

* **Iniciar sesión en Azure con una cuenta profesional o educativa**: en este caso, se utiliza Azure Active Directory para autenticar las credenciales. A partir de versión 0.9.9 de la CLI, esta admite cuentas de organización habilitadas para la autenticación multifactor. Después de iniciar sesión, puede utilizar el Administrador de recursos o los comandos clásicos (administración de servicios).

* **Descarga y uso de un archivo de configuración de publicación**: de este modo se instala un certificado que le permite realizar tareas de administración durante el periodo de validez de la suscripción y el certificado. Este método solo permite usar comandos clásicos (administración de servicios).

Para obtener más información acerca de la administración de la autenticación y la suscripción, consulte ["¿Cuál es la diferencia entre la autenticación basada en cuentas y la basada en certificados?"][authandsub].

En caso de no tener ninguna cuenta de Azure, puede crear una de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][free-trial].


## Uso del método de inicio de sesión

El método de inicio de sesión solo funciona con una cuenta profesional o educativa, también conocida como *cuenta de organización*. Esta cuenta está administrada por su organización y definida en el Azure Active Directory de la organización. También puede [crear una cuenta de organización](#create-an-organizational-account) si no tiene una.


* **Para iniciar sesión** desde la CLI de Azure con una cuenta profesional o educativa, use el comando siguiente:

	```
	azure login -u <username>
	```

	Escriba la contraseña cuando se le solicite.

	Si se trata de la primera vez que inicia sesión con estas credenciales, se le pedirá que verifique si desea almacenar en caché un token de autenticación. Este aviso también aparece si ha usado anteriormente el comando `azure logout` (descrito a continuación). Para omitir este aviso en escenarios de automatización, ejecute `azure login` con el parámetro `-q`.

* **Para cerrar sesión**, utilice el comando siguiente:

	```
	azure logout -u <username>
	```

	Si las suscripciones asociadas a la cuenta solo se autenticaron con Active Directory, al cerrar sesión se elimina la información de la suscripción del perfil local. Sin embargo, si también se ha importado un archivo de configuración de publicación para las suscripciones, al cerrar sesión se elimina solo la información relacionada con Active Directory del perfil local.

### Multi-Factor Authentication
A partir de la versión 0.9.9 de la CLI de Azure, puede iniciar sesión con una cuenta de organización que use la autenticación multifactor (autenticación con una contraseña y uno o varios métodos de comprobación adicionales, como un dispositivo de confianza o el suministro de otros datos personales).

Después de ejecutar `azure login` con el nombre de usuario y la contraseña de la cuenta, la CLI proporciona la dirección de una página web que se debe abrir. Las instrucciones le dirigen a la especificación de un código en esa página para continuar con la autenticación. Una vez que se cumpla la directiva de autenticación, la CLI de Azure completa el inicio de sesión.


### Creación de una cuenta de organización

Si actualmente no tiene una cuenta profesional o educativa y usa una cuenta personal para iniciar sesión en su suscripción de Azure, puede crear fácilmente una cuenta de organización mediante los pasos que se indican a continuación.

1. Inicie sesión en el [Portal de Azure][portal] y seleccione **Active Directory**.

2. Si no hay ningún directorio, seleccione **Crear su directorio** y proporcione la información que se le pida.

3. Seleccione su directorio y agregue un nuevo usuario. Este usuario nuevo es una cuenta profesional o educativa.

	Durante la creación del usuario, se le proporcionará una dirección de correo electrónico para el usuario y una contraseña temporal. Guarde esta información, porque se necesitará más adelante.

4. En el portal de Azure, seleccione **Configuración** y, a continuación, **Administradores**. Seleccione **Agregar** y agregue el usuario nuevo como coadministrador. Esto permite que la cuenta profesional o educativa administre su suscripción de Azure.

5. Finalmente, cierre sesión en el portal de Azure y, a continuación, vuelva a iniciarlo usando la cuenta profesional o educativa. Si es la primera vez que inicia sesión con esta cuenta, se le pide que cambie la contraseña.

	Asegúrese de que ve las suscripciones al iniciar sesión con la cuenta nueva.

Para obtener más información acerca de cuentas profesionales o educativas, consulte [Inicio de sesión como organización en Microsoft Azure][signuporg].

## Uso del método del archivo de configuración de publicación

Si solo necesita usar los comandos clásicos de la CLI (administración de servicios), puede conectarse utilizando un archivo de configuración de publicación.

* **Para descargar el archivo de configuración de publicación** para su cuenta, use el comando siguiente:

	```
	azure account download
	```

	Esto abrirá su explorador predeterminado y le solicitará iniciar sesión en el [Portal de Azure][portal]. Después de iniciar sesión, se descarga un archivo `.publishsettings`. Tome nota de dónde se guarda este archivo.

	> [AZURE.NOTE]Si su cuenta está asociada a varios inquilinos de Azure Active Directory, puede que se le pida que seleccione para qué Active Directory desea descargar el archivo de configuración de publicación.
	>
	> Una vez seleccionado usando la página de descarga, o visitando el Portal de Azure, el Active Directory seleccionado se convierte en el predeterminado que usan tanto el portal como la página de descarga. Cuando se haya establecido el predeterminado, verá el texto "__click here to return to the selection page__" en la parte superior de la página de descarga. Utilice el vínculo proporcionado para volver a la página de selección.

* **Para importar el archivo de configuración de publicación**, ejecute el comando siguiente:

	```
	azure account import <path to your .publishsettings file>
	```

	Después de importar su configuración de publicación, debe eliminar el archivo `.publishsettings`, porque ya no lo necesita la CLI de Azure y supone un riesgo para la seguridad, ya que se puede usar para obtener acceso a su suscripción.


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

> [AZURE.NOTE]El cambio de la suscripción predeterminada surte efecto de forma inmediata y es global; los nuevos comandos de la CLI de Azure, ya los ejecute desde la misma instancia de línea de comandos o desde una instancia diferente, usan la nueva suscripción predeterminada.

Si desea usar una suscripción no predeterminada con la CLI de Azure, pero no quiere cambiar la predeterminada actualmente, puede usar la opción `--subscription` para el comando y proporcionar el nombre de la suscripción que desee usar para la operación.

Una vez que esté conectado a su suscripción de Azure, puede empezar a usar los comandos de la CLI de Azure. Para obtener más información, consulte [Uso de la CLI de Azure](xplat-cli.md).

## Almacenamiento de la configuración de la CLI

Independientemente de que inicie sesión con una cuenta profesional o educativa o importe la configuración de publicación, el perfil de CLI y los registros se almacenan en un directorio `.azure` ubicado en su directorio `user`. El sistema operativo protege su directorio `user`; no obstante, es recomendable que tome medidas adicionales para cifrar su directorio `user`. Puede hacerlo de las siguientes maneras:

* En Windows, modifique las propiedades del directorio o use BitLocker.
* En Mac, active FileVault para el directorio.
* En Ubuntu, use la característica del directorio de inicio (home) cifrado. Otras distribuciones de Linux ofrecen características similares.

## Recursos adicionales

* [Uso de la interfaz de la línea de comandos de Azure con los comandos de la Administración de servicios (modo clásico)][cliasm]

* [Uso de la interfaz de la línea de comandos de Azure con el Administrador de recursos][cliarm]

* Si desea obtener más información acerca de la CLI de Azure, descargar el código fuente, informar sobre problemas o colaborar con el proyecto, visite el [Repositorio de GitHub para la CLI de Azure](https://github.com/azure/azure-xplat-cli).

* Si tiene problemas al usar la CLI de Azure o Azure, visite los [Foros de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=Sept15_HO4-->