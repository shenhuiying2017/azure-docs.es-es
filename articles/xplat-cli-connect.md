---
title: "Inicio de sesión en Azure desde la CLI | Microsoft Docs"
description: "Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure) para Mac, Linux y Windows"
editor: tysonn
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: ed856527-d75e-4e16-93fb-253dafad209d
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: rasquill
"\"/": 
translationtype: Human Translation
ms.sourcegitcommit: 3136b8345d0c851c29a9498089da73c8564549d1
ms.openlocfilehash: 88414392c4ee03e0b140bba0f73e44b6886c4038
ms.lasthandoff: 01/31/2017


---
# <a name="log-in-to-azure-from-the-azure-cli"></a>Inicio de sesión en Azure desde la CLI de Azure
La CLI de Azure es un conjunto de comandos de código abierto y multiplataforma para trabajar con recursos de Azure. En este artículo se describen las distintas maneras de proporcionar las credenciales de su cuenta de Azure apara conectar la CLI de Azure a su suscripción de Azure:

* Ejecute el comando `azure login` de la CLI para autenticarse con Azure Active Directory. Este método le brinda acceso a los comandos de la CLI en ambos [modos de comando](#cli-command-modes). Cuando ejecuta el comando sin opciones adicionales, `azure login` le solicita seguir iniciando sesión de manera interactiva a través de un portal web. Si desea ver opciones adicionales del comando `azure login`, consulte los escenarios de este artículo o escriba `azure login --help`.
* Si solo necesita usar los comandos de la CLI del modo Azure Service Management (no se recomienda para la mayoría de implementaciones nuevas), puede descargar e instalar un archivo de configuración de publicación en el equipo.

Si no ha instalado aún la CLI, consulte [Instalación de la CLI de Azure](xplat-cli-install.md). Si carece de una suscripción de Azure, puede crear una [cuenta de prueba gratuita](http://azure.microsoft.com/free/) en tan solo unos minutos.

Para obtener más información general sobre las diferentes identidades de cuenta y suscripciones de Azure, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="scenario-1-azure-login-with-interactive-login"></a>Escenario 1: Inicio de sesión de Azure con inicio de sesión interactivo
Con ciertas cuentas, la CLI requiere que ejecute `azure login` y luego continúe con el proceso de inicio con un explorador web a través de un portal web, un proceso llamado *inicio de sesión interactivo*. Un motivo común es cuando tiene una cuenta profesional o educativa (también conocida como *cuenta de organización*) que está configurada para requerir autenticación multifactor. También usa el inicio de sesión interactivo con su cuenta de Microsoft cuando desea usar los comandos de modo de Resource Manager.

El inicio de sesión interactivo es simple: escriba `azure login`, sin ninguna opción, tal como aparece en el ejemplo siguiente:

```
azure login
```                                                                                             

La salida debe tener un aspecto similar al siguiente:

```         
info:    Executing command login
info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate.
```
Copie el código que se le entrega en la salida del comando y abra http://aka.ms/devicelogin en el explorador, u otra página si así se especifica. (Puede abrir el explorador en el mismo equipo o en un equipo o dispositivo distinto). Indique el código y se le pedirá que escriba el nombre de usuario y la contraseña para la identidad que desea utilizar. Cuando se completa el proceso, el shell de comandos completa el inicio de sesión. Sería algo parecido a lo siguiente:

    info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Added subscription Azure Free Trial
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

> [!NOTE]
> Con el inicio de sesión interactivo, la autenticación y la autorización se realizan con Azure Active Directory. Si usa una identidad de cuenta de Microsoft, el proceso de inicio de sesión obtiene acceso a su dominio predeterminado de Azure Active Directory. (Si se registro para obtener una cuenta de Azure gratuita, Azure Active Directory creó automáticamente un dominio predeterminado para la cuenta).
>
>

## <a name="scenario-2-azure-login-with-a-username-and-password"></a>Escenario 2: Inicio de sesión de Azure con un nombre de usuario y contraseña
Use el comando `azure login` con el parámetro de nombre de usuario (`-u`) para autenticarse cuando quiera usar una cuenta profesional o educativa que no requiere la autenticación multifactor. Se le pedirá la contraseña en la línea de comandos (o bien puede pasar la contraseña como un parámetro adicional del comando `azure login`). En el siguiente ejemplo se pasa el nombre de usuario de una cuenta organizativa:

    azure login -u myUserName@contoso.onmicrosoft.com

Luego, se le solicitará escribir la contraseña:

    info:    Executing command login
    Password: *********

El proceso de inicio de sesión se completará.

    info:    Added subscription Visual Studio Ultimate with MSDN
    +
    info:    login command OK

Si se trata de la primera vez que inicia sesión con estas credenciales, se le pedirá que verifique si desea almacenar en caché un token de autenticación. Este aviso también aparecerá si anteriormente usó el comando `azure logout` (que se describe más adelante en el artículo). Para omitir este aviso en escenarios de automatización, ejecute `azure login` con el parámetro `-q`.

## <a name="scenario-3-azure-login-with-a-service-principal"></a>Escenario 3: Inicio de sesión de Azure con una entidad de servicio
Si crea una entidad de servicio para una aplicación de Active Directory y la entidad de servicio principal tiene permisos sobre la suscripción, puede usar el comando `azure login` para autenticar la entidad de servicio. En función del escenario, podría proporcionar las credenciales de la entidad de servicio como parámetros explícitos del comando `azure login`. Por ejemplo, el comando siguiente pasa el nombre de la entidad de servicio y el identificador de inquilino de Active Directory:

    azure login -u https://www.contoso.org/example --service-principal --tenant myTenantID

Luego, se le solicitará proporcionar la contraseña. También puede proporcionar las credenciales mediante un script de la CLI o un código de aplicación, o bien usar un certificado para autenticar la entidad de servicio de manera no interactiva para los escenarios de automatización. Para ver información más detallada y ejemplos, consulte [Autenticación de una entidad de servicio con Azure Resource Manager](resource-group-authenticate-service-principal-cli.md).

## <a name="scenario-4-use-a-publish-settings-file"></a>Escenario 4: Uso de un archivo de configuración de publicación
Si solo necesita usar los comandos de la CLI del modo de administración de servicios de Azure (por ejemplo, para implementar máquinas virtuales de Azure en el modelo de implementación clásico), puede conectarse mediante un archivo de configuración de publicación. Este método instala un certificado en el equipo local que le permite realizar tareas de administración siempre que la suscripción y el certificado sean válidos.

* **Para descargar el archivo de configuración de publicación** de la cuenta, escriba `azure config mode asm` para asegurarse de que la CLI tiene el modo Administración de servicios. Luego, ejecute el siguiente comando:

        azure account download

Esto abrirá el explorador predeterminado y le solicitará iniciar sesión en el [Portal de Azure clásico](https://manage.windowsazure.com). Después de iniciar sesión, se descarga un archivo `.publishsettings` . Tome nota de dónde se guarda este archivo.

> [!NOTE]
> Si su cuenta está asociada a varios inquilinos de Azure Active Directory, puede que se le pida que seleccione para qué Active Directory desea descargar el archivo de configuración de publicación.
>
>

Una vez seleccionado usando la página de descarga, o visitando el Portal de Azure clásico, el Active Directory seleccionado se convierte en el predeterminado que usan tanto el portal clásico como la página de descarga. Una vez que se haya restablecido un valor predeterminado, verá el texto "**haga clic aquí para volver a la página de selección**" en la parte superior de la página de descarga. Utilice el vínculo proporcionado para volver a la página de selección.

* **Para importar el archivo de configuración de publicación**, ejecute el comando siguiente:

        azure account import <path to your .publishsettings file>

> [!IMPORTANT]
> Tras importar la configuración de publicación, debe eliminar el archivo `.publishsettings`. La CLI de Azure ya no lo necesita y supone un riesgo para la seguridad, puesto que se puede usar para obtener acceso a su suscripción.
>
>

## <a name="cli-command-modes"></a>Modos de comando de CLI
La CLI de Azure proporciona dos modos de comando para trabajar con recursos de Azure, con conjuntos de comandos distintos:

* **Modo de Resource Manager** : para trabajar con recursos de Azure en el modelo de implementación de Resource Manager. Para establecer este modo, ejecute `azure config mode arm`.
* **Modo de administración de servicios** : para trabajar con recursos de Azure en el modelo de implementación clásico. Para establecer este modo, ejecute `azure config mode asm`.

Cuando se instala por primera vez, la versión actual de la CLI está en el modo Resource Manager.

> [!NOTE]
> Los modos Resource Manager y Administración de servicios se excluyen mutuamente. Es decir, los recursos creados en un modo no se pueden administrar desde el otro.
>
>

## <a name="multiple-subscriptions"></a>Varias suscripciones
Si tiene varias suscripciones de Azure, la conexión a Azure le concede acceso a todas las suscripciones asociadas a sus credenciales. Se selecciona una suscripción como predeterminada y la CLI de Azure la utilizará al realizar operaciones. Puede ver las suscripciones, incluida la suscripción predeterminada actual, con el comando `azure account list`. Este comando devuelve información similar a la siguiente:

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
    data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

En la lista anterior, la columna **Actual** indica la suscripción predeterminada actual como Azure-sub-1. Para cambiar la suscripción predeterminada, utilice el comando `azure account set` y especifique qué suscripción quiere que sea la predeterminada. Por ejemplo:

    azure account set Azure-sub-2

Esto cambia la suscripción predeterminada a Azure-sub-2.

> [!NOTE]
> El cambio de la suscripción predeterminada surte efecto de forma inmediata y es global; los nuevos comandos de la CLI de Azure, ya los ejecute desde la misma instancia de línea de comandos o desde una instancia diferente, usan la nueva suscripción predeterminada.
>
>

Si desea usar una suscripción no predeterminada con la CLI de Azure, pero no quiere cambiar la predeterminada actualmente, puede usar la opción `--subscription` para el comando y proporcionar el nombre de la suscripción que desee usar para la operación.

Una vez que esté conectado a su suscripción de Azure, puede empezar a usar los comandos de la CLI de Azure para trabajar con recursos de Azure.

## <a name="storage-of-cli-settings"></a>Almacenamiento de la configuración de la CLI
Independientemente de que inicie sesión con el comando `azure login` o importe la configuración de publicación, el perfil de CLI y los registros se almacenan en un directorio `.azure` ubicado en su directorio `user`. El directorio `user` está protegido por el sistema operativo. Sin embargo, se recomienda que tome medidas adicionales para cifrar el directorio `user`. Puede hacerlo de las siguientes maneras:

* En Windows, modifique las propiedades del directorio o use BitLocker.
* En Mac, active FileVault para el directorio.
* En Ubuntu, use la característica del directorio de inicio (home) cifrado. Otras distribuciones de Linux ofrecen características similares.

## <a name="logging-out"></a>Cerrar sesión
Para cerrar sesión, utilice el comando siguiente:

    azure logout -u <username>

Si las suscripciones asociadas a la cuenta solo se autentican con Active Directory, al cerrar sesión se elimina la información de la suscripción del perfil local. Sin embargo, si también se importó un archivo de configuración de publicación para las suscripciones, al cerrar sesión se elimina solo la información relacionada con Active Directory del perfil local.

## <a name="next-steps"></a>Pasos siguientes
* Para usar los comandos de la CLI de Azure, consulte [Comandos de la CLI de Azure en el modo de Resource Manager](virtual-machines/azure-cli-arm-commands.md) y [Comandos CLI de Azure en modo de Administración de servicios de Azure (asm)](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* Si desea obtener más información acerca de la CLI de Azure, descargar el código fuente, informar sobre problemas o colaborar con el proyecto, visite el [Repositorio de GitHub para la CLI de Azure](https://github.com/azure/azure-xplat-cli).
* Si tiene problemas al usar la CLI de Azure o Azure, visite los [Foros de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).

