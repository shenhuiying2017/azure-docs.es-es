---
title: 'Supervisar la comunicación de red (tutorial): Azure Portal | Microsoft Docs'
description: Aprenda a supervisar la comunicación de red entre dos máquinas virtuales con la funcionalidad de supervisión de conexiones de Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: bfd9552a0d7c3b1e631fcc1a25d240608754c6a3
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-monitor-network-communication-between-two-virtual-machines-using-the-azure-portal"></a>Tutorial: supervisar la comunicación de red entre dos máquinas virtuales mediante Azure Portal

La comunicación correcta entre una máquina virtual (VM) y un punto de conexión, como otra máquina virtual, puede ser crítica para una organización. En ocasiones, se introducen cambios de configuración que pueden interrumpir la comunicación. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear dos máquinas virtuales.
> * Supervisar la comunicación entre VM con la funcionalidad de supervisión de conexiones de Network Watcher.
> * Diagnosticar un problema de comunicación entre dos VM y aprender a resolverlo

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en el [Azure Portal](https://portal.azure.com).

## <a name="create-vms"></a>Creación de máquinas virtuales

Cree dos VM.

### <a name="create-the-first-vm"></a>Creación de la primera máquina virtual

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Compute** y, a continuación, seleccione un sistema operativo. En este tutorial usaremos **Windows Server 2016 Datacenter**.
3. Escriba o seleccione la siguiente información, acepte los valores predeterminados para el resto de la configuración y luego seleccione **Aceptar**:

    |Configuración|Valor|
    |---|---|
    |NOMBRE|myVm1|
    |Nombre de usuario| Escriba un nombre de usuario de su elección.|
    |Password| Escriba una contraseña de su elección. La contraseña debe tener al menos 12 caracteres de largo y cumplir con los [requisitos de complejidad definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |La suscripción| Seleccione su suscripción.|
    |Grupos de recursos| Haga clic en **Crear nuevo** y escriba **myResourceGroup**.|
    |Ubicación| Seleccione **Este de EE. UU**.|

4. Seleccione un tamaño para la máquina virtual y luego **Seleccionar**.
5. En **Configuración**, seleccione **Extensiones**. Seleccione **Agregar extensión** y **Network Watcher Agent for Windows** (Agente de Network Watcher para Windows), tal y como se muestra en la siguiente imagen:

    ![Extensión del agente de Network Watcher](./media/connection-monitor/nw-agent-extension.png)

6. En **Network Watcher Agent for Windows** (Agente de Network Watcher para Windows), seleccione **Crear**; en **Instalar extensión** seleccione **Aceptar** y, a continuación, en **Extensiones**, seleccione **Aceptar**.
7. Acepte los valores predeterminados de los **valores de configuración** restantes y seleccione **Aceptar** .
8. En **Crear** de la página **Resumen**, seleccione **Crear** para iniciar la implementación de la máquina virtual.

### <a name="create-the-second-vm"></a>Creación de la segunda máquina virtual

Complete los pasos de [Creación de la primera máquina virtual](#create-the-first-vm) nuevamente, con los siguientes cambios:

|Paso|Configuración|Valor|
|---|---|---|
| 1 | Seleccione la **VM Ubuntu Server 17.10**. |                                                                         |
| 3 | NOMBRE                              | myVm2                                                                   |
| 3 | Tipo de autenticación               | Pegue la clave pública SSH o seleccione **Contraseña** y escriba una contraseña. |
| 3 | Grupos de recursos                    | Seleccione **Usar existente** y, a continuación, **myResourceGroup**.                 |
| 6 | Extensiones                        | **Agente de red para Linux**                                             |

La maquina virtual tarda unos minutos en implementarse. Espere a que la VM finalice la implementación antes de continuar con los pasos restantes.

## <a name="create-a-connection-monitor"></a>Creación de un monitor de conexión

Cree un monitor de conexión para supervisar la comunicación a través del puerto TCP 22 desde *myVm1* a *myVm2*.

1. En el lado izquierdo del portal, seleccione **Todos los servicios**.
2. Empiece a escribir *network watcher* en el cuadro **Filtro**. Cuando aparezca la opción **Network Watcher** en los resultados de búsqueda, selecciónela.
3. En **SUPERVISIÓN**, seleccione **Monitor de conexión**.
4. Seleccione **+Agregar**.
5. Escriba o seleccione la información para la conexión que desea supervisar y seleccione **Agregar**. En el ejemplo que se muestra en la imagen siguiente, la conexión supervisada va de la VM *myVm1* a *myVm2* a través del puerto 22:

    | Configuración                  | Valor               |
    | ---------                | ---------           |
    | NOMBRE                     | myVm1-myVm2(22)     |
    | Origen                   |                     |
    | Máquina virtual          | myVm1               |
    | Destino              |                     |
    | Seleccionar una máquina virtual |                     |
    | Máquina virtual          | myVm2               |
    | Port                     | 22                  |

    ![Adición de un monitor de conexión](./media/connection-monitor/add-connection-monitor.png)

## <a name="view-a-connection-monitor"></a>Ver un monitor de conexión

1. Complete los pasos del 1 al 3 de [Creación de un monitor de conexión](#create-a-connection-monitor) para ver la supervisión de la conexión. Verá una lista de monitores de conexión existentes, tal como se muestra en la siguiente imagen:

    ![Monitores de conexión](./media/connection-monitor/connection-monitors.png)

2. Seleccione el monitor con el nombre **myVm1-myVm2(22)**, tal y como se muestra en la imagen anterior, para ver los detalles del monitor que se muestran en la siguiente imagen:

    ![Detalles del monitor](./media/connection-monitor/vm-monitor.png)

    Tenga en cuenta la información siguiente:

    | item                     | Valor                      | Detalles                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Status                   | Accesible                  | Le permite saber si el punto de conexión es accesible o no.|
    | AVG. ROUND-TRIP          | Le permite conocer el tiempo de ida y vuelta para hacer la conexión, en milisegundos. El monitor de conexión sondea la conexión cada 60 segundos, por lo que puede controlar la latencia a lo largo del tiempo.                                         |
    | Hops                     | El monitor de conexión le permite conocer los saltos entre los dos puntos de conexión. En este ejemplo, la conexión se realiza entre dos VM de la misma red virtual, por lo que solo hay un salto, a la dirección IP 10.0.0.5. Si algún sistema existente o ruta personalizada redirige el tráfico entre las VM a través de una puerta de enlace de VPN o una aplicación virtual de red, por ejemplo, se enumeran saltos adicionales.                                                                                                                         |
    | STATUS                   | Las marcas de verificación verdes de cada punto de conexión le indican que el estado de cada punto de conexión es correcto.    ||

## <a name="view-a-problem"></a>Ver un problema

De forma predeterminada, Azure permite la comunicación de todos los puertos entre VM de la misma red virtual. Es posible que, con el tiempo, usted o alguien de su organización anule las reglas predeterminadas de Azure, provocando, involuntariamente, un error de comunicación. Complete los pasos siguientes para crear un problema de comunicación y, a continuación, consulte de nuevo el monitor de conexión:

1. Escriba *myResourceGroup* en el cuadro de búsqueda que se encuentra en la parte superior del portal. Seleccione el grupo de recursos **myResourceGroup** cuando aparezca en los resultados de búsqueda.
2. Seleccione el grupo de seguridad de red **myVm2-nsg**.
3. Seleccione **Reglas de seguridad de entrada** y, a continuación, seleccione **Agregar**, tal como se muestra en la imagen siguiente:

    ![Reglas de seguridad de entrada](./media/connection-monitor/inbound-security-rules.png)

4. La regla predeterminada que permite la comunicación entre todas las VM de una red virtual es la regla denominada **AllowVnetInBound**. Cree una regla con una prioridad más alta (con un número menor) a la de la regla **AllowVnetInBound** que deniegue la comunicación entrante en el puerto 22. Escriba o seleccione la siguiente información, acepte los valores predeterminados restantes y haga clic en **Agregar**:

    | Configuración                 | Valor          |
    | ---                     | ---            |
    | Intervalos de puertos de destino | 22             |
    | .                  | Denegar           |
    | Prioridad                | 100            |
    | NOMBRE                    | DenySshInbound |

5. Puesto que el monitor de conexión realiza sondeos a intervalos de 60 segundos, espere unos minutos y, a continuación, en el lado izquierdo del portal, seleccione **Network Watcher**; a continuación, seleccione **Monitor de conexión** y el monitor **myVm1-myVm2(22)** de nuevo. Ahora, los resultados son diferentes, tal como se muestra en la siguiente imagen:

    ![Monitor details fault](./media/connection-monitor/vm-monitor-fault .png)

    Puede ver que hay un icono de signo de exclamación rojo en la columna Estado de la interfaz de red **myvm2529**.

6. Para saber por qué ha cambiado el estado, seleccione 10.0.0.5 en la imagen anterior. El monitor de conexión le informará que el motivo del error de comunicación es el siguiente: *Tráfico bloqueado debido a la siguiente regla del grupo de seguridad de red: UserRule_DenySshInbound*.

    Si no sabía que alguien había implementado la regla de seguridad que creó en el paso 4, ahora sabrá gracias al monitor de conexión que esta regla es la que provoca el problema de comunicación. A continuación, puede cambiar, invalidar o quitar la regla para restablecer la comunicación entre las VM.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sea necesario, elimine el grupo de recursos y todos los recursos que contiene:

1. Escriba *myResourceGroup* en el cuadro **Buscar** que se encuentra en la parte superior del portal. Seleccione **myResourceGroup** cuando lo vea en los resultados de la búsqueda.
2. Seleccione **Eliminar grupo de recursos**.
3. Escriba *myResourceGroup* para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a supervisar una conexión entre dos VM. Aprendió que una regla del grupo de seguridad de red era la que impedía la comunicación con una VM. Para obtener información acerca de las diferentes respuestas que puede devolver el monitor de conexión, consulte los [tipos de respuesta](network-watcher-connectivity-overview.md#response). También puede supervisar una conexión entre una VM, un nombre de dominio completo, un identificador de recursos uniforme o una dirección IP.

Es posible que, en algún momento, vea que los recursos de una red virtual no se pueden comunicar con los recursos de otras redes conectadas mediante una puerta de enlace de red virtual de Azure. Vaya al siguiente tutorial para aprender a diagnosticar un problema con una puerta de enlace de red virtual.

> [!div class="nextstepaction"]
> [Diagnosticar problemas de comunicación entre redes](diagnose-communication-problem-between-networks.md)