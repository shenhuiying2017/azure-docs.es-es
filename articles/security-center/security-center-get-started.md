---
title: "Guía de inicio rápido de Azure Security Center: incorporación de su suscripción de Azure al nivel Estándar de Security Center | Microsoft Docs"
description: "Esta guía de inicio rápido muestra cómo actualizarse al plan de tarifa Estándar de Security Center para incrementar la seguridad."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2018
ms.author: terrylan
ms.openlocfilehash: ac4e3b36b08223f7e3b54850ed53a8185e85f0d2
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Guía de inicio rápido: incorporación de su suscripción de Azure al nivel Estándar de Security Center
Azure Security Center proporciona administración unificada de la seguridad y protección contra amenazas para cargas de trabajo en la nube híbrida. Mientras el nivel Gratis solo ofrece seguridad limitada para los recursos de Azure, el nivel Estándar amplía estas funcionalidades en local y en otras nubes. El nivel Estándar de Security Center le ayuda a encontrar y corregir vulnerabilidades de seguridad, aplicar controles de acceso y de aplicación para bloquear actividades malintencionadas, detectar amenazas mediante análisis e inteligencia y responder rápidamente en caso de ataque. El nivel Estándar de Security Center se puede probar de forma gratuita los primeros 60 días.

En este artículo, actualizará al nivel Estándar para una mayor seguridad e instalará Microsoft Monitoring Agent en las máquinas virtuales para supervisar las amenazas y vulnerabilidades de seguridad.

## <a name="prerequisites"></a>Requisitos previos
Para empezar a trabajar con el Centro de seguridad, debe disponer de una suscripción a Microsoft Azure. Si no tiene una suscripción, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Para actualizar una suscripción al nivel Estándar, debe tener asignado el rol de propietario de la suscripción, colaborador de la suscripción o administrador de seguridad.

## <a name="enable-your-azure-subscription"></a>Habilitación de la suscripción a Azure

1. Inicie sesión en el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/).
2. En el menú de **Microsoft Azure**, seleccione **Security Center**. Se abre **Security Center - Información general**.

 ![Introducción a Security Center][2]

**Security Center - Información general** proporciona una vista unificada de la situación de seguridad de las cargas de trabajo de la nube híbrida, lo que le permite detectar y evaluar la seguridad de las cargas de trabajo e identificar y mitigar riesgos. Security Center habilita automáticamente cualquiera de las suscripciones de Azure que no fueron incorporadas previamente por usted u otro usuario de la suscripción al nivel Gratis.

Puede ver y filtrar la lista de suscripciones haciendo clic en el elemento de menú **Suscripciones**. Security Center comenzará ahora a evaluar la seguridad de estas suscripciones para identificar vulnerabilidades de seguridad. Para personalizar los tipos de evaluaciones, puede modificar la directiva de seguridad. Una directiva de seguridad define la configuración deseada de las cargas de trabajo. Además, ayuda a garantizar el cumplimiento de los requisitos de seguridad normativos o de la empresa.

A los pocos minutos de iniciar Security Center la primera vez, puede ver:

- **Recomendaciones** para formas de mejorar la seguridad de las suscripciones de Azure. Al hacer clic en el icono **Recomendaciones** se iniciará una lista de prioridades.
- Un inventario de los recursos **Compute**, **Redes**, **Almacenamiento y datos** y **Aplicaciones** a los que ahora accede Security Center junto con la posición de seguridad de cada uno de ellos.

Para aprovechar al máximo Security Center, debe completar los pasos siguientes para actualizar al nivel Estándar e instalar Microsoft Monitoring Agent.

## <a name="upgrade-to-the-standard-tier"></a>Actualización al nivel Estándar
Para conseguir las guías de inicio rápido y los tutoriales de Security Center, debe actualizar al nivel Estándar. Los primeros sesenta días son gratis y puede regresar al nivel Gratis en cualquier momento.

1. En el menú principal de Security Center, seleccione **Incorporación a la seguridad avanzada**.

2. En **Incorporación a la seguridad avanzada**, Security Center muestra las suscripciones y áreas de trabajo aptas para la incorporación. Seleccione una suscripción en la lista.

  ![Selección de una suscripción][4]

