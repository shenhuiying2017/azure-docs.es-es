---
title: "Cómo conservar una dirección IP virtual constante para un servicio en la nube de Azure | Microsoft Docs"
description: "Obtenga información para garantizar que no cambia la dirección IP virtual (VIP) de su servicio en la nube de Azure."
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 12d73e2f490acc92bb4a2e1014bb24f7f672f713
ms.lasthandoff: 03/22/2017


---
# <a name="how-to-retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Cómo conservar una dirección IP virtual constante para un servicio en la nube de Azure
Al actualizar un servicio en la nube que se hospeda en Azure, es posible que deba asegurarse de que no cambia la dirección IP virtual (VIP) del servicio. Muchos de los servicios de administración de dominio usan el Sistema de nombres de dominio (DNS) para registrar nombres de dominio. DNS solo funciona si la dirección VIP sigue siendo la misma. Puede usar el **Asistente para publicación** en Azure Tools para asegurarse de que la dirección VIP del servicio en la nube no cambia cuando la actualiza. Para más información sobre cómo usar la administración de dominios DNS para servicios en la nube, vea [Configuración de un nombre de dominio personalizado para un servicio en la nube de Azure](cloud-services/cloud-services-custom-domain-name.md).

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>Publicar un servicio en la nube sin cambiar su VIP
La dirección VIP de un servicio en la nube se asigna al implementarla por primera vez en Azure en un entorno determinado, como el entorno de producción. La dirección VIP solo cambia si elimina la implementación de manera explícita o si se elimina implícitamente por el proceso de actualización de implementación. Para conservar la dirección VIP, no debe eliminar su implementación y también debe asegurarse de que Visual Studio no elimina su implementación automáticamente. Puede controlar el comportamiento mediante la especificación de la configuración de implementación en el **Asistente para publicación**, que admite varias opciones de implementación. Puede especificar una nueva implementación o una implementación de actualización, que puede ser incremental o simultánea, y ambos tipos de implementaciones de actualización conservan la dirección VIP. Para obtener definiciones de estos tipos diferentes de implementación, vea [Asistente Publicar aplicaciones de Azure](vs-azure-tools-publish-azure-application-wizard.md).  Además, puede controlar si la implementación anterior de un servicio en la nube se elimina si se produce un error. La dirección VIP podría cambiar de forma inesperada si esa opción no se establece correctamente.

## <a name="updating-a-cloud-service-without-changing-its-vip"></a>Actualización de un servicio en la nube sin cambiar su VIP
1. Cree o abra un proyecto de servicio en la nube de Azure en Visual Studio. 

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y, en el menú contextual, seleccione **Publicar**.

    ![Publish menu](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

1. En el cuadro de diálogo **Publicar aplicaciones de Azure**, seleccione la suscripción de Azure en la que desea realizar la implementación, inicie sesión si es necesario y, luego, seleccione **Siguiente**.

    ![Publicar aplicaciones de Azure: inicio de sesión](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

1. En la pestaña **Configuración común**, compruebe que el nombre del servicio en la nube en el que está implementando, el **Entorno**, la **Configuración de compilación** y el **Configuración del servicio** son todos correctos.

    ![Publicar aplicaciones de Azure: configuración común](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

1. En la pestaña **Configuración avanzada**, compruebe que la cuenta de almacenamiento y la etiqueta de implementación son correctas, que la casilla **Eliminar implementación en caso de error** está desactivada y que la casilla de actualización **Implementación** está activada. Seleccione la casilla **Actualización de implementación** para asegurarse de que no se elimine la implementación y de que no se pierda la dirección VIP al volver a publicar la aplicación. Desactive la casilla **Eliminar implementación en caso de error** para asegurarse de que no se pierda la dirección VIP si se produce un error durante la implementación.

    ![Publicar aplicaciones de Azure: configuración avanzada](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

1. (Opcional) Para especificar con más detalle cómo desea que se actualicen los roles, seleccione **Configuración** junto a **Actualización de implementación**. Seleccione **Actualización incremental** o **Actualización simultánea**. Si elige **Actualización incremental**, cada instancia de la aplicación se actualizará una tras otra, para que la aplicación siempre esté disponible. Si elige **Actualización simultánea**, todas las instancias de la aplicación se actualizarán al mismo tiempo. La actualización simultánea es más rápida, pero es posible que el servicio no esté disponible durante el proceso de actualización. Cuando termine, seleccione **Siguiente**.

    ![Publicar aplicaciones de Azure: configuración de actualización de implementación](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

1. Cuando vuelva al cuadro de diálogo **Publicar aplicaciones de Azure**, seleccione **Siguiente** hasta que vea la página **Resumen**. Compruebe la configuración y, luego, seleccione **Publicar**.
   
    ![Publicar aplicaciones de Azure: resumen](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>Pasos siguientes
- [Uso del Asistente Publicar aplicaciones de Azure de Visual Studio](vs-azure-tools-publish-azure-application-wizard.md)


