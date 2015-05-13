<properties services="virtual-machines" title="Setting up xplat-cli for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## Uso de la xplat-cli

Los pasos siguientes le ayudarán a usar la xplat-cli fácilmente con la versión más reciente y la suscripción adecuada. Si necesita instalar la xplat-cli y conectarla a su cuenta en primer lugar, consulte la [Interfaz de línea de comandos de Azure \(xplat-cli\)](xplat-cli.md).

### Paso 1: actualización de la versión de xplat-cli

Para usar la xplat-cli para comandos imperativos con el modo Administración de servicios, debe tener una versión reciente, si es posible. Para comprobar su versión, escriba `azure --version`. Debería ver algo parecido a lo siguiente:

    $ azure --version
    0.8.17 (node: 0.10.25)
    
Si desea actualizar la versión de la xplat-cli, consulte [xplat-cli](https://github.com/Azure/azure-xplat-cli).

### Paso 2: establecimiento de la cuenta y suscripción de Azure

Una vez que haya conectado la xplat-cli a la cuenta que desee utilizar, puede tener más de una suscripción. Si lo hace, debe revisar las suscripciones disponibles para su cuenta, para lo que debe escribir `azure account list` y, a continuación, seleccione la suscripción que desea usar escribiendo `azure account set <subscription id or name> true`, donde _subscription id or name_ es el identificador de la suscripción o el nombre de la suscripción con la que desea trabajar en la sesión actual. Debe ver algo parecido a lo siguiente:

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK
    
> [AZURE.NOTE]Si todavía no tiene una cuenta de Azure, pero dispone de una suscripción a MSDN, puede obtener créditos gratuitos de Azure mediante la activación de sus [ventajas de suscriptor de MSDN aquí](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), o puede usar la cuenta gratuita. Con cualquiera de estas opciones podrá obtener acceso a Azure.

<!--HONumber=52-->
