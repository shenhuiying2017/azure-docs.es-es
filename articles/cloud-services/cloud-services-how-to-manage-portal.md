---
title: "Tareas de administración de servicios en la nube comunes | Microsoft Docs"
description: "Vea cómo administrar servicios en la nube en el Portal de Azure. Estos ejemplos usan el Portal de Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: a06becda42541b22d3e090087dd96cd08c980f44
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="how-to-manage-cloud-services"></a>Administración de Cloud Services
En el área **Cloud Services** de Azure Portal, puede actualizar un rol de servicio o una implementación, pasar su servicio en la nube de ensayo a producción, vincular recursos con su servicio en la nube de modo que pueda ver las dependencias de los recursos y escalar los recursos juntos, además de eliminar un servicio en la nube o una implementación.

Para obtener más información sobre cómo escalar un servicio en la nube, haga clic [aquí](cloud-services-how-to-scale-portal.md).

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Actualización del rol de servicio en la nube o implementación
Si necesita actualizar el código de la aplicación para su servicio en la nube, use **Actualizar** en la hoja del servicio en la nube. Puede actualizar un solo rol o todos los roles. Para ello, puede cargar un nuevo paquete de servicio o un archivo de configuración de servicio.

1. En el [portal de Azure][Azure portal], seleccione el servicio en la nube que quiera actualizar. Tras esta acción, se abrirá la hoja de la instancia del servicio en la nube.
2. En la hoja, haga clic en el botón **Actualizar** .

    ![Botón Actualizar](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Actualice la implementación con un nuevo archivo de paquete de servicio (.cspkg) y un archivo de configuración de servicio (.cscfg).

    ![Implementación de actualizaciones](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **Opcionalmente** puede actualizar la etiqueta de implementación y la cuenta de almacenamiento.
5. Si uno de los roles solo tiene una instancia de rol, seleccione **Implementar aunque uno o varios roles contengan una sola instancia** para permitir que la actualización continúe.

    Azure solo puede garantizar un 99,95 % de disponibilidad del servicio durante una actualización del servicio en la nube si cada rol tiene al menos dos instancias de rol (máquinas virtuales). Con dos instancias de rol, una máquina virtual procesa las solicitudes de cliente mientras la otra se actualiza.

6. Active **Iniciar implementación** si quiere que la actualización se aplique cuando termine de cargarse el paquete.
7. Haga clic en **Aceptar** para iniciar la actualización del servicio.

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Intercambio de implementaciones para pasar un servicio en la nube de ensayo a producción
Cuando decida implementar una nueva versión de un servicio en la nube, puede colocar en etapa y probar la nueva versión en su entorno de ensayo del servicio en la nube. Use **Intercambiar** para cambiar las direcciones URL que dirigen a las dos implementaciones y promover una nueva versión a producción.

Puede intercambiar implementaciones desde la página **Cloud Services** o el panel.

1. En el [portal de Azure][Azure portal], seleccione el servicio en la nube que quiera actualizar. Tras esta acción, se abrirá la hoja de la instancia del servicio en la nube.
2. En la hoja, haga clic en el botón **Intercambiar** .

    ![Intercambio de Cloud Services](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Se abre la siguiente solicitud de confirmación.

    ![Intercambio de Cloud Services](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Después de verificar la información de la implementación, haga clic en **Aceptar** para intercambiar las implementaciones.

    El intercambio de implementaciones ocurre rápidamente porque lo único que cambia son las direcciones IP virtuales (VIP) de las implementaciones.

    Para ahorrar en los costos de proceso, puede eliminar la implementación de ensayo después de comprobar que la implementación de producción funcione según lo previsto.

### <a name="common-questions-about-swapping-deployments"></a>Preguntas comunes sobre el intercambio de implementaciones

**¿Cuáles son los requisitos previos para el intercambio de implementaciones?**

Existen principalmente dos requisitos previos para que el intercambio de implementación se realice de manera correcta:

- Si quiere usar una dirección IP estática para su ranura de producción, debe reservar también una para su ranura de ensayo. Si no lo hace así, se producirá un error en el intercambio.

- Todas las instancias de los roles se deben estar ejecutando para poder realizar el intercambio. El estado de las instancias se puede comprobar de la hoja de información general de Azure Portal. Como alternativa, puede usar el comando [Get-AzureRole](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0) de Windows PowerShell.

Tenga en cuenta que las actualizaciones del SO invitado y las operaciones de recuperación de servicios también pueden provocar errores en los intercambios de implementación. Para más información, consulte [Solución de problemas de implementación de servicios en la nube](cloud-services-troubleshoot-deployment-problems.md).

**¿Un intercambio conlleva tiempo de inactividad de mi aplicación? ¿Cómo puedo controlarlo?**

Como se ha descrito en la última sección, un intercambio de implementación suele ser rápido, ya que no es más que un cambio de configuración en Azure Load Balancer. Sin embargo, en algunos casos, puede tardar diez o más segundos y dar lugar a errores de conexión transitorios. Para limitar el impacto en los clientes, considere la posibilidad de implementar la [lógica de reintento del cliente](../best-practices-retry-general.md).

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Eliminación de implementaciones y un servicio en la nube
Antes de que pueda eliminar un servicio en la nube, debe eliminar cada implementación existente.

Para ahorrar en los costos de proceso, puede eliminar la implementación de ensayo después de comprobar que la implementación de producción funcione según lo previsto. Se le facturarán los costos de proceso de las instancias de rol implementadas que se detengan.

Use el siguiente procedimiento para eliminar una implementación o su servicio en la nube.

1. En el [portal de Azure][Azure portal], seleccione el servicio en la nube que quiera eliminar. Tras esta acción, se abrirá la hoja de la instancia del servicio en la nube.
2. En la hoja, haga clic en el botón **Eliminar** .

    ![Intercambio de Cloud Services](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Puede eliminar el servicio en la nube completo seleccionando **Servicio en la nube y sus implementaciones** o elija **Implementación de producción** o **Implementación de almacenamiento provisional**.

    ![Intercambio de Cloud Services](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Haga clic en el botón **Eliminar** situado en la parte inferior.
5. Para eliminar el servicio en la nube, haga clic en **Eliminar servicio en la nube**. Luego, haga clic en **Sí**en la solicitud de confirmación.

> [!NOTE]
> Cuando se elimina un servicio en la nube y se configura una supervisión detallada, debe eliminar manualmente los datos de la cuenta de almacenamiento. Para obtener información sobre dónde buscar las tablas métricas, consulte [este](cloud-services-how-to-monitor.md) artículo.


## <a name="how-to-find-more-information-about-failed-deployments"></a>Búsqueda de más información acerca de las implementaciones con errores
La hoja **Información general** tiene una barra de estado en la parte superior. Al hacer clic en ella, se abre una nueva hoja que muestra la información de cualquier error. Si la implementación no contiene errores, la hoja de información está en blanco.

![Intercambio de Cloud Services](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Pasos siguientes
* [Configuración general de su servicio en la nube](cloud-services-how-to-configure-portal.md).
* Obtenga información sobre cómo [implementar un servicio en la nube](cloud-services-how-to-create-deploy-portal.md).
* Configuración de un [nombre de dominio personalizado](cloud-services-custom-domain-name-portal.md).
* Configuración de [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).
