---
title: "Instalación de Endpoint Protection en Azure Security Center | Microsoft Docs"
description: "En este documento se muestra cómo implementar la recomendación **Instalar Endpoint Protection** de Azure Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: d20b23ae84bea6c7e030d2de992e8db1f04bb2a3
ms.contentlocale: es-es
ms.lasthandoff: 06/17/2017


---
# <a name="install-endpoint-protection-in-azure-security-center"></a>Instalación de Endpoint Protection en Azure Security Center
Azure Security Center recomienda instalar Endpoint Protection en las máquinas virtuales de Azure si aún no se ha habilitado esta solución. Esta recomendación se aplica únicamente a las máquinas virtuales de Windows.

> [!NOTE]
> En esta implementación de ejemplo se utiliza Microsoft Antimalware. Consulte [Integración de asociados en Azure Security Center](security-center-partner-integration.md#what-partners-are-integrated-with-security-center) para ver una lista de asociados integrados con Security Center.  
>
>

## <a name="implement-the-recommendation"></a>Implementación de la recomendación

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo.  Este documento no es una guía paso a paso.
>
>

1. En la hoja **Recomendaciones**, seleccione **Instalar Endpoint Protection**.
   ![Selección de instalar Endpoint Protection][1]
2. Se abre la hoja **Instalar Endpoint Protection** con una lista de máquinas virtuales sin Endpoint Protection. Seleccione en la lista las máquinas virtuales en las que quiere instalar Endpoint Protection y haga clic en **Install on VMs** (Instalar en máquinas virtuales).
   ![Selección de máquinas virtuales en las que instalar Endpoint Protection][2]
3. Se abre la hoja **Seleccionar Endpoint Protection**, que permite seleccionar la solución Endpoint Protection que quiere usar. En este ejemplo, vamos a seleccionar **Microsoft Antimalware**.
   ![Select Endpoint Protection][3]
4. Aparece información adicional sobre la solución Endpoint Protection. Seleccione **Crear**.
   ![Creación de solución antimalware][4]
5. Escriba las opciones de configuración requeridas en la hoja **Agregar extensión** y seleccione **Aceptar**. Para obtener más información acerca de las opciones de configuración, consulte [Configuración predeterminada y personalizada de Antimalware](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

[Microsoft Antimalware](../security/azure-security-antimalware.md) ahora está activo en las máquinas virtuales seleccionadas.

## <a name="see-also"></a>Otras referencias
En este documento, mostramos cómo implementar la recomendación "Instalar Endpoint Protection" de Security Center. Para obtener más información sobre cómo habilitar Microsoft Antimalware en Azure, consulte el siguiente documento:

* [Microsoft Antimalware para Azure Cloud Services y Virtual Machines](../security/azure-security-antimalware.md): aprenda a implementar Microsoft Antimalware.

Para obtener más información sobre Security Center, consulte los siguientes documentos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que le ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : busque las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : encuentre publicaciones de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png

