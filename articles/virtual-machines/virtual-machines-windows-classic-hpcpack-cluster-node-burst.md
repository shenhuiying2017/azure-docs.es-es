<properties
 pageTitle="Agregar nodos de ráfaga a un clúster de HPC Pack | Microsoft Azure"
 description="Conozca cómo expandir la capacidad del clúster de HPC Pack bajo demanda agregando instancias de rol de trabajo que se ejecutan en un servicio en la nube"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/15/2016"
 ms.author="danlep"/>

# Adición de nodos de ráfaga a petición a un clúster de HPC Pack en Azure



En este artículo se muestra cómo agregar nodos de "ráfaga" de Azure (instancias de rol de trabajo que se ejecutan un servicio en la nube) como recursos de proceso a un nodo principal de HPC Pack existente en Azure. Con el nodo de ráfaga, podrá escalar o reducir verticalmente la capacidad de proceso del clúster de HPC en Azure a petición, sin tener que mantener un conjunto de máquinas virtuales de nodos de proceso preconfiguradas.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

![Nodos de ráfaga][burst]

Los pasos descritos en este artículo le ayudarán a agregar nodos de Azure rápidamente a una máquina virtual de nodo principal de HPC Pack basado en la nube para la implementación de una prueba o una prueba de concepto. El procedimiento es básicamente el mismo que el de "ráfaga en Azure" para agregar capacidad de proceso en la nube a un clúster de HPC Pack local. Si desea conseguir un tutorial, consulte [Configurar un clúster de proceso híbrido con Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Para obtener instrucciones detalladas y consideraciones acerca de las implementaciones de producción, consulte [Ráfaga en Azure con Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

Para más información sobre el uso del tamaño de instancia de proceso intensivo para los nodos de ráfaga, consulte [Acerca de las máquinas virtuales de la serie H y A de proceso intensivo](virtual-machines-windows-a8-a9-a10-a11-specs.md).

## Requisitos previos

* **Nodo principal de HPC Pack implementado en una máquina virtual de Azure**: puede usar una máquina virtual del nodo principal independiente o una que forme parte de un clúster de mayor tamaño. Para crear un nodo principal independiente, consulte [Creación del nodo principal de un clúster de HPC Pack en una máquina virtual de Azure con una imagen de Marketplace](virtual-machines-windows-hpcpack-cluster-headnode.md). Para ver las opciones de implementación de HPC Pack, consulte [Opciones para crear y administrar un clúster de informática de alto rendimiento (HPC) de Linux en Azure con Microsoft HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

    >[AZURE.TIP] Si usa el [script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para crear el clúster en Azure, puede incluir nodos de ráfaga de Azure en la implementación automatizada. Consulte los ejemplos del artículo correspondiente.

* **Suscripción de Azure**: para agregar nodos de Azure, puede elegir la misma suscripción usada para implementar la máquina virtual del nodo principal o una suscripción (o suscripciones) diferente.

* **Cuota de núcleos**: tal vez tenga que aumentar la cuota de núcleos, especialmente si decide implementar varios nodos de Azure con tamaños de núcleos múltiples. Para aumentar una cuota, [abra una solicitud de soporte técnico al cliente en línea](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sin cargo alguno.

## Paso 1: Crear un servicio en la nube y una cuenta de almacenamiento para los nodos de Azure

Use el Portal de Azure clásico o herramientas equivalentes para configurar los siguientes recursos, necesarios para implementar los nodos de Azure:

* Un nuevo servicio en la nube de Azure
* Una nueva cuenta de almacenamiento de Azure

>[AZURE.NOTE] No reutilice un servicio en la nube existente en su suscripción.

**Consideraciones**

* Configure un servicio en la nube independiente para cada plantilla de nodo de Azure que planea crear. Sin embargo, puede usar la misma cuenta de almacenamiento para varias plantillas de nodos.

* Le recomendamos ubicar el servicio en la nube y la cuenta de almacenamiento para la implementación en la misma región de Azure.




## Paso 2: Configurar un certificado de administración de Azure

Para agregar nodos de Azure como recursos de proceso, necesita un certificado de administración en el nodo principal y cargar un certificado correspondiente a la suscripción de Azure usada para la implementación.

En este escenario, puede elegir el **Certificado de administración de Azure de HPC predeterminado** que HPC Pack instala y configura automáticamente en el nodo principal. Este certificado resulta de utilidad para pruebas e implementaciones de prueba de concepto. Para usar este certificado, cargue el archivo C:\\Archivos de programa\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer desde la máquina virtual del nodo principal a la suscripción. Para cargar el certificado en el [portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Configuración** > **Certificados de administración**.

Para ver opciones adicionales para configurar el certificado de administración, consulte [Escenarios para configurar el certificado de administración de Azure para implementaciones de ráfaga de Azure](http://technet.microsoft.com/library/gg481759.aspx).

## Paso 3: Implementar nodos de Azure al clúster



Los pasos para agregar e iniciar nodos de Azure en este escenario suelen ser los mismos que los empleados con un nodo principal local. Para más información, consulte las secciones siguientes en [Pasos para implementar nodos de Azure con Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Crear una plantilla de nodo de Azure

* Agregar nodos de Azure al clúster de Windows HPC

* Iniciar (aprovisionar) los nodos de Azure

Después de agregar e iniciar los nodos, estarán listos para que los use para ejecutar trabajos de clúster.

Si tiene problemas al implementar nodos de Azure, consulte [Solución de problemas de implementaciones de nodos de Azure con Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097.aspx).

## Pasos siguientes

* Si quiere aumentar o reducir automáticamente los recursos de proceso de Azure según la carga de trabajo del clúster, consulte [Aumento y reducción automáticos de los recursos de clúster de HPC Pack en Azure según la carga de trabajo de clúster](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-cluster-node-burst/burst.png

<!---HONumber=AcomDC_0928_2016-->