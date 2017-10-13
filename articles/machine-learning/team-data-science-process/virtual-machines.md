---
title: "Aprovisionamiento de máquinas de virtuales de ciencia de datos de Azure como IPython Notebook Servers | Microsoft Docs"
description: "Configuración de una máquina virtual de ciencia de datos como un IPython Notebook Server con herramientas compatibles."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 95e1fa87-794a-4d03-80a4-af4f3f3ac31e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: bradsev
ms.openlocfilehash: 88fe9673176cdade92faad4bbdcb2e1bd11f4a55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="provision-azure-data-science-virtual-machines-as-ipython-notebook-servers"></a>Aprovisionamiento de máquinas de virtuales de ciencia de datos de Azure como IPython Notebook Servers
Se ofrecen instrucciones que describen cómo configurar una VM de Azure y una VM de Azure con el servicio SQL como, por ejemplo, los servidores de Bloc de notas de IPython. La máquina virtual Windows se configura con herramientas de compatibilidad como IPython Notebook, el Explorador de Azure Storage y AzCopy, así como otras utilidades que son útiles para los proyectos de ciencia de datos. El Explorador de almacenamiento de Azure y AzCopy, por ejemplo, permiten cargar de manera cómoda datos en el almacenamiento de blobs de Azure desde la máquina local o descargarlos en el equipo local desde el almacenamiento. 

Este menú redirige a temas en los que se describe cómo configurar los diversos entornos de ciencia de datos que usa el [proceso de ciencia de datos en equipos (TDSP)](overview.md).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

Se pueden aprovisionar un configurar varios tipos de máquinas virtuales de Azure para usarse como parte de un entorno de ciencia de datos basado en la nube. La decisión sobre el tipo de máquina virtual que se debe usarse depende del tipo y la cantidad de datos que se quieran modelar con aprendizaje automático y el destino de los datos en la nube. 

* Para obtener orientación sobre las cuestiones que se deben tener en cuenta al tomar esta decisión, consulte [Planear su entorno de ciencia de datos de aprendizaje automático de Azure](plan-your-environment.md). 
* Para un catálogo de algunos de los escenarios que se pueden encontrar al realizar el análisis avanzado, consulte [Escenarios para la Tecnología y procesos de análisis avanzado en Aprendizaje automático de Azure](plan-sample-scenarios.md)

Se proporcionan dos conjuntos de instrucciones:

* [Configuración de una máquina virtual de Azure como servidor del Bloc de notas de IPython para realizar análisis avanzados](../data-science-virtual-machine/setup-virtual-machine.md) muestra cómo aprovisionar una máquina virtual de Azure con el Bloc de notas de IPython y otras herramientas que se usan para realizar ciencia de datos en los casos en que pueda usarse una forma de almacenamiento de Azure distinta de SQL para almacenar los datos.
* [Configuración de una máquina virtual de Azure SQL Server como servidor del Bloc de notas de IPython para realizar análisis avanzados](../data-science-virtual-machine/setup-sql-server-virtual-machine.md) muestra cómo aprovisionar una máquina virtual de Azure SQL Server con el Bloc de notas de IPython y otras herramientas que se usan para realizar ciencia de datos en los casos en que pueda usarse SQL Database para almacenar los datos.

Una vez aprovisionadas y configuradas, estas máquinas virtuales están preparadas para usarse como servidores de IPython Notebook para la exploración y el procesamiento de datos, y para otras tareas necesarias junto con los procesos de ciencia de datos en equipos (TDSP) y de Aprendizaje automático de Azure. Los pasos siguientes del proceso de ciencia de datos se asignan en la [ruta de aprendizaje de TDSP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) y pueden incluir pasos que muevan datos a SQL Server o HDInsight, los procese y muestree allí como preparación para el aprendizaje a partir de los datos con Aprendizaje automático de Azure.

> [!NOTE]
> Las máquinas virtuales de Azure tienen unas tarifas del tipo **pague solo por lo que use**. Para asegurarse de que no se le facture cuando no use la máquina virtual, debe estar en el estado **Detenida (desasignada)** en el [Portal de Azure clásico](http://manage.windowsazure.com/). Para obtener instrucciones paso a paso sobre cómo desasignar la máquina virtual, vea [Apagado y desasignación de la máquina virtual cuando no esté en uso](../data-science-virtual-machine/setup-virtual-machine.md#shutdown).
> 
> 

