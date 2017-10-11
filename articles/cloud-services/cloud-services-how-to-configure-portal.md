---
title: "Configuración de un servicio en la nube (portal) | Microsoft Docs"
description: "Aprenda a configurar servicios en la nube en Azure. Aprenda a actualizar la configuración del servicio en la nube y configurar el acceso remoto en instancias de rol. Estos ejemplos usan el Portal de Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 7308f3c0-825e-499d-bfa5-c60f86371921
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: adegeo
ms.openlocfilehash: a7e891d05ffe4cc2b4f68dce072a81499cc6de80
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-configure-cloud-services"></a>Configuración de servicios en la nube
> [!div class="op_single_selector"]
> * [Portal de Azure](cloud-services-how-to-configure-portal.md)
> * [Portal de Azure clásico](cloud-services-how-to-configure.md)
>
>

Puede configurar la mayoría de los ajustes más usados para un servicio en la nube en el Portal de Azure. O bien, si desea actualizar los archivos de configuración directamente, descargue un archivo de configuración de servicio para actualizar y, a continuación, cargue el archivo actualizado y actualice el servicio en la nube con los cambios en la configuración. De cualquier manera, las actualizaciones de la configuración se realizan en todas las instancias de rol.

También puede administrar las instancias de los roles de servicio en la nube o conectarse mediante Escritorio remoto a ellas.

Azure solo puede asegurar un 99,95 % de disponibilidad del servicio durante las actualizaciones de la configuración si tiene al menos dos instancias de rol para cada rol. Esto permite que una máquina virtual procese las solicitudes del cliente mientras la otra se actualiza. Para obtener más información, consulte [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Cambiar un servicio en la nube
Después de abrir el [Portal de Azure](https://portal.azure.com/), vaya al servicio en la nube. Desde aquí puede administrar muchos aspectos de este.

![Página de configuración](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Los vínculos de **Configuración** o **Toda la configuración** se abrirán en la hoja **Configuración** donde podrá cambiar las **propiedades**, cambiar la **configuración**, administrar los **certificados**, instalar las **reglas de alerta**, y administrar los **usuarios** que tienen acceso a este servicio en la nube.

![Hoja de configuración del servicio en la nube de Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Administración de la versión del SO invitado

De forma predeterminada, Azure actualiza periódicamente el sistema operativo invitado a la imagen compatible más reciente dentro de la familia del SO que ha especificado en la configuración del servicio (.cscfg), como Windows Server 2016.

Si tiene como destino una versión de sistema operativo específica, puede establecerla en la hoja **Configuración**.

![Establecimiento de la versión del sistema operativo](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)


>[!IMPORTANT]
> Si elige una versión específica del sistema operativo deshabilitará las actualizaciones automáticas y hará que la aplicación de revisiones sea responsabilidad suya. Debe asegurarse de que las instancias de rol están recibiendo actualizaciones o puede exponer su aplicación a las vulnerabilidades de seguridad.

## <a name="monitoring"></a>Supervisión
Puede agregar alertas a su servicio en la nube. Haga clic en **Configuración** > **Reglas de alerta** > **Agregar alerta**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Desde aquí puede configurar una alerta. Mediante el cuadro desplegable **Mertic** , puede configurar una alerta para los siguientes tipos de datos.

* Lectura de disco
* Escritura de disco
* Red interna
* Red externa
* Porcentaje de CPU

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Configuración de la supervisión desde un icono de métrica
En lugar de usar **Configuración** > **Reglas de alerta**, puede hacer clic en uno de los iconos de métrica en la sección **Supervisión** de la hoja **Servicio en la nube**.

![Supervisión de servicios en la nube](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Desde aquí puede personalizar el gráfico que se usa con el icono o agregar una regla de alerta.

## <a name="reboot-reimage-or-remote-desktop"></a>Reinicio, restablecimiento de imagen inicial o conexión mediante Escritorio remoto
En este momento no se puede configurar el Escritorio remoto con el **Portal de Azure**. Sin embargo, se puede configurar a través del [Portal de Azure clásico](cloud-services-role-enable-remote-desktop.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) o mediante [Visual Studio](../vs-azure-tools-remote-desktop-roles.md).

Primero, haga clic en la instancia de servicio en la nube.

![Instancia del servicio en la nube](./media/cloud-services-how-to-configure-portal/cs-instance.png)

En la hoja que se abre, puede iniciar una conexión de escritorio remoto, reiniciar la instancia de forma remota o restablecer la imagen inicial de forma remota (empieza con una imagen nueva) de la instancia.

![Botones de instancia del servicio en la nube](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Reconfiguración del archivo .cscfg
Puede que necesite volver a configurar el servicio en la nube a través del archivo de [configuración de servicio (cscfg)](cloud-services-model-and-package.md#cscfg) . Primero debe descargar el archivo .cscfg, modificarlo y volverlo a cargar.

1. Haga clic en el icono **Configuración** o el vínculo **Toda la configuración** para abrir la hoja **Configuración**.

    ![Página de configuración](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Haga clic en el elemento **Configuración** .

    ![Hoja de configuración](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Haga clic en el botón **Descargar** .

    ![Descargar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Después de actualizar el archivo de configuración del servicio, cargue y aplique las actualizaciones de la configuración:

    ![Cargar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Seleccione el archivo .cscfg y haga clic en **Aceptar**.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo [implementar un servicio en la nube](cloud-services-how-to-create-deploy-portal.md).
* Configuración de un [nombre de dominio personalizado](cloud-services-custom-domain-name-portal.md).
* [Administración de su servicio en la nube](cloud-services-how-to-manage-portal.md).
* Configuración de [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).
