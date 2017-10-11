---
title: "Migración de la plataforma de Azure Security Center | Microsoft Docs"
description: En este documento se explican algunos en la forma en que se recopilan los datos de Azure Security Center.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 80246b00-bdb8-4bbc-af54-06b7d12acf58
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: yurid
ms.openlocfilehash: 5ddf71dcd9c5a2b03e3b1441d8c9b4d91b6bad12
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-security-center-platform-migration"></a>Migración de la plataforma de Azure Security Center

A partir de principios de junio de 2017, Azure Security Center implementa cambios importantes en la forma en que se recopilan y almacenan los datos de seguridad.  Estos cambios descubren nuevas funcionalidades, como la posibilidad de buscar fácilmente datos de seguridad y mejores alineaciones con otros servicios de administración y supervisión de Azure.

> [!NOTE]
> La migración de la plataforma no debería afectar a los recursos de producción y no es necesaria ninguna intervención del usuario.


## <a name="whats-happening-during-this-platform-migration"></a>¿Qué sucede durante esta migración de la plataforma?

Anteriormente, Security Center usaba Azure Monitoring Agent para recopilar datos de seguridad de las máquinas virtuales. Por ejemplo, información sobre las configuraciones de seguridad, que se usan para identificar las vulnerabilidades, y eventos de seguridad, que se usan para detectar amenazas. Estos datos se almacenan en sus cuentas de Storage de Azure.

En adelante, Security Center usará Microsoft Monitoring Agent, que es el mismo agente que usan la solución Operations Management Suite y el servicio Log Analytics. Los datos recopilados por este agente se almacenan en *áreas de trabajo* existentes de [Log Analytics](../log-analytics/log-analytics-manage-access.md) asociadas con la suscripción de Azure o en unas nuevas áreas de trabajo, según la geolocalización de la máquina virtual.

## <a name="agent"></a>Agente

Como parte de la transición, Microsoft Monitoring Agent (para [Windows](../log-analytics/log-analytics-windows-agents.md) o [Linux](../log-analytics/log-analytics-linux-agents.md)) se instala en todas las máquinas virtuales de Azure de las que se están recopilando datos en ese momento.  Si la máquina virtual ya tiene instalado Microsoft Monitoring Agent, Security Center aprovechará al agente instalado actual.

Durante un período de tiempo (normalmente unos días), ambos agentes se ejecutarán en paralelo para garantizar una transición fluida sin pérdida de datos. De esta forma, Microsoft podrá validar que la nueva canalización de datos está operativa antes de dejar de usar la canalización actual. Una vez comprobado, Azure Monitoring Agent se quitará de las máquinas virtuales. No es necesario que el usuario intervenga de ninguna forma. Cuando todos los clientes se hayan migrado, recibirá una notificación por correo electrónico.
 
No se recomienda desinstalar manualmente Azure Monitoring Agent durante la migración, ya podrían producirse lagunas en los datos de seguridad. Si necesita ayuda adicional, consulte [servicio de Soporte técnico y Atención al cliente de Microsoft](https://support.microsoft.com/contactus/). 

Microsoft Monitoring Agent para Windows requiere el uso del puerto TCP 443. Para más información, lea la [Guía de solución de problemas de Azure Security Center](security-center-troubleshooting-guide.md).


> [!NOTE] 
> Como Microsoft Monitoring Agent se puede usar en otros servicios de administración y supervisión de Azure, el agente no se desinstalará automáticamente cuando desactive la recopilación de datos en Security Center. Sin embargo, puede desinstalar al agente manualmente si es necesario.

## <a name="workspace"></a>Área de trabajo

Como se describió anteriormente, los datos recopilados por Microsoft Monitoring Agent (en nombre de Security Center) se almacenan en las áreas de trabajo existentes de Log Analytics asociadas con la suscripción de Azure o en unas nuevas áreas de trabajo, según la geolocalización de la máquina virtual.

En el portal de Azure, puede realizar una exploración para ver una lista de las áreas de trabajo de Log Analytics, incluidas las creadas por Azure Security Center. Se creará un grupo de recursos relacionado para las nuevas áreas de trabajo. Ambas siguen esta convención de nomenclatura:

- Área de trabajo: *DefaultWorkspace-[subscription-ID]-[geo]*
- Grupo de recursos: *DefaultResouceGroup-[geo]* 
 
En el caso de las áreas de trabajo creadas por Security Center, los datos se conservan durante 30 días. En las áreas de trabajo existentes, la retención se basa en el plan de tarifa del área de trabajo.

> [!NOTE]
> Los datos recopilados previamente por Security Center permanecen en sus cuentas de Storage. Una vez completada la migración, puede eliminar estas cuentas.

### <a name="oms-security-solution"></a>Solución de seguridad de OMS 

Los clientes existentes que no tengan instalada la solución de seguridad de OMS están de suerte. Microsoft la instalará en sus áreas de trabajo, pero irá dirigida únicamente a las máquinas virtuales de Azure. No desinstale esta solución, ya que no existe ninguna solución automática si lo hace desde la consola de administración de OMS.


## <a name="other-updates"></a>Otras actualizaciones

Junto con la migración de la plataforma, estamos implementando algunas otras actualizaciones de menor importancia:

- Se admitirán otras versiones de sistema operativo. Vea la lista [aquí](security-center-faq.md#virtual-machines).
- Se ampliará la lista de vulnerabilidades del sistema operativo. Vea la lista [aquí](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Los [precios](https://azure.microsoft.com/pricing/details/security-center/) se prorratearán por hora (antes era diariamente), lo que se traducirá en un ahorro de costos para los clientes.
- La recopilación de datos se exigirá y se habilitará automáticamente para los clientes del plan de tarifa Estándar.
- Azure Security Center comenzará a detectar soluciones antimalware que no se implementaron mediante extensiones de Azure. En primer lugar estará disponible la detección de Symantec Endpoint Protection y Defender para Windows 2016.
- Las directivas de prevención y las notificaciones solo se pueden configurar en el nivel de *Suscripción*. Sin embargo, aún se pueden establecer los precios en el nivel *Grupo de recursos*.

