---
title: "Solución de problemas de escalado automático de conjuntos de escalado de máquinas virtuales | Microsoft Docs"
description: "Solución de problemas de escalado automático de conjuntos de escalado de máquinas virtuales. Descripción de los problemas habituales y cómo resolverlos."
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: guybo
ms.openlocfilehash: bd45a0fb99a77851aa7b91d23bd4b830b6f5cc7b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Solución de problemas de escalado automático de conjuntos de escalado de máquinas virtuales
**Problema**: ha creado una infraestructura de escalado automático en Azure Resource Manager mediante conjuntos de escalado de máquinas virtuales; por ejemplo, al implementar una plantilla como esta: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale, tiene sus reglas de escalado definidas y funcionan bien, excepto que sin importar cuánta carga se coloque en las máquinas virtuales, no se escalará automáticamente.

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas
Entre los aspectos que debe considerar se incluyen:

* ¿Cuántos núcleos tiene cada máquina virtual? ¿Está cargando cada núcleo?
  La plantilla de inicio rápido de Azure del ejemplo anterior incluye un script do_work.php, que carga un único núcleo. Si está usando una máquina virtual con un tamaño superior a un único núcleo, como Standard_A1 o D1, debe ejecutar entonces esta carga varias veces. Compruebe el número de núcleos de las máquinas virtuales después de consultar [Tamaños de las máquinas virtuales Windows en Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* ¿Cuántas máquinas virtuales hay en el conjunto de escalado? ¿está realizando trabajos en cada una de ellas?
  
    Solo se producirá un evento de escalado horizontal si el promedio de uso de CPU en **todas** las máquinas virtuales de un conjunto de escalado supera el valor de umbral durante el tiempo interno definido en las reglas de escalado automático.
* ¿Falta cualquier evento de escalado?
  
    Compruebe los registros de auditoría en el Portal de Azure para ver los eventos de escalado. Tal vez se haya producido un escalado o una reducción vertical que se ha perdido. Puede filtrar por “Escala”...
  
    ![Registros de auditoría][audit]
* ¿Son los umbrales de reducción y escalado horizontal suficientemente diferentes?
  
    Imagine que establece una regla de escalado horizontal cuando el promedio de uso de la CPU es mayor del 50% durante más de 5 minutos y de reducción horizontal cuando el promedio de uso de la CPU es inferior al 50%. Esto puede provocar un problema "oscilación" cuando el uso de la CPU está cerca de este umbral, con acciones de escalado que constantemente aumentan y disminuyen el tamaño del conjunto. Por este motivo, el servicio de escalado automático intenta evitar una "oscilación", que puede resultar en la no realización del escalado. Por lo tanto, asegúrese de que los umbrales de escalado y reducción horizontal son lo suficiente diferentes como para permitir un cierto espacio entre el escalado.
* ¿Escribió su propia plantilla JSON?
  
    Es fácil cometer errores, así que comience con una plantilla como la anterior que ya sabemos que funciona y haga pequeños cambios incrementales. 
* ¿Puede escalar o reducir horizontalmente de forma manual?
  
    Intente volver a implementar el recurso del conjunto de escalado en la máquina virtual con una configuración diferente de "capacidad" para cambiar el número de máquinas virtuales manualmente. Una plantilla de ejemplo para hacer esto es: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing. Puede que deba editar la plantilla para asegurarse de que tiene el mismo tamaño de máquina que el conjunto de escalado que está usando. Si puede cambiar correctamente el número de máquinas virtuales de forma manual, sabrá que ha aislado el problema para el escalado automático.
* Compruebe los recursos Microsoft.Compute/virtualMachineScaleSet y Microsoft.Insights en el [Explorador de recursos de Azure](https://resources.azure.com/)
  
    Se trata de una herramienta indispensable de solución de problemas que muestra el estado de los recursos de Azure Resource Manager. Haga clic en la suscripción y examine el grupo de recursos que se están diagnosticando. En el proveedor de recursos de proceso mire el conjunto de escalado de máquinas virtuales que ha creado y compruebe la vista de instancia que muestra el estado de una implementación. Compruebe también la vista de instancia de las máquinas virtuales del conjunto de escalado correspondiente. A continuación, entre en el proveedor de recursos de Microsoft.Insights y compruebe que las reglas de escalado automático se ven bien.
* ¿Funciona la extensión Diagnóstico y emite los datos de rendimiento?
  
    **Actualización:** el escalado automático de Azure se ha mejorado para usar una canalización de métricas basada en host que ya no requiere la instalación de una extensión de diagnóstico. Esto significa que los siguientes párrafos ya no se aplican si crea una aplicación de escalado automático con la nueva canalización. En https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale encontrará un ejemplo de plantillas de Azure que se han convertido para usar la canalización de host. 
  
    El uso de métricas basadas en host para el escalado automático es mejor por las razones siguientes:
  
  * Menos partes móviles ya que no es necesario instalar ninguna extensión de diagnóstico.
  * Plantillas más sencillas. Basta con que agregue reglas de escalado automático de Insights a una plantilla de conjunto de escalado existente.
  * Informes más confiable e inicio rápido de nuevas máquinas virtuales.
    
    Los únicos motivos por los que querría seguir usando una extensión de diagnóstico serían si necesitara informes o escalado de diagnósticos de memoria. Las métricas basadas en host no informan sobre memoria.
    
    Teniendo este en cuenta, siga el resto de este artículo si aún usa extensiones de diagnóstico para el escalado automático.
    
    El escalado automático en Azure Resource Manager puede funcionar (aunque ya no es necesario) por medio de una extensión de máquina virtual llamada extensión de diagnóstico. Emite datos de rendimiento para una cuenta de almacenamiento que se define en la plantilla. A continuación, el servicio Azure Monitor agrega estos datos.
    
    Si el servicio Insights no puede leer los datos de las máquinas virtuales, por ejemplo si estas no funcionan, el servicio debería enviarle un correo electrónico, por lo que debe comprobar el correo (el mismo que especificó al crear la cuenta de Azure).
    
    También puede comprobar los datos usted mismo. Observe la cuenta de almacenamiento de Azure mediante Cloud Explorer. Por ejemplo, mediante [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), inicie sesión y seleccione la suscripción de Azure que está usando y el nombre de la cuenta de almacenamiento de información de Diagnósticos al que se hace referencia en la definición de la extensión Diagnósticos en la plantilla de implementación...
    
    ![Cloud Explorer][explorer]
    
    Aquí verá una serie de tablas donde se almacenan los datos de cada máquina virtual. Tomando como ejemplo Linux y las métricas de CPU, observe las filas más recientes. Visual Studio Cloud Explorer admite un lenguaje de consulta para que pueda ejecutar una consulta como "Timestamp gt datetime’2016-02-02T21:20:00Z’" para asegurarse de que obtiene los eventos más recientes (se supone que la hora es UTC). ¿Se corresponden los datos que puede ver allí con las reglas de escalado que ha configurado? En el ejemplo siguiente, el uso de la CPU para la máquina 20 empezó a aumentar al 100% durante los últimos 5 minutos...
    
    ![Tablas de almacenamiento][tables]
    
    Si los datos no están allí, eso significa que el problema está relacionado con la extensión de diagnóstico que se ejecuta en las máquinas virtuales. Si los datos están allí, significa que hay un problema con las reglas de escalado o con el servicio Insights. Compruebe el [estado de Azure](https://azure.microsoft.com/status/).
    
    Después de haber seguido este pasos, si sigue teniendo problemas con el escalado automático, podría probar con los foros de [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp) o [Stack Overflow](http://stackoverflow.com/questions/tagged/azure), o realizar una llamada al soporte técnico. Esté preparado para compartir la plantilla y una vista de los datos de rendimiento.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
