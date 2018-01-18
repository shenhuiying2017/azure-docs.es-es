---
title: "Guía de inicio rápido de Azure Security Center: incorporación de equipos Windows a Security Center | Microsoft Docs"
description: "En esta guía de inicio rápido se explica cómo aprovisionar Microsoft Monitoring Agent en un equipo Windows."
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
ms.openlocfilehash: 50cbbca9181d67bc41632a4650c76b9636a72356
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>Guía de inicio rápido: incorporación de equipos Windows a Azure Security Center
Después de incorporar las suscripciones de Azure, puede habilitar Security Center para recursos que se ejecutan fuera de Azure, por ejemplo, en entornos locales o en otras nubes, mediante el aprovisionamiento de Microsoft Monitoring Agent.

En esta guía de inicio rápido se explica cómo instalar Microsoft Monitoring Agent en un equipo Windows.

## <a name="prerequisites"></a>Requisitos previos
Para empezar a trabajar con el Centro de seguridad, debe disponer de una suscripción a Microsoft Azure. Si no tiene una suscripción, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Debe disponer del plan de tarifa Estándar de Security Center para poder iniciar esta guía de inicio rápido. Vea [Incorporación de su suscripción de Azure al nivel Estándar de Security Center](security-center-get-started.md) para obtener instrucciones de actualización. Dicho plan se puede probar de forma gratuita los primeros sesenta días.

## <a name="add-new-windows-computer"></a>Adición de un nuevo equipo Windows

1. Inicie sesión en el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/).
2. En el menú de **Microsoft Azure**, seleccione **Security Center**. Se abre **Security Center: Información general**.

 ![Introducción a Security Center][2]

3. En el menú principal de Security Center, seleccione **Incorporación a la seguridad avanzada**.
4. Seleccione **¿Quiere agregar equipos que no forman parte de Azure?**

   ![Incorporación a la seguridad avanzada][3]

5. En **Agregar nuevos equipos que no son de Azure**, se muestra una lista de las áreas de trabajo de Log Analytics. La lista incluye, si procede, el área de trabajo predeterminada que Security Center crea automáticamente si el aprovisionamiento automático está habilitado. Seleccione esta área de trabajo u otra que desee usar.

    ![Agregar un equipo que no es de Azure][4]

  Se abre la hoja **Agente directo** con un vínculo para descargar un agente de Windows y las claves de su identificador de área de trabajo que usará para configurar el agente.

6.  Seleccione el vínculo **Descargar Agente para Windows** correspondiente a su tipo de procesador del equipo para descargar el archivo del programa de instalación.

7.  A la derecha de **Id. del área de trabajo**, seleccione el icono Copiar y pegue el identificador en el Bloc de notas.

8.  A la derecha de **Clave principal**, seleccione el icono Copiar y pegue la clave en el Bloc de notas.

## <a name="install-the-agent"></a>Instalación del agente
Ahora, debe instalar el archivo descargado en el equipo de destino.

1. Copie el archivo en el equipo de destino y ejecute el programa de instalación.
2. En la página **principal**, seleccione **Siguiente**.
3. En la página **Términos de licencia**, lea la licencia y seleccione **Acepto**.
4. En la página **Carpeta de destino**, cambie o mantenga la carpeta de instalación predeterminada y seleccione **Siguiente**.
5. En la página **Opciones de instalación del agente**, elija la opción para conectar el agente a Azure Log Analytics (OMS) y, luego, seleccione **Siguiente**.
6. En la página **Azure Log Analytics**, pegue el **identificador del área de trabajo** y la **clave del área de trabajo (clave principal)** que copió en el Bloc de notas en el procedimiento anterior.
7. Si el equipo tiene que notificar a un área de trabajo de Log Analytics en la nube de Azure Government, seleccione **Azure Gobierno de EE.UU.** de la lista desplegable **Azure Cloud**.  Si el equipo necesita comunicarse a través de un servidor proxy con el servicio de Log Analytics, seleccione **Avanzado** y proporcione la dirección URL y el número de puerto del servidor proxy.
8. Seleccione **Siguiente** cuando haya terminado de proporcionar las opciones de configuración necesarias.

  ![Instalación del agente][5]

9. En la página **Listo para instalar**, revise las opciones elegidas y seleccione **Instalar**.
10. En la página **Configuración completada correctamente**, seleccione **Finalizar**.

Una vez completado el proceso, el **Agente de administración de Microsoft** aparece en el **Panel de control**. Puede revisar ahí la configuración y verificar que el agente esté conectado.

Para más información sobre cómo instalar y configurar el agente, vea [Conexión de equipos Windows](../log-analytics/log-analytics-agent-windows.md#install-the-agent-using-setup).

Ahora puede supervisar las máquinas virtuales de Azure y los equipos que no son de Azure en un único lugar. En **Compute**, dispone de información general de todas las máquinas virtuales y los equipos junto con recomendaciones. Cada columna representa un conjunto de recomendaciones. El color representa el estado de seguridad actual del equipo o de la máquina virtual para dicha recomendación. Security Center también expone todas las detecciones de estos equipos en las alertas de seguridad.

  ![Hoja Proceso][6]

Hay dos tipos de iconos representados en la hoja **Compute**:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Equipos que no son de Azure

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) MV de Azure

## <a name="clean-up-resources"></a>Limpieza de recursos
Cuando ya no lo necesite, puede quitar el agente del equipo Windows.

Para eliminar el agente:

1. Abra el **Panel de control**.
2. Abra **Programas y características**.
3. En **Programas y características**, seleccione **Microsoft Monitoring Agent** y haga clic en **Desinstalar**.

## <a name="next-steps"></a>pasos siguientes
En esta guía de inicio rápido se ha explicado cómo aprovisionar Microsoft Monitoring Agent en un equipo Windows. Para más información sobre el uso de Security Center, siga con el tutorial para configurar una directiva de seguridad y evaluar la seguridad de los recursos.

> [!div class="nextstepaction"]
> [Tutorial: Definición y evaluación de las directivas de seguridad](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
