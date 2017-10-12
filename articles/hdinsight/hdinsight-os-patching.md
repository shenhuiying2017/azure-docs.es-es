---
title: "Configuración de la programación de aplicación de revisiones de SO para clústeres de HDInsight basado en Linux (Azure) | Microsoft Docs"
description: "Aprenda a configurar la programación de la aplicación de revisión del SO para clústeres de HDInsight basado en Linux."
services: hdinsight
documentationcenter: 
author: bprakash
manager: asadk
editor: bprakash
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: bhanupr
ms.openlocfilehash: af3c5a19ae8e2e606e4b0506f9f6dddb41192e40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="os-patching-for-hdinsight"></a>Aplicación de revisión del SO para HDInsight 
Como servicio de Hadoop administrado, HDInsight se preocupa de aplicar las revisiones del SO de las máquinas virtuales subyacentes que los clústeres de HDInsight usan. A partir del 1 de agosto de 2016, cambiamos la directiva de aplicación de revisión del SO invitado para clústeres de HDInsight basado en Linux (versión 3.4 o superior). El objetivo de la nueva directiva consiste en reducir significativamente el número de reinicios debidos a la aplicación de revisión. La nueva directiva seguirá aplicando revisiones en máquinas virtuales de clústeres Linux cada lunes o jueves a partir de las 12:00 (UTC) de manera escalonada en los distintos nodos de cualquier clúster. Sin embargo, las máquinas virtuales solo se reiniciarán, como máximo, una vez cada 30 días debido a la aplicación de revisión de SO invitado. Además, el primer reinicio de un clúster recién creado no se realizará hasta que no hayan transcurrido 30 días desde la fecha de creación del clúster. Las revisiones entrarán en vigor una vez que se reinicien las máquinas virtuales.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Configuración de la programación de la aplicación de revisión del SO para clústeres de HDInsight basado en Linux
Las máquinas virtuales de un clúster de HDInsight se deben reiniciar de forma ocasional para poder instalar las revisiones de seguridad importantes. A partir del 1 de agosto de 2016, los nuevos clústeres de HDInsight basado en Linux (versión 3.4 o superior) se reinician según la programación siguiente:

1. Una máquina virtual en el clúster solo se puede reiniciar para las revisiones, como máximo, una vez en un período de 30 días.
2. El reinicio se realiza a las 12:00 a.m., hora UTC.
3. El reinicio es un proceso escalonado entre las distintas máquinas virtuales del clúster, por lo que el clúster sigue disponible durante el proceso de reinicio.
4. El primer reinicio de un clúster recién creado no se realizará hasta que hayan transcurrido 30 días después de la fecha de creación del clúster.

Con la acción de script que se describe en este artículo, puede modificar la programación de aplicación de revisión del SO de la siguiente manera:
1. Habilite o deshabilite los reinicios automáticos.
2. Establezca la frecuencia de los reinicios (días entre reinicios).
3. Establezca el día de la semana en que se realizará un reinicio.

> [!NOTE]
> Esta acción de script solo funcionará con clústeres de HDInsight basado en Linux que se hayan creado después del 1 de agosto de 2016. Las revisiones solo entrarán en vigor una vez que se reinicien las máquinas virtuales. 
>

## <a name="how-to-use-the-script"></a>Uso del script 

Cuando se usa este script, se requiere la información siguiente:
1. La ubicación del script: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh.  HDInsight usa este URI para encontrar y ejecutar el script en todas las máquinas virtuales del clúster.
  
2. Los tipos de nodo de clúster al que se aplica el script: headnode, workernode, zookeeper. Este script se debe aplicar a todos los tipos de nodo del clúster. Si no se aplica a un tipo de nodo, las máquinas virtuales de ese tipo de nodo seguirán usando la programación de aplicación de revisión anterior.


3.  Parámetro: este script acepta tres parámetros numéricos:

    | Parámetro | Definición |
    | --- | --- |
    | Habilite o deshabilite los reinicios automáticos |0 o 1. El valor 0 deshabilita los reinicios automáticos, mientras que 1 los habilita. |
    | Frecuencia |7 a 90 (incluido). La cantidad de días que se esperará antes de reiniciar las máquinas virtuales para las revisiones que requieren un reinicio. |
    | Día de la semana |1 a 7 (incluido). El valor 1 indica que el reinicio debe realizarse un lunes y el valor 7 indica que se debe realizar el domingo. Por ejemplo, si usa los parámetros 1 60 2, los reinicios automáticos se realizan cada 60 días (como máximo) los martes. |
    | Persistencia |Cuando aplica una acción de script en un clúster existente, puede marcar el script como persistente. Los scripts persistentes se aplican cuando los nuevos nodos workernode se agregan al clúster a través de operaciones de escalado. |

> [!NOTE]
> Debe marcar este script como resistente cuando lo aplique en un clúster existente. De lo contrario, cualquier nodo nuevo que se creen a través de las operaciones de escalado usarán la programación de aplicación de revisión predeterminada.
Si aplica el script como parte del proceso de creación de clústeres, se marcará como persistente de forma automática.
>

## <a name="next-steps"></a>Pasos siguientes

Para los pasos específicos sobre cómo usar la acción de script, consulte las secciones siguiente en [Personalización de clústeres de HDInsight basado en Linux mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md):

* [Uso de una acción de script durante la creación de un clúster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Aplicación de una acción de script a un clúster en ejecución](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