3. **Directiva de seguridad** proporciona información sobre los grupos de recursos incluidos en la suscripción. **Precios** también se abre.
4. En **Precios**, seleccione **Estándar** para actualizar del nivel Gratis al nivel Estándar y haga clic en **Guardar**.

  ![Selección de Estándar][5]

Ahora que ha actualizado al nivel Estándar, tiene acceso a características adicionales de Security Center, incluidos los **controles de aplicación adaptables**, **acceso a VM Just-In-Time**, **alertas de seguridad**, **inteligencia sobre amenazas**, **playbook de automatización** y mucho más. Tenga en cuenta que las alertas de seguridad solo aparecerán cuando Security Center detecte actividad malintencionada.

  ![Alertas de seguridad][7]

## <a name="automate-data-collection"></a>Recopilación automática de datos
Security Center recopila datos de VM de Azure y de los equipos que no son de Azure para supervisar las amenazas y vulnerabilidades de seguridad. Los datos se recopilan con Microsoft Monitoring Agent, que lee distintas configuraciones relacionadas con la seguridad y distintos registros de eventos de la máquina y copia los datos en el área de trabajo para analizarlos. De forma predeterminada, Azure Security Center creará automáticamente una nueva área de trabajo.

Si el aprovisionamiento automático está habilitado, Security Center instala Microsoft Monitoring Agent en todas las VM de Azure compatibles y en las nuevas que se creen. Se recomienda encarecidamente el aprovisionamiento automático.

Para habilitar el aprovisionamiento automático de Microsoft Monitoring Agent:

1. En el menú principal de Security Center, seleccione **Directiva de seguridad**.
2. Seleccione la suscripción.
3. En **Directiva de seguridad**, seleccione **Recopilación de datos**.
4. En **Recopilación de datos**, seleccione **Activado** para habilitar el aprovisionamiento automático.
5. Seleccione **Guardar**.

  ![Habilitar el aprovisionamiento automático][6]

Con esta nueva información sobre VM de Azure, Security Center puede proporcionar recomendaciones adicionales relacionados con el estado de actualización del sistema, las configuraciones de seguridad del SO, la protección de puntos de conexión, así como generar alertas de seguridad adicionales.

  ![Recomendaciones][8]

## <a name="clean-up-resources"></a>Limpieza de recursos
Otras guías de inicio rápido y tutoriales de esta colección se basan en los valores de esta. Si planea continuar trabajando con las guías rápidas y tutoriales posteriores, debe seguir ejecutando el plan de tarifa Estándar y mantener el aprovisionamiento automático habilitado. Si no planea continuar o desea volver al nivel Gratis:

1. Vuelva al menú principal de Security Center y seleccione **Directiva de seguridad**.
2. Seleccione la suscripción o directiva que desea que vuelva al nivel Gratis. Se abre **Directiva de seguridad**.
3. En **COMPONENTES DE LA DIRECTIVA**, seleccione **Plan de tarifa**.
4. Seleccione **Gratis** para cambiar la suscripción de Estándar a Gratis.
5. Seleccione **Guardar**.

Si desea deshabilitar el aprovisionamiento automático:

1. Vuelva al menú principal de Security Center y seleccione **Directiva de seguridad**.
2. Seleccione la suscripción en la que quiere deshabilitar el aprovisionamiento automático.
3. En **Directiva de seguridad: Colección de datos**, en **Incorporación**, seleccione **Desactivado** para deshabilitar el aprovisionamiento automático.
4. Seleccione **Guardar**.

>[!NOTE]
> La deshabilitación del aprovisionamiento automático no quita Microsoft Monitoring Agent de las máquinas virtuales de Azure en las que se ha aprovisionado el agente. La deshabilitación del aprovisionamiento automático limita la supervisión de seguridad de los recursos.
>

## <a name="next-steps"></a>pasos siguientes
En esta guía de inicio rápido ha actualizado al nivel Standard y ha aprovisionado Microsoft Monitoring Agent para la administración unificada de seguridad y protección contra amenazas para cargas de trabajo en la nube híbrida. Para más información sobre cómo usar Security Center, siga la guía de inicio rápido para la incorporación de equipos Windows que se encuentran en local y en otras nubes.

> [!div class="nextstepaction"]
> [Guía de inicio rápido: incorporación de equipos Windows a Azure Security Center](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/onboarding.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
