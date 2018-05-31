---
title: Administración de Protección contra DDoS de Azure estándar mediante Azure Portal | Microsoft Docs
description: Aprenda a usar la telemetría de Protección contra DDoS de Azure estándar en Azure Monitor para mitigar un ataque.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: dd094f2b9cdb9b5eb164dda2925d094cafa7cd89
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895619"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Administración de Protección contra DDoS de Azure estándar mediante Azure Portal

Obtenga información acerca de cómo habilitar y deshabilitar la protección contra denegación de servicio (DDoS) distribuido y usar la telemetría para mitigar un ataque DDoS con Protección contra DDoS de Azure estándar. Protección contra DDoS de Azure estándar protege los recursos de Azure, como las máquinas virtuales, los equilibradores de carga y las puertas de enlace de aplicaciones que disponen de una [dirección IP pública](virtual-network-public-ip-address.md) de Azure asignada. Para más información sobre el estándar de protección contra DDoS y sus funcionalidades, vea [Introducción a Protección contra DDoS de Azure estándar](ddos-protection-overview.md).

Antes de completar los pasos de este tutorial, inicie sesión en Azure Portal en https://portal.azure.com con una cuenta asignada al rol de [colaborador de red](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un[ rol personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenga asignadas las acciones apropiadas en [Permisos](#permissions).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="create-a-ddos-protection-plan"></a>Creación de un plan de protección contra DDoS

