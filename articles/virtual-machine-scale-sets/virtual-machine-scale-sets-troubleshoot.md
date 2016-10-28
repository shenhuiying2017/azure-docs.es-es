<properties
	pageTitle="Solución de problemas de escalado automático de conjuntos de escalado de máquinas virtuales | Microsoft Azure"
	description="Solución de problemas de escalado automático de conjuntos de escalado de máquinas virtuales. Descripción de los problemas habituales y cómo resolverlos."
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="guybo"/>
    
# Solución de problemas de escalado automático de conjuntos de escalado de máquinas virtuales

**Problema**: ha creado una infraestructura de escalado automático en Azure Resource Manager con conjuntos de escalado de máquinas virtuales mediante, por ejemplo, la implementación de una plantilla como esta: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale y tiene sus reglas de escalado definidas y funcionan perfectamente, excepto porque independientemente de cuánta carga coloque en las máquinas virtuales, no se lleva a cabo el escalado automático.

## Pasos para solucionar problemas

Entre los aspectos que debe considerar se incluyen:

- ¿Cuántos núcleos tiene cada máquina virtual? ¿Está cargando cada núcleo? La plantilla de inicio rápido de Azure del ejemplo anterior incluye un script do\_work.php, que carga un único núcleo. Si utiliza una máquina virtual mayor que Standard\_A1 necesitará ejecutar esta carga varias veces. Compruebe el número de núcleos de las máquinas virtuales después de consultar [Tamaños de las máquinas virtuales Linux en Azure](../virtual-machines/virtual-machines-windows-sizes.md)

- ¿Cuántas máquinas virtuales hay en el conjunto de escalado? ¿está realizando trabajos en cada una de ellas?

    Solo se producirá un evento de escalado horizontal si el promedio de uso de CPU en **todas** las máquinas virtuales de un conjunto de escalado supera el valor de umbral durante el tiempo interno definido en las reglas de escalado automático.

- ¿Falta cualquier evento de escalado?

    Compruebe los registros de auditoría en el Portal de Azure para ver los eventos de escalado. Tal vez se haya producido un escalado o una reducción vertical que se ha perdido. Puede filtrar por “Escala”...

	![Registros de auditoría][audit]

- ¿Son los umbrales de reducción y escalado horizontal suficientemente diferentes?

    Imagine que establece una regla de escalado horizontal cuando el promedio de uso de la CPU es mayor del 50% durante más de 5 minutos y de reducción horizontal cuando el promedio de uso de la CPU es inferior al 50%. Esto puede provocar un problema "oscilación" cuando el uso de la CPU está cerca de este umbral, con acciones de escalado que constantemente aumentan y disminuyen el tamaño del conjunto. Por este motivo, el servicio de escalado automático intenta evitar una "oscilación", que puede resultar en la no realización del escalado. Por lo tanto, asegúrese de que los umbrales de escalado y reducción horizontal son lo suficiente diferentes como para permitir un cierto espacio entre el escalado.

- ¿Escribió su propia plantilla JSON?

    Es fácil cometer errores, así que comience con una plantilla como la anterior que ya sabemos que funciona y haga pequeños cambios incrementales. La plantilla debe poner en correlación una cuenta de almacenamiento de la extensión de diagnóstico, el conjunto de escalado y el recurso Microsoft.Insights y hacer referencia correctamente al nombre de la métrica de datos de rendimiento, el cual es diferente en Windows y Linux.

- ¿Puede escalar o reducir horizontalmente de forma manual?

    Intente volver a implementar el recurso del conjunto de escalado en la máquina virtual con una configuración diferente de "capacidad" para cambiar el número de máquinas virtuales manualmente. Aquí le mostramos un ejemplo de plantilla para hacerlo: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing; puede que necesite modificar la plantilla para asegurarse de que tiene el mismo tamaño de máquina que está usando su conjunto de escalado. Si puede cambiar correctamente el número de máquinas virtuales de forma manual, sabrá que ha aislado el problema para el escalado automático.

- Compruebe los recursos Microsoft.Compute/virtualMachineScaleSet y Microsoft.Insights en el [Explorador de recursos de Azure](https://resources.azure.com/)

    Se trata de una herramienta indispensable de solución de problemas que muestra el estado de los recursos de Azure Resource Manager. Haga clic en la suscripción y examine el grupo de recursos que se están diagnosticando. En el proveedor de recursos de proceso mire el conjunto de escalado de máquinas virtuales que ha creado y compruebe la vista de instancia que muestra el estado de una implementación. Compruebe también la vista de instancia de las máquinas virtuales del conjunto de escalado correspondiente. A continuación, entre en el proveedor de recursos de Microsoft.Insights y compruebe que las reglas de escalado automático se ven bien.

- ¿Funciona la extensión Diagnóstico y emite los datos de rendimiento?
 
    El escalado automático en Azure Resource Manager funciona por medio de una extensión de máquina virtual denominada extensión Diagnósticos (dividida en extensión de diagnósticos de Linux y de Windows). Emite datos de rendimiento para una cuenta de almacenamiento que se define en la plantilla. A continuación, el servicio de Azure Insights agrega estos datos.

    Si el servicio Insights no puede leer los datos de las máquinas virtuales, por ejemplo si estas no funcionan, el servicio debería enviarle un correo electrónico, por lo que debe comprobar el correo (el mismo que especificó al crear la cuenta de Azure).

    También puede comprobar los datos usted mismo. Observe la cuenta de almacenamiento de Azure mediante Cloud Explorer. Por ejemplo, mediante [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), inicie sesión y seleccione la suscripción de Azure que está usando y el nombre de la cuenta de almacenamiento de información de Diagnósticos al que se hace referencia en la definición de la extensión Diagnósticos en la plantilla de implementación...

	![Cloud Explorer][explorer]

    Aquí verá una serie de tablas donde se almacenan los datos de cada máquina virtual. Tomando como ejemplo Linux y las métricas de CPU, observe las filas más recientes. Visual Studio Cloud Explorer admite un lenguaje de consulta para que pueda ejecutar una consulta como "Timestamp gt datetime’2016-02-02T21:20:00Z’" para asegurarse de que obtiene los eventos más recientes (se supone que la hora es UTC). ¿Se corresponden los datos que puede ver allí con las reglas de escalado que ha configurado? En el ejemplo siguiente, el uso de la CPU para la máquina 20 empezó a aumentar al 100% durante los últimos 5 minutos...

	![Tablas de almacenamiento][tables]

    Si los datos no están allí, eso significa que el problema está relacionado con la extensión de diagnóstico que se ejecuta en las máquinas virtuales. Si los datos están allí, significa que hay un problema con las reglas de escalado o con el servicio Insights. Compruebe el [estado de Azure](https://azure.microsoft.com/status/).

    Una vez que ha seguido todos estos pasos, puede probar los foros de [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp) o de [desbordamiento de pila](http://stackoverflow.com/questions/tagged/azure), o iniciar una llamada de soporte técnico. Esté preparado para compartir la plantilla y una vista de los datos de rendimiento.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png

<!---HONumber=AcomDC_0427_2016-->