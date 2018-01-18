---
title: "Guía de inicio rápido de Azure Security Center: incorporación de equipos Linux a Security Center | Microsoft Docs"
description: "En esta guía de inicio rápido se explica cómo incorporar equipos Linux a Security Center."
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
ms.openlocfilehash: 365afd2199b1b8b2e70d882f6a729384edbdffbc
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Guía de inicio rápido: incorporación de equipos Linux a Azure Security Center
Después de incorporar las suscripciones de Azure, puede habilitar Security Center para recursos de Linux que se ejecutan fuera de Azure, por ejemplo, en entornos locales o en otras nubes, mediante el aprovisionamiento del agente de Linux.

En esta guía de inicio rápido se explica cómo instalar el agente de Linux en un equipo Linux.

## <a name="prerequisites"></a>Requisitos previos
Para empezar a trabajar con el Centro de seguridad, debe disponer de una suscripción a Microsoft Azure. Si no tiene una suscripción, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Debe disponer del plan de tarifa Estándar de Security Center para poder iniciar esta guía de inicio rápido. Vea [Incorporación de su suscripción de Azure al nivel Estándar de Security Center](security-center-get-started.md) para obtener instrucciones de actualización. Dicho plan se puede probar de forma gratuita los primeros sesenta días.

## <a name="add-new-linux-computer"></a>Adición de un nuevo equipo Linux

1. Inicie sesión en el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/).
2. En el menú de **Microsoft Azure**, seleccione **Security Center**. Se abre **Security Center: Información general**.

 ![Introducción a Security Center][2]

3. En el menú principal de Security Center, seleccione **Incorporación a la seguridad avanzada**.
4. Seleccione **¿Quiere agregar equipos que no forman parte de Azure?**
   ![Incorporación a la seguridad avanzada][3]

5. En **Agregar nuevos equipos que no son de Azure**, se muestra una lista de las áreas de trabajo de Log Analytics. La lista incluye, si procede, el área de trabajo predeterminada que Security Center crea automáticamente si el aprovisionamiento automático está habilitado. Seleccione esta área de trabajo u otra que desee usar.

    ![Agregar un equipo que no es de Azure][4]

6.  En la página **Agente directo**, en **DESCARGAR E INCORPORAR AGENTE PARA LINUX**, seleccione el botón **Copiar** para copiar el comando *wget*.

7.  Abra el Bloc de notas y pegue este comando. Guarde este archivo en una ubicación a la que se pueda acceder desde el equipo Linux.

## <a name="install-the-agent"></a>Instalación del agente

1.  En el equipo Linux, abra el archivo que guardó anteriormente. Seleccione todo el contenido, cópielo, abra una consola de terminal y pegue el comando.
2.  Una vez finalizada la instalación, puede validar que *omsagent* está instalado mediante la ejecución del comando *pgrep*. El comando devolverá el PID (identificador de proceso) de *omsagent* tal y como se muestra a continuación:

  ![Instalación del agente][5]

Los registros del agente de Security Center para Linux se pueden encontrar en: */var/opt/microsoft/omsagent/<workspace id>/log/*

  ![Registros del agente][6]

Después de algún tiempo, hasta treinta minutos, el nuevo equipo Linux aparecerá en Security Center.

Ahora puede supervisar las máquinas virtuales de Azure y los equipos que no son de Azure en un único lugar. En **Compute**, dispone de información general de todas las máquinas virtuales y los equipos junto con recomendaciones. Cada columna representa un conjunto de recomendaciones. El color representa el estado de seguridad actual del equipo o de la máquina virtual para dicha recomendación. Security Center también expone todas las detecciones de estos equipos en las alertas de seguridad.

  ![Hoja Compute][7] Hay dos tipos de iconos representados en la hoja **Compute**:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Equipos que no son de Azure

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) MV de Azure

## <a name="clean-up-resources"></a>Limpieza de recursos
Cuando ya no lo necesite, puede quitar el agente del equipo Linux.

Para eliminar el agente:

1. Descargue en el equipo el [script universal](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) del agente Linux.
2. Ejecute el archivo .sh del lote con el argumento *--purge* en el equipo, lo que elimina completamente el agente y su configuración.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>Pasos siguientes
En esta guía de inicio rápido, se va a aprovisionar el agente en un equipo Linux. Para más información sobre el uso de Security Center, siga con el tutorial para configurar una directiva de seguridad y evaluar la seguridad de los recursos.

> [!div class="nextstepaction"]
> [Tutorial: Definición y evaluación de las directivas de seguridad](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
