<properties
	pageTitle="Cómo instalar y configurar Azure PowerShell"
	description="Obtenga información acerca de cómo instalar y configurar Azure PowerShell."
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp-at-msft"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="coreyp"/>

# Cómo instalar y configurar Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="CLI de Azure">CLI de Azure</a></div>

##¿Qué es Azure PowerShell?#
Azure PowerShell es un módulo que ofrece cmdlet para administrar Azure con Windows PowerShell. Puede utilizar los cmdlets para crear, probar, implementar y administrar soluciones y servicios ofrecidos a través de la plataforma de Azure. En la mayoría de los casos, se pueden usar los cmdlet para las mismas tareas que el Portal de administración de Azure, como la creación y configuración de servicios en la nube, máquinas virtuales, redes virtuales y aplicaciones web.

El módulo y el código fuente están disponibles para descargarse en un repositorio disponible públicamente:

- [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi)
- [Código fuente de Azure PowerShell 1.0.1](https://github.com/Azure/azure-powershell/archive/v1.0.1-November2015.zip)

<a id="Install"></a>
## Paso 1: Instalar
Descargue e instale [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi) <a id="Connect"></a>

## Paso 2: Iniciar
El módulo instala una consola personalizada para Azure PowerShell. Puede ejecutar los cmdlet desde la consola estándar de Windows PowerShell o desde la consola de Azure PowerShell.

## Paso 3: Conectar
Los cmdlets necesitan la suscripción para que puedan administrar los servicios. Puede comprar una suscripción de Azure si no tiene ya una. Para instrucciones, consulte [Instrucciones para contratar Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Escriba **Add-AzureAccount**

2. Escriba la dirección de correo electrónico y la contraseña asociadas a su cuenta. Azure autentica y guarda las credenciales y, a continuación, cierra la ventana.

--O--

Inicie sesión en su cuenta profesional o educativa:

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

> [AZURE.NOTE]Este método de inicio de sesión no interactivo solo funciona con una cuenta profesional o educativa. Una cuenta profesional o educativa es un usuario bajo la administración del trabajo o de la escuela y que está definido en la instancia de Azure Active Directory para el trabajo o la escuela. Si actualmente no tiene una cuenta profesional o educativa y usa una cuenta Microsoft para iniciar sesión en su suscripción de Azure, puede crear una fácilmente siguiente los pasos que se indican a continuación.

> 1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com) y haga clic en **Active Directory**.

> 2. Si no hay ningún directorio, seleccione **Crear su directorio** y proporcione la información que se le pida.

> 3. Seleccione su directorio y agregue un nuevo usuario. Este usuario nuevo puede iniciar sesión con una cuenta profesional o educativa. Durante la creación del usuario, se le proporcionará una dirección de correo electrónico para el usuario y una contraseña temporal. Guarde esta información, la usará en el paso 5 que viene a continuación.

> 4. En el portal de administración, seleccione **Configuración** y, a continuación, **Administradores**. Seleccione **Agregar** y agregue el usuario nuevo como coadministrador. Esto permite que la cuenta profesional o educativa administre su suscripción de Azure.

> 5. Finalmente, cierre sesión en el portal de Azure y, a continuación, vuelva a iniciarla usando la cuenta profesional o educativa. Si es la primera vez que inicia sesión con esta cuenta, se le pedirá que cambie la contraseña.

> Para obtener más información acerca del inicio de sesión en Microsoft Azure con una cuenta profesional o educativa, consulte [Inicio de sesión en Azure como una organización](sign-up-organization.md).

### Visualización de los detalles de las cuentas y las suscripciones

Puede tener varias cuentas y suscripciones disponibles para su uso por parte de Azure PowerShell. Puede agregar múltiples cuentas ejecutando **Add-AzureAccount** más de una vez.

Para mostrar las cuentas de Azure disponibles, escriba **Get-AzureAccount**.

Para mostrar las suscripciones de Azure, escriba **Get-AzureSubscription**.

## Paso 4: Prueba<a id="Ex"></a>


Después de instalar el módulo y configurar el equipo para que se conecte a su suscripción, puede crear una aplicación web de Azure para asegurarse de que todo funciona correctamente.

1. Inicie la consola de Azure PowerShell.

2. Elija un nombre para la aplicación web. Elija un nombre que se ajuste a las convenciones de nomenclatura de DNS. Los nombres válidos puede contener solo las letras de la 'a' a la 'z', los números entre '0' y '9', y un guión ('-').

	El nombre de la aplicación web debe ser único en Azure. En este ejemplo, usaremos "mySite". Sin embargo, asegúrese de elegir un nombre diferente, tal como el nombre de cuenta seguido de un número.

	Una vez que elija un nombre, escriba un comando similar al siguiente. Sustituya el nombre de la aplicación web por "mySite".

		New-AzureWebsite mySite

	El cmdlet crea la aplicación web y devuelve un objeto que representa la nueva aplicación web. Las propiedades del objeto incluyen información útil acerca de la aplicación web.

3. Para obtener información sobre la aplicación web, escriba el comando siguiente. Devuelve un poco de información sobre todas las aplicaciones web de la suscripción, incluida la que acaba de crear.

		Get-AzureWebsite

4. Para obtener más información acerca de la aplicación web, incluya el nombre de esta en el comando. Asegúrese de sustituir el nombre de la aplicación web por "mySite".

		Get-AzureWebsite -Name mySite

5. Las aplicaciones web se inician después de crearse. Para detener la aplicación web, escriba este comando, incluido el nombre de la aplicación web.

		Stop-AzureWebsite -Name mySite

6. Para comprobar que el estado del sitio es 'detenido', vuelva a ejecutar el comando Get-AzureWebsite.

		Get-AzureWebsite

7. Para completar esta prueba, elimine la aplicación web. Escriba:

		Remove-AzureWebsite -Name mySite

7. Para completar la tarea, confirme que la aplicación web se ha eliminado.

		Get-AzureWebsite -Name mySite

##<a id="Help"></a>Ayuda##

Estos recursos ofrecen ayuda para cmdlets concretos:


-   Desde la consola, puede utilizar el sistema de ayuda integrado. El cmdlet **Get-Help** proporciona acceso a este sistema. 



- En la biblioteca de Azure existe información de referencia acerca de los cmdlets de los módulos de Azure PowerShell. Para obtener información, consulte [Referencia de cmdlets de Azure](https://msdn.microsoft.com/library/azure/dn708514.aspx).

Para obtener ayuda de la comunidad, utilice estos foros populares:

- [Foro de Azure en MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

<!---HONumber=AcomDC_1217_2015-->