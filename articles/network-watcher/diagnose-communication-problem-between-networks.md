---
title: 'Diagnosticar un problema de comunicación entre redes (tutorial): Azure Portal | Microsoft Docs'
description: Aprenda a diagnosticar los problemas de comunicación entre una red virtual de Azure conectada a una red local o a otra red virtual, a través de una puerta de enlace de red virtual de Azure, mediante la capacidad de diagnóstico de VPN de Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
Customer intent: I need to determine why resources in a virtual network can't communicate with resources in a different network.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: d89c5a3f2545edd7c02b67fa9d2e2b78937a9791
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779577"
---
# <a name="tutorial-diagnose-a-communication-problem-between-networks-using-the-azure-portal"></a>Tutorial: diagnosticar un problema de comunicación entre redes mediante Azure Portal

Una puerta de enlace de red virtual conecta una red virtual de Azure a otras redes locales o virtuales. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Diagnosticar un problema con una puerta de enlace de red virtual con la funcionalidad de diagnóstico de VPN de Network Watcher.
> * Diagnosticar un problema con una conexión de puerta de enlace.
> * Resolver un problema con una puerta de enlace.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>requisitos previos

Para usar el diagnóstico de VPN, debe tener una puerta de enlace de VPN en ejecución. Si no tiene una puerta de enlace de VPN para realizar diagnósticos, puede implementar una mediante un [script de PowerShell](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Puede ejecutar el script de PowerShell desde:
    - **Un proceso de instalación local de PowerShell**: el script requiere la versión del módulo de AzureRM PowerShell 5.7.0 o posterior. Ejecute `Get-Module -ListAvailable AzureRM` para buscar la versión instalada. Si necesita actualizarla, consulte [Instale Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.
    - **Azure Cloud Shell**: [Azure Cloud Shell](https://shell.azure.com/powershell) tiene la versión más reciente de PowerShell instalada y configurada, e inicia una sesión en Azure.

Este script necesita aproximadamente una hora para crear una puerta de enlace de VPN. En los pasos restantes se supone que la puerta de enlace que está diagnosticando es la que implementó el script. Si, en su lugar, diagnostica su propia puerta de enlace, el resultado puede variar.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en el [Azure Portal](https://portal.azure.com).

## <a name="enable-network-watcher"></a>Habilitación de Network Watcher

Si ya dispone de un monitor de red habilitado en la región Este de EE. UU., vaya al paso [Diagnosticar una puerta de enlace](#diagnose-a-gateway).

1. En el portal, seleccione **Todos los servicios**. En el **cuadro Filtrar**, escriba *Network Watcher*. Cuando aparezca la opción **Network Watcher** en los resultados, selecciónela.
2. Seleccione **Regiones** para expandirla y, a continuación, seleccione **...**  a la derecha de la opción **Este de EE. UU.**, tal y como se muestra en la siguiente imagen:

    ![Habilitación de Network Watcher](./media/diagnose-communication-problem-between-networks/enable-network-watcher.png)

3. Seleccione **Habilitar Network Watcher**.

## <a name="diagnose-a-gateway"></a>Diagnosticar una puerta de enlace

1. En el lado izquierdo del portal, seleccione **Todos los servicios**.
2. Empiece a escribir *network watcher* en el cuadro **Filtro**. Cuando aparezca la opción **Network Watcher** en los resultados de búsqueda, selecciónela.
3. En **HERRAMIENTAS DE DIAGNÓSTICO DE RED**, seleccione **Diagnóstico de VPN**.
4. Seleccione **Cuenta de almacenamiento** y, a continuación, seleccione la cuenta de almacenamiento en la que quiera escribir la información de diagnóstico.
5. En la lista **Cuentas de almacenamiento**, seleccione la cuenta de almacenamiento que vaya a usar. Si no tiene una cuenta de almacenamiento, seleccione **+ Cuenta de almacenamiento**, escriba o seleccione la información necesaria y, a continuación, seleccione **Crear**, para crear una. Si creó una puerta de enlace de VPN con el script de [requisitos previos](#prerequisites), tal vez quiera crear la cuenta de almacenamiento en el mismo grupo de recursos *TestRG1*, donde se encuentra la puerta de enlace.
6. En la lista **Contenedores**, seleccione el contenedor que quiera utilizar y, a continuación, haga clic en **Seleccionar**. Si no tiene ningún contenedor, seleccione **+ Contenedor**, escriba un nombre para el contenedor y, a continuación, seleccione **Aceptar**.
7. Seleccione una puerta de enlace y, a continuación, **Comenzar la solución de problemas**. Tal como se muestra en la siguiente imagen, la prueba se ejecuta en una puerta de enlace denominada **Vnet1GW**:

    ![Diagnósticos de VPN](./media/diagnose-communication-problem-between-networks/vpn-diagnostics.png)

8. Mientras se ejecuta la prueba, la opción **Ejecutando** aparece en la columna **ESTADO DE LA SOLUCIÓN DE PROBLEMAS**, donde se muestra el valor **Sin iniciar** en la imagen anterior. Esta prueba puede tardar varios minutos en ejecutarse.
9. Compruebe el estado de una prueba completa. En la siguiente imagen se muestran los resultados de estado de una prueba de diagnóstico completada:

    ![Status](./media/diagnose-communication-problem-between-networks/status.png)

    Puede ver que la opción **ESTADO DE LA SOLUCIÓN DE PROBLEMAS** tiene el valor **Incorrecto**, así como un **Resumen** y los **Detalles** del problema en la pestaña **Estado**.
10. Cuando seleccione la pestaña **Acción**, el proceso de diagnóstico de VPN proporciona información adicional. En el ejemplo que se muestra en la siguiente imagen, el proceso de diagnóstico de VPN le indicará que debe comprobar el estado de cada conexión:

  ![.](./media/diagnose-communication-problem-between-networks/action.png)

## <a name="diagnose-a-gateway-connection"></a>Diagnosticar una conexión de puerta de enlace

Una puerta de enlace está conectada a otras redes a través de una conexión de puerta de enlace. Tanto la puerta de enlace como las conexiones de puerta de enlace deben ser correctas para poder realizar comunicación satisfactoria entre una red virtual y una red conectada.

1. Complete de nuevo el paso 7 denominado [Diagnosticar una puerta de enlace](#diagnose-a-gateway) y, esta vez, seleccione una conexión. En el ejemplo siguiente, se prueba una conexión denominada **VNet1toSite1**:

    ![Conexión](./media/diagnose-communication-problem-between-networks/connection.png)

    La prueba se ejecuta durante varios minutos.
2. Una vez completada la prueba de la conexión, recibirá resultados similares a los que se muestran en las siguientes imágenes en las fichas **Estado** y **Acción**:

    ![Estado de la conexión](./media/diagnose-communication-problem-between-networks/connection-status.png)

    ![Acción de conexión](./media/diagnose-communication-problem-between-networks/connection-action.png)

    Diagnósticos de VPN le informa acerca de cuál es el problema en la ficha **Estado** y le proporciona varias sugerencias en la ficha **Acción** sobre lo que pueda estar causando ese problema.

    Si la puerta de enlace que se ha probado era la que implementó el [script](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en [Requisitos previos](#prerequisites), el problema de la ficha **Estado** y los dos primeros elementos de la ficha **Acciones** describen exactamente de qué trata ese problema. El script configura una dirección IP de marcador de posición (23.99.221.164) para el dispositivo de puerta de enlace de VPN local.

    Para resolver el problema, debe asegurarse de que la puerta de enlace de VPN local está [configurada correctamente](../vpn-gateway/vpn-gateway-about-vpn-devices.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), y que ha cambiado la dirección IP que configuró el script para la puerta de enlace de red local, a la dirección pública real de la puerta de enlace de VPN local.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si creó una puerta de enlace de VPN mediante el script de [requisitos previos](#prerequisites) solamente para completar este tutorial y ya no lo necesita, elimine el grupo de recursos y todos los recursos que contenga:

1. Escriba *TestRG1* en el cuadro **Buscar** que se encuentra en la parte superior del portal. Cuando encuentre **TestRG1** en los resultados de búsqueda, selecciónelo.
2. Seleccione **Eliminar grupo de recursos**.
3. Escriba *TestRG1* en la sección **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a diagnosticar un problema con una puerta de enlace de red virtual. Puede registrar la comunicación de red hacia y desde una máquina virtual para que pueda revisar el registro de anomalías. Para ver cómo hacerlo, prosiga con el siguiente tutorial.

> [!div class="nextstepaction"]
> [Registro del tráfico de red hacia una máquina virtual y desde esta](network-watcher-nsg-flow-logging-portal.md)