---
title: "Galerías de runbooks y módulos para Azure Automation | Microsoft Docs"
description: "Dispone de runbooks y módulos de Microsoft y la comunidad para instalarlos y usarlos en su entorno de Azure Automation.  En este artículo, se describe cómo acceder a estos recursos y contribuir sus runbooks a la galería."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: d3fee7b4-630a-4c10-8425-9bf51d7c9e58
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte;bwren
ms.openlocfilehash: d6a950d69e5279c3cc19d8e457bfa23c2b40f515
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galerías de runbooks y módulos para Azure Automation
En lugar de crear sus propios runbooks y módulos en Azure Automation, dispone de acceso a diversos escenarios ya compilados por Microsoft y la comunidad.  Puede usar estos escenarios sin modificaciones o como punto de partida para modificarlos según sus requisitos específicos.

Puede obtener runbooks en la [Galería de Runbook](#runbooks-in-runbook-gallery) y módulos en la [Galería de PowerShell](#modules-in-powerShell-gallery).  También puede contribuir a la comunidad si comparte los escenarios que desarrolle.

## <a name="runbooks-in-runbook-gallery"></a>Runbooks en la Galería de runbooks
La [Galería de Runbook](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) ofrece diversos runbooks de Microsoft y la comunidad que se pueden importar a Azure Automation. Puede descargar un runbook de la galería, que se hospeda en el [Centro de scripts de TechNet](https://gallery.technet.microsoft.com/scriptcenter/site/upload), o puede importar runbooks directamente desde la galería de Azure Portal.

Solo se puede importar directamente desde la Galería de Runbooks mediante Azure Portal. No se puede llevar a cabo esta función mediante Windows PowerShell.

> [!NOTE]
> Debería validar el contenido de los runbooks que obtenga de la Galería de runbooks y extremar el cuidado al instalarlos y ejecutarlos en un entorno de producción.|
> 
> 

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Para importar un runbook de la Galería de runbooks con el Portal de Azure
1. En Azure Portal, abra su cuenta de Automation.
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Haga clic en el botón **Examinar galería** .
   
    ![Botón Examinar galería](media/automation-runbook-gallery/browse-gallery-button.png)
4. Busque el elemento de la galería que desee y selecciónelo para ver sus detalles.
   
    ![Examinar galería](media/automation-runbook-gallery/browse-gallery.png)
5. Haga clic en **Ver proyecto de origen** para ver el elemento en el [Centro de scripts de TechNet](http://gallery.technet.microsoft.com/).
6. Para importar un elemento, haga clic en él para ver sus detalles y después haga clic en el botón **Importar** .
   
    ![Botón Importar](media/automation-runbook-gallery/gallery-item-detail.png)
7. Si lo desea, cambie el nombre del runbook y después haga clic en **Aceptar** para importarlo.
8. El runbook aparecerá en la pestaña **Runbooks** para la cuenta de Automation.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Adición de un runbook a la Galería de runbooks
Microsoft recomienda agregar a la Galería de Runbooks aquellos runbooks que piense que podrían ser útiles para otros clientes.  Puede agregar un runbook si [lo carga al Centro de scripts](http://gallery.technet.microsoft.com/site/upload) teniendo en cuenta los siguientes detalles.

* Necesita especificar *Windows Azure* como **categoría** y *Automation* como **subcategoría** para el runbook que se mostrará en el asistente.  
* Se debe cargar un único archivo. ps1 o .graphrunbook.  Si el runbook requiere módulos, runbooks secundarios o recursos, debe enumerarlos en la descripción del envío y en la sección de comentarios del runbook.  Si tiene un escenario que requiere varios runbooks, cargue cada uno por separado e indique los nombres de los runbooks relacionados en cada una de sus descripciones. Asegúrese de usar las mismas etiquetas para que se muestren en la misma categoría. Un usuario tendrá que leer la descripción para saber que se requieren otros runbooks para que funcione el escenario.
* Agregue la etiqueta "GraphicalPS" si va a publicar un **runbook gráfico** (no en un flujo de trabajo gráfico). 
* Inserte un fragmento de código de Flujo de trabajo de PowerShell o PowerShell en la descripción con el icono **Insertar sección de código** .
* El resumen de la descarga se mostrará en los resultados de la Galería de runbooks, por lo que debería proporcionar información detallada que ayude a un usuario a identificar la funcionalidad del runbook.
* Debería asignar entre una y tres de las etiquetas siguientes a la carga.  El runbook se mostrará en el asistente en las categorías que coincidan con sus etiquetas.  El asistente pasará por alto las etiquetas que no aparezcan en esta lista. Si no especifica ninguna etiqueta coincidente, el runbook se incluirá en la categoría Otros.
  
  * Backup
  * Administración de la capacidad
  * Control de cambios
  * Cumplimiento normativo
  * Desarrollo y entornos de prueba
  * Recuperación ante desastres
  * Supervisión
  * Aplicación de revisiones
  * Aprovisionamiento
  * Corrección
  * Administración del ciclo de vida de VM
* Automation actualiza la galería una vez a la hora, por lo que no verá sus contribuciones de inmediato.

## <a name="modules-in-powershell-gallery"></a>Módulos en la Galería de PowerShell
Los módulos de PowerShell contienen cmdlets que puede usar en sus runbooks; los módulos existentes que se pueden instalar en Azure Automation están disponibles en la [Galería de PowerShell](http://www.powershellgallery.com).  Puede iniciar esta galería desde el Portal de Azure e instalarlos directamente en Azure Automation o puede descargarlos e instalarlos de forma manual.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Para importar un módulo de la Galería de módulos de automatización con el Portal de Azure
1. En Azure Portal, abra su cuenta de Automation.
2. Seleccione **Módulos** en **Recursos compartidos** para abrir la lista de módulos.
4. Haga clic en **Examinar la galería** en la parte superior de la página.
   
    ![Galería de módulos](media/automation-runbook-gallery/modules-blade.png) <br>
5. En la página **Examinar galería**, puede buscar por los campos siguientes:
   
   * Nombre de módulo
   * Etiquetas
   * Autor
   * Nombre de cmdlet/recurso de DSC
6. Busque un módulo que le interese y selecciónelo para ver sus detalles.  
   Cuando explore un módulo específico, podrá ver más información sobre el módulo, incluido un vínculo a la Galería de PowerShell, las dependencias necesarias y todos los cmdlets o recursos de DSC que contiene el módulo.
   
    ![Detalles del módulo de PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. Para instalar el módulo directamente en Azure Automation, haga clic en el botón **Importar** .
   
    ![Botón de importación de módulo](media/automation-runbook-gallery/module-import-button.png)
8. Al hacer clic en el botón Importar, en el panel **Importar**, verá el nombre del módulo que se va a importar. Si están instaladas todas las dependencias, el botón **Aceptar** estará activo. Si faltan dependencias, debe importarlas antes de importar este módulo.
9. Haga clic en **Aceptar** para importar el módulo. Cuando Azure Automation importa un módulo en la cuenta, extrae metadatos sobre el módulo y los cmdlets.
   
    ![Página de importación de módulo](media/automation-runbook-gallery/module-import-blade.png)
   
    Esto puede tardar un par de minutos, ya que cada actividad debe extraerse.
10. Recibirá una notificación inicial indicando que el módulo se está implementado y otra notificación cuando se haya completado.
11. Una vez importado el módulo, verá las actividades disponibles y podrá usar sus recursos en los runbooks y en la Configuración de estado deseado.

## <a name="requesting-a-runbook-or-module"></a>Solicitud de un runbook o módulo
Puede enviar solicitudes a [Voz de usuario](https://feedback.azure.com/forums/246290-azure-automation/).  Si necesita ayuda para escribir un runbook o se plantea preguntas acerca de PowerShell, publique una pregunta en nuestro [foro](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Pasos siguientes
* Para empezar a trabajar con runbooks, consulte [Creación o importación de un runbook en Azure Automation](automation-creating-importing-runbook.md)
* Para comprender las diferencias entre PowerShell y los flujos de trabajo de PowerShell para runbooks, consulte [Aprendizaje del flujo de trabajo de Windows PowerShell](automation-powershell-workflow.md)

