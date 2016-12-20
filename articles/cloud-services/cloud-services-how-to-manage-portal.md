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
ms.date: 11/16/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 2501b6480e81b236995c37db7171a4ed1429dcbf
ms.openlocfilehash: 3400c137088800398e3757ecfcfb5007044ea66c


---
# <a name="how-to-manage-cloud-services"></a>Administración de servicios en la nube
> [!div class="op_single_selector"]
> * [Portal de Azure](cloud-services-how-to-manage-portal.md)
> * [Portal de Azure clásico](cloud-services-how-to-manage.md)
> 
> 

El servicio en la nube se administra en el área **Servicios en la nube (clásico)** de Azure Portal. En este artículo se describen algunas acciones comunes que se deben realizar al administrar servicios en la nube; por ejemplo, actualizar, eliminar, escalar y promover una implementación de ensayo a producción.

Para obtener más información sobre cómo escalar un servicio en la nube, haga clic [aquí](cloud-services-how-to-scale-portal.md).

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Actualización del rol de servicio en la nube o implementación
Si necesita actualizar el código de la aplicación para su servicio en la nube, use **Actualizar** en la hoja del servicio en la nube. Puede actualizar un solo rol o todos los roles. Para ello, puede cargar un nuevo paquete de servicio o un archivo de configuración de servicio.

1. En [Portal de Azure][Portal de Azure], seleccione el servicio en la nube que desee actualizar. Tras esta acción, se abrirá la hoja de la instancia del servicio en la nube.
2. En la hoja, haga clic en el botón **Actualizar** .
   
    ![Botón Actualizar](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Actualice la implementación con un nuevo archivo de paquete de servicio (.cspkg) y un archivo de configuración de servicio (.cscfg).
   
    ![Implementación de actualizaciones](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **Opcionalmente** puede actualizar la etiqueta de implementación y la cuenta de almacenamiento. 
5. Si uno de los roles solo tiene una instancia de rol, seleccione **Implementar aunque uno o varios roles contengan una sola instancia** para permitir que la actualización continúe. 
   
    Azure solo puede garantizar un 99,95 % de disponibilidad del servicio durante una actualización del servicio en la nube si cada rol tiene al menos dos instancias de rol (máquinas virtuales). Con dos instancias de rol, una máquina virtual procesará las solicitudes de cliente mientras la otra se actualiza.
    
6. Active **Iniciar implementación** si quiere que la actualización se aplique cuando termine de cargarse el paquete.
7. Haga clic en **Aceptar** para iniciar la actualización del servicio.

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Intercambio de implementaciones para pasar un servicio en la nube de ensayo a producción
Cuando decida implementar una nueva versión de un servicio en la nube, puede colocar en etapa y probar la nueva versión en su entorno de ensayo del servicio en la nube. Use **Intercambiar** para cambiar las direcciones URL que dirigen a las dos implementaciones y promover una nueva versión a producción. 

Puede intercambiar implementaciones desde la página **Servicios en la nube** o el panel.

1. En [Portal de Azure][Portal de Azure], seleccione el servicio en la nube que desee actualizar. Tras esta acción, se abrirá la hoja de la instancia del servicio en la nube.
2. En la hoja, haga clic en el botón **Intercambiar** .
   
    ![Intercambio de servicios en la nube](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Se abre la siguiente solicitud de confirmación.
   
    ![Intercambio de servicios en la nube](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Después de verificar la información de la implementación, haga clic en **Aceptar** para intercambiar las implementaciones.
   
    El intercambio de implementaciones ocurre rápidamente porque lo único que cambia son las direcciones IP virtuales (VIP) de las implementaciones.
   
    Para ahorrar en los costos de proceso, puede eliminar la implementación de ensayo después de comprobar que la implementación de producción funcione según lo previsto.

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Vinculación de un recurso a un servicio en la nube
El Portal de Azure no vincula recursos entre sí como el Portal de Azure clásico actual. En su lugar, implemente más recursos en el mismo grupo de recursos que está usando el servicio en la nube.

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Eliminación de implementaciones y un servicio en la nube
Antes de que pueda eliminar un servicio en la nube, debe eliminar cada implementación existente.

Para ahorrar en los costos de proceso, puede eliminar la implementación de ensayo después de comprobar que la implementación de producción funcione según lo previsto. Se le facturarán los costos de proceso de las instancias de rol implementadas que se detengan.

Use el siguiente procedimiento para eliminar una implementación o su servicio en la nube. 

1. En [Portal de Azure][Portal de Azure], seleccione el servicio en la nube que desee eliminar. Tras esta acción, se abrirá la hoja de la instancia del servicio en la nube.
2. En la hoja, haga clic en el botón **Eliminar** .
   
    ![Intercambio de servicios en la nube](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Puede eliminar el servicio en la nube completo seleccionando **Servicio en la nube y sus implementaciones** o elija **Implementación de producción** o **Implementación de almacenamiento provisional**.
   
    ![Intercambio de servicios en la nube](./media/cloud-services-how-to-manage-portal/delete-blade.png) 

4. Haga clic en el botón **Eliminar** situado en la parte inferior.
5. Para eliminar el servicio en la nube, haga clic en **Eliminar servicio en la nube**. Luego, haga clic en **Sí**en la solicitud de confirmación.

> [!NOTE]
> Cuando se elimina un servicio en la nube y se configura una supervisión detallada, debe eliminar manualmente los datos de la cuenta de almacenamiento. Para obtener información sobre dónde buscar las tablas métricas, consulte [este](cloud-services-how-to-monitor.md) artículo.
> 
> 

[Portal de Azure]: https://portal.azure.com

## <a name="next-steps"></a>Pasos siguientes
* [Configuración general de su servicio en la nube](cloud-services-how-to-configure-portal.md).
* Obtenga información sobre cómo [implementar un servicio en la nube](cloud-services-how-to-create-deploy-portal.md).
* Configuración de un [nombre de dominio personalizado](cloud-services-custom-domain-name-portal.md).
* Configuración de [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).




<!--HONumber=Nov16_HO3-->


