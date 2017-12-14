---
title: "Supervisión de un clúster de Azure DC/OS: Operations Management"
description: "Uso de Microsoft Operations Management Suite para supervisar un clúster DC/OS de Azure Container Service."
services: container-service
author: keikhara
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: a675f0b57ed9e5d515cfa79a3a841e0f133fff6f
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-operations-management-suite"></a>Uso de Operations Management Suite para supervisar un clúster DC/OS de Azure Container Service

Microsoft Operations Management Suite (OMS) es la solución de administración de TI basada en la nube de Microsoft que le ayuda a administrar y proteger su infraestructura local y en la nube. La solución de contenedor es una solución de Log Analytics de OMS, que le permite ver el inventario de contenedor, el rendimiento y los registros en una sola ubicación. Puede auditar contenedores y solucionar problemas de los mismos mediante la visualización de los registros en una ubicación centralizada, así como encontrar contenedores ruidosos con un consumo excesivo.

![](media/container-service-monitoring-oms/image1.png)

Para más información sobre la solución de contenedor, consulte [Solución Containers (versión preliminar) en Log Analytics](../../log-analytics/log-analytics-containers.md).

## <a name="setting-up-oms-from-the-dcos-universe"></a>Configuración de OMS desde el universo de DC/OS


En este artículo se supone que ha configurado un DC/OS y ha implementado aplicaciones de contenedor web simples en el clúster.

### <a name="pre-requisite"></a>Requisito previo
- [Suscripción de Microsoft Azure](https://azure.microsoft.com/free/) (se puede obtener de gratis).  
- Configuración de área de trabajo de Microsoft OMS (consulte el "paso 3" a continuación)
- [CLI de DC/OS](https://dcos.io/docs/1.8/usage/cli/install/) instalada.

1. En el panel de DC/OS, haga clic en Universo y busque ‘OMS’ tal y como se muestra a continuación.

![](media/container-service-monitoring-oms/image2.png)

2. Haga clic en **Instalar**. Verá un mensaje emergente con la información de versión OMS y un botón **Instalar paquete** o **Advanced Installation** (Instalación avanzada). Al hacer clic en **Instalación avanzada**, se abre la página **OMS specific configuration properties** (Propiedades de configuración específicas de OMS).

![](media/container-service-monitoring-oms/image3.png)

![](media/container-service-monitoring-oms/image4.png)

3. Aquí se le pedirá que escriba `wsid` (el identificador del área de trabajo de OMS) y `wskey` (la clave principal de OMS para el identificador del área de trabajo). Para obtener ambos `wsid` y `wskey` debe crear una cuenta OMS en <https://mms.microsoft.com>. Siga los pasos para crear una cuenta. Una vez que haya terminado de crear la cuenta, deberá obtener su `wsid` y `wskey` haciendo clic en **Configuración**, luego en **Orígenes conectados** y, por último, en **Servidores Linux**, tal y como se muestra a continuación.

 ![](media/container-service-monitoring-oms/image5.png)

4. Seleccione el número de instancias de OMS que desee y haga clic en el botón 'Revisar e instalar'. Por lo general, querrá tener el mismo número de instancias de OMS que VM hay en el clúster de agente. El agente de OMS para Linux se instala como contenedores individuales en cada VM que quiere para recopilar información de supervisión e inicio de sesión.

## <a name="setting-up-a-simple-oms-dashboard"></a>Configuración de un panel de OMS

Una vez que instalado al agente de OMS para Linux en las VM, el siguiente paso es configurar el panel de OMS. Hay dos maneras de hacerlo: mediante el portal de OMS o mediante Azure Portal.

### <a name="oms-portal"></a>Portal de OMS 

Inicie sesión en el portal de OMS (<https://mms.microsoft.com>) y vaya a la **Galería de soluciones**.

![](media/container-service-monitoring-oms/image6.png)

Cuando se encuentre en la **Galería de soluciones**, seleccione **Contenedores**.

![](media/container-service-monitoring-oms/image7.png)

Una vez que haya seleccionado la solución de contenedor, verá el icono en la página OMS Overview Dashboard (Panel de información general de OMS). Una vez indizador los datos de contenedor ingeridos, verá el icono rellenado con información sobre los iconos de vista de la solución.

![](media/container-service-monitoring-oms/image8.png)

### <a name="azure-portal"></a>Azure Portal 

Inicie sesión en Azure Portal en <https://portal.microsoft.com/>. Vaya a **Marketplace**, seleccione **Supervisión y administración** y haga clic en **See All** (Ver todo). A continuación, escriba `containers` en la búsqueda. Verá "contenedores" en los resultados de búsqueda. Seleccione **Contenedores** y haga clic en **Crear**.

![](media/container-service-monitoring-oms/image9.png)

Cuando haga clic en **Crear**, le preguntará por el área de trabajo. Seleccione el área de trabajo o si no tiene uno, cree uno nuevo.

![](media/container-service-monitoring-oms/image10.PNG)

Una vez que haya seleccionado el área de trabajo, haga clic en **Crear**.

![](media/container-service-monitoring-oms/image11.png)

Para más información sobre la solución de contenedor de OMS, consulte [Solución Containers (versión preliminar) en Log Analytics](../../log-analytics/log-analytics-containers.md).

### <a name="how-to-scale-oms-agent-with-acs-dcos"></a>Escalado del agente de OMS con ACS DC/OS 

En caso de que se necesite tener instalado el agente de OMS o esté escalando verticalmente VMSS mediante la incorporación de más VM, puede hacerlo mediante el escalado del servicio `msoms`.

Puede ir a Marathon o la pestaña de servicios de IU de DC/OS y escalar verticalmente el número de nodos.

![](media/container-service-monitoring-oms/image12.PNG)

Esto se implementará en otros nodos que aún no han implementado el agente de OMS.

## <a name="uninstall-ms-oms"></a>Desinstalación de MS OMS

Para desinstalar MS OMS escriba el siguiente comando:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>¡Queremos saber!
¿Qué funciona? ¿Qué falta? ¿Qué más necesita para esto para que sea útil para usted? Háganos saber en <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Pasos siguientes

 Ahora que ha configurado OMS para supervisar los contenedores,[vea el panel de contenedor](../../log-analytics/log-analytics-containers.md).