Un plan de protección contra DDoS define un conjunto de redes virtuales que tiene habilitada la protección contra DDoS estándar en distintas suscripciones. Puede configurar un plan de protección contra DDoS para la organización y vincular redes virtuales de distintas suscripciones al mismo plan. El propio plan de protección contra DDoS también está asociado a una suscripción, la que selecciona durante la creación del plan. La suscripción a la que está asociado el plan incurre en la factura mensual recurrente del plan, además de cargos de uso por encima del límite, en caso de que la cantidad de direcciones IP públicas protegidas supere las 100. Para más información sobre los precios de DDoS, consulte los [detalles de precios](https://azure.microsoft.com/pricing/details/ddos-protection/).

En la mayoría de las organizaciones, no es necesario crear más de un plan. Un plan no puede moverse entre suscripciones. Si desea cambiar la suscripción en que se encuentra un plan, debe [eliminar el plan existente](#work-with-ddos-protection-plans) y crear uno nuevo.

1. Seleccione **Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Busque *DDoS*. Seleccione **Plan de protección contra DDoS** cuando aparezca en los resultados de la búsqueda.
3. Seleccione **Crear**.
4. Escriba o seleccione sus propios valores, o bien escriba o seleccione los valores de ejemplo siguientes y, luego, seleccione **Crear**:

    |Configuración        |Valor                                              |
    |---------      |---------                                          |
    |NOMBRE           | myDdosProtectionPlan                              |
    |La suscripción   | Seleccione su suscripción.                         |
    |Grupos de recursos | Seleccione **Crear nuevo** y escriba *myResourceGroup* |
    |Ubicación       | Este de EE. UU                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Habilitación de DDoS para una red virtual nueva

1. Seleccione **Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y **Red virtual**.
3. Escriba o seleccione sus propios valores, o bien escriba o seleccione los valores de ejemplo siguientes, acepte el resto de los valores predeterminados y, luego, seleccione **Crear**:

    | Configuración         | Valor                                                        |
    | ---------       | ---------                                                    |
    | NOMBRE            | myVirtualNetwork                                             |
    | La suscripción    | Seleccione su suscripción.                                    |
    | Grupos de recursos  | Seleccione **Usar existente** y, luego, seleccione **myResourceGroup** |
    | Ubicación        | Este de EE. UU                                                      |
    | Protección contra DDOS | Seleccione **Estándar** y, luego, en **Protección contra DDoS**, seleccione **myDdosProtectionPlan**. El plan que selecciona puede estar en la misma suscripción que la red virtual, o una suscripción distinta, pero ambas suscripciones deben estar asociadas al mismo inquilino de Azure Active Directory.|

No puede mover una red virtual a otro grupo de recursos ni a otra suscripción si DDoS Standard está habilitado para la red virtual. Si tiene que mover una red virtual que tenga habilitado DDoS Standard, primero deshabilítelo, mueva la red virtual y, luego, habilite DDoS Standard. Después de eso, se restablecen los umbrales de directiva ajustados automáticamente para todas las direcciones IP públicas protegidas en la red virtual.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Habilitación de DDoS para una red virtual existente

1. Cree un plan de protección contra DDoS con los pasos descritos en [Creación de un plan de protección contra DDoS](#create-a-ddos-protection-plan), si no tiene un plan de protección de DDoS existente.
2. Seleccione **Crear un recurso** en la esquina superior izquierda de Azure Portal.
3. Escriba el nombre de la red virtual para la que desea habilitar DDoS Protection Standard en el cuadro **Buscar recursos, servicios y documentos** en la parte superior del portal. Seleccione el nombre de la red virtual cuando aparezca en los resultados de la búsqueda.
4. Seleccione **Protección DDoS** en **CONFIGURACIÓN**.
5. Seleccione **Estándar**. En **Plan de protección contra DDoS**, seleccione un plan de protección contra DDoS existente o el plan que creó en el paso 1 y, luego, seleccione **Guardar**. El plan que selecciona puede estar en la misma suscripción que la red virtual, o una suscripción distinta, pero ambas suscripciones deben estar asociadas al mismo inquilino de Azure Active Directory.

## <a name="disable-ddos-for-a-virtual-network"></a>Deshabilitación de DDoS para una red virtual

1. Escriba el nombre de la red virtual para la que desea deshabilitar DDoS Protection Standard en el cuadro **Buscar recursos, servicios y documentos** en la parte superior del portal. Seleccione el nombre de la red virtual cuando aparezca en los resultados de la búsqueda.
2. Seleccione **Protección DDoS** en **CONFIGURACIÓN**.
3. Seleccione **Básico** en **Plan de protección contra DDoS** y, luego, seleccione **Guardar**.

## <a name="work-with-ddos-protection-plans"></a>Trabajo con planes de protección contra DDoS

1. Seleccione **Todos los servicios** en la parte superior izquierda del portal.
2. Escriba *DDoS* en el cuadro **Filtrar**. Seleccione **Planes de protección contra DDoS** cuando aparezca en los resultados.
3. Seleccione el plan de protección que desea ver en la lista.
4. Aparecen todas las redes virtuales asociadas al plan.
5. Si desea eliminar un plan, primero debe desasociar todas las redes virtuales que tiene asociadas. Para desasociar un plan de una red virtual, consulte [Deshabilitación de DDoS para una red virtual](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Configuración de alertas para métricas de protección contra DDoS

Puede seleccionar cualquiera de las métricas de protección contra DDoS disponibles para que le avisen cuando hay una mitigación activa durante un ataque mediante la configuración de alertas de Azure Monitor. Cuando se cumplan las condiciones, recibirá un correo electrónico de alerta en la dirección especificada:

1. Seleccione **Todos los servicios** en la parte superior izquierda del portal.
2. Escriba *Monitor* en el cuadro **Filtrar**. Seleccione **Monitor** cuando aparezca en los resultados.
3. Seleccione **Métricas** en **SERVICIOS COMPARTIDOS**.
4. Escriba o seleccione sus propios valores, o bien escriba los valores de ejemplo siguientes, acepte el resto de los valores predeterminados y, luego, seleccione **Aceptar**:

    |Configuración                  |Valor                                                                                               |
    |---------                |---------                                                                                           |
    |NOMBRE                     | myDdosAlert                                                                                        |
    |La suscripción             | Seleccione la suscripción que contiene la dirección IP pública para la que desea recibir alertas.        |
    |Grupos de recursos           | Seleccione el grupo de recursos que contiene la dirección IP pública para la que desea recibir alertas.      |
    |Recurso                 | Seleccione la dirección IP pública que contiene la dirección IP pública para la que desea recibir alertas. DDoS supervisa las direcciones IP públicas asignadas a los recursos dentro de una red virtual. Si no tiene ningún recurso con direcciones IP públicas en la red virtual, primero debe crear un recurso con una dirección IP pública. Puede supervisar la dirección IP pública de todos los recursos implementados a través de Resource Manager (no clásico) que aparecen en [Red virtual para servicios de Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), excepto para entornos de Azure App Service y Azure VPN Gateway. Para continuar con este tutorial, puede crear rápidamente una máquina virtual [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).                   |
    |Métrica                   | Bajo ataque de DDoS o no                                                                            |
    |Umbral                | 1: un **1** significa que está siendo atacado. Un **0** significa que no está siendo atacado.                         |
    |Período                   | Seleccione el valor que prefiera.                                                                   |
    |Notificar por correo electrónico         | Active la casilla de verificación                                                                                  |
    |Administrador adicional | Escriba su dirección de correo electrónico si no es propietario del correo electrónico, colaborador o lector de la suscripción. |

    Dentro de unos pocos minutos después de la detección del ataque, recibirá un correo electrónico con métricas de Azure Monitor que tiene un aspecto similares a la imagen siguiente:

    ![Alerta de ataque](./media/manage-ddos-protection/ddos-alert.png)


Para simular un ataque de DDoS para validar la alerta, consulte [Validación de la detección de DDoS](#validate-ddos-detection).

Puede aprender más sobre [configurar webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y [aplicaciones lógicas](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para la creación de alertas.

## <a name="configure-logging-for-ddos-protection-metrics"></a>Configuración del registro de métricas de protección contra DDoS

1. Seleccione **Todos los servicios** en la parte superior izquierda del portal.
2. Escriba *Monitor* en el cuadro **Filtrar**. Seleccione **Monitor** cuando aparezca en los resultados.
3. En **CONFIGURACIÓN**, seleccione **Configuración de diagnóstico**.
4. Seleccione la **suscripción** y el **grupo de recursos** que contienen la dirección IP pública que desea registrar.
5. Seleccione la **dirección IP pública** para el **tipo de recurso**, luego seleccione la dirección IP pública específica para la que desea registrar las métricas.
6. Seleccione **Turn on diagnostics to collect the following data** (Activar diagnósticos para recopilar los siguientes datos) y luego seleccione todas las opciones siguientes que necesite:

    - **Archivar en cuenta de almacenamiento**: los datos se escriben en una cuenta de Azure Storage. Para más información sobre esta opción, consulte [Archivo de registros de diagnósticos](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Transmitir a un centro de eventos**: permite que un receptor de registros seleccione los registros mediante una instancia de Azure Event Hub. Los centros de eventos habilitan la integración con Splunk y otros sistemas SIEM. Para más información sobre esta opción, consulte [Transmisión de registros de diagnóstico a un centro de eventos](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Enviar a Log Analytics**: escribe registros en el servicio Log Analytics de OMS de Azure. Para más información sobre esta opción, consulte [Recopilación de registros y métricas de Azure para servicios de Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Para simular un ataque de DDoS para validar el registro, consulte [Validación de la detección de DDoS](#validate-ddos-detection).

## <a name="use-ddos-protection-telemetry"></a>Usar telemetría de Protección contra DDoS

La telemetría para un ataque se proporciona a través de Azure Monitor en tiempo real. La telemetría está disponible solo durante el tiempo en que una dirección IP pública está en proceso de mitigación. No ve ninguna telemetría antes o después de mitigar un ataque.

1. Seleccione **Todos los servicios** en la parte superior izquierda del portal.
2. Escriba *Monitor* en el cuadro **Filtrar**. Seleccione **Monitor** cuando aparezca en los resultados.
3. Seleccione **Métricas** en **SERVICIOS COMPARTIDOS**.
4. Seleccione la **suscripción** y el **grupo de recursos** que contienen la dirección IP pública para la que desea la telemetría.
5. Seleccione la **dirección IP pública** para el **tipo de recurso**, luego seleccione la dirección IP pública específica para la que desea la telemetría.
6. En el lado izquierdo de la pantalla se muestra una serie de **métricas disponibles**. Cuando se seleccionan estas métricas, se representan en el **gráfico de métricas de Azure Monitor**, en la pantalla de información general.

Los nombres de las métricas presentan distintos tipos de paquetes y bytes frente a paquetes, con una construcción básica de nombres de etiqueta en cada métrica, como se muestra aquí:

- **Nombre de etiqueta de quitados** (por ejemplo, **paquetes de entrada quitados por DDoS**): el número de paquetes quitados o limpiados por el sistema de Protección contra DDoS.
- **Nombre de etiqueta de reenviados** (por ejemplo, **paquetes de entrada reenviados por DDoS**): el número de paquetes reenviados por el sistema DDoS a la dirección IP virtual de destino (tráfico que no se filtró).
- **Sin nombre de etiqueta** (por ejemplo, **paquetes de entrada por DDoS**): el número total de paquetes que han llegado al sistema de limpieza (representa la suma de los paquetes quitados y los reenviados).

Para simular un ataque de DDoS para validar la telemetría, consulte [Validación de la detección de DDoS](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Visualización de las directivas de mitigación de DDoS

DDoS Protection Standard aplica tres directivas de mitigación de ajuste automático (TCP SYN, TCP y UDP) a cada dirección IP pública del recurso protegido, en la red virtual que tiene habilitado DDoS. Puede ver los umbrales de directiva si selecciona las métricas **Paquetes entrantes TCP para desencadenar la mitigación de DDoS** y **Paquetes entrantes UDP para desencadenar la mitigación de DDoS** métricas, como se muestra en la siguiente imagen:

![Vista de directivas de mitigación](./media/manage-ddos-protection/view-mitigation-policies.png)

Los umbrales de las directivas se configuran automáticamente con nuestro sistema de generación de perfiles de tráfico de red basado en aprendizaje automático de Azure. Solo cuando se supera el umbral de la directiva, la mitigación de DDoS se produce para la dirección IP que está siendo atacada.

## <a name="validate-ddos-detection"></a>Validación de la detección de DDoS

Microsoft se ha asociado con [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para crear una interfaz en la que se puede generar tráfico destinado a las direcciones IP públicas que tengan habilitado el servicio DDoS Protection con fines de simulación. La simulación de BreakPoint Cloud le permite:

- Comprobar en qué medida Microsoft Azure DDoS Protection protege sus recursos de Azure frente a ataques de DDoS.
- Optimizar el proceso de respuesta a incidentes durante el ataque de DDoS.
- Documentar el cumplimiento normativo de DDoS.
- Enseñar a los equipos de seguridad de red.

## <a name="permissions"></a>Permisos

Para trabajar con planes de protección contra DDoS, su cuenta debe estar asignada al rol de [colaborador de red](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un rol [personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenga asignadas las acciones adecuadas que se muestran en la tabla siguiente:

| .                                            | NOMBRE                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Obtención de un plan de protección contra DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Creación o actualización de un plan de protección contra DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Eliminación de un plan de protección contra DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Unión a un plan de protección contra DDoS              |

Para habilitar la protección contra DDoS para una red virtual, su cuenta también debe estar asignada a las [acciones para redes virtuales](manage-virtual-network.md#permissions) adecuadas.

## <a name="next-steps"></a>Pasos siguientes

- Crear y aplicar una [directiva de Azure](policy-samples.md) para redes virtuales