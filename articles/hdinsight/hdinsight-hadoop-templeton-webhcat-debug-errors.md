---
title: "Información sobre errores de WebHCat y resolución en HDInsight y Azure | Microsoft Docs"
description: "Aprenda cómo tratar errores comunes devueltos por WebHCat en HDInsight y cómo resolverlos."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1b3d94b1-207d-4550-aece-21dc45485549
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/01/2017
ms.author: larryfr
ms.openlocfilehash: c21b575e9e055b2dec69bea270012b91df2b662b
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>Entender y resolver errores recibidos de WebHCat en HDInsight

Obtener información acerca de los errores recibidos al utilizar WebHCat con HDInsight y cómo resolverlos. Las herramientas de cliente como Azure PowerShell y Data Lake Tools para Visual Studio usan WebHCat internamente.

## <a name="what-is-webhcat"></a>¿Qué es WebHCat?

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) es una API de REST para [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), una capa de administración de almacenamiento y tablas para Hadoop. WebHCat está habilitado de manera predeterminada en los clústeres de HDInsight y se usa por diversas herramientas para enviar trabajos, obtener el estado de los trabajos, etc. sin iniciar sesión en el clúster.

## <a name="modifying-configuration"></a>Modificación de la configuración

> [!IMPORTANT]
> Algunos de los errores que se muestran en este documento se producen porque se ha superado un máximo configurado. Cuando el paso de la resolución menciona que puede cambiar un valor, debe usar una de las acciones siguientes para realizar el cambio:

* Para clústeres de **Windows** : use una acción de script para configurar el valor durante la creación del clúster. Para obtener más información, vea [Desarrollar acciones de script](hdinsight-hadoop-script-actions.md).

* Para clústeres **Linux** : use Ambari (web o API de REST) para modificar el valor. Para obtener más información, vea [Administrar HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md)

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

### <a name="default-configuration"></a>Configuración predeterminada

Si se superan los siguientes valores predeterminados, puede degradar el rendimiento de WebHCat o provocar errores:

| Configuración | Qué hace | Valor predeterminado |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |El número máximo de trabajos que pueden estar activos de manera simultánea (pendientes o en ejecución) |10.000 |
| [templeton.exec.max-procs][max-procs] |El número máximo de solicitudes que se pueden atender de manera simultánea |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |El número de días que se conservará el historial de trabajos |7 días |

## <a name="too-many-requests"></a>Demasiadas solicitudes

**Código de estado HTTP**: 429

| Causa | Resolución |
| --- | --- |
| Ha superado el máximo de solicitudes simultáneas atendidas por WebHCat por minuto (el valor predeterminado es 20). |Reduzca la carga de trabajo para asegurarse de que no envía más que el número máximo de solicitudes simultáneas o aumente el límite de solicitudes simultáneas modificando `templeton.exec.max-procs`. Consulte [Modificación de la configuración](#modifying-configuration) para más información. |

## <a name="server-unavailable"></a>Servidor no disponible

**Código de estado HTTP**: 503

| Causa | Resolución |
| --- | --- |
| Este código de estado suele producirse durante la conmutación por error entre el nodo principal primario y secundario para el clúster. |Espere dos minutos y vuelva a intentar la operación. |

## <a name="bad-request-content-could-not-find-job"></a>Contenido de solicitud incorrecta: no se encontró el trabajo.

**Código de estado HTTP**: 400

| Causa | Resolución |
| --- | --- |
| Los detalles de los trabajos se han limpiado con el limpiador del historial de trabajos |El período de retención predeterminado para el historial de trabajos es de 7 días. El período de retención predeterminado puede cambiarse modificando `mapreduce.jobhistory.max-age-ms`. Consulte [Modificación de la configuración](#modifying-configuration) para más información. |
| Se ha suprimido el trabajo debido a una conmutación por error |Vuelva a intentar el envío de trabajos durante un tiempo máximo de dos minutos |
| Se usó un identificador de trabajo no válido |Compruebe si el id. de trabajo es correcto |

## <a name="bad-gateway"></a>Puerta de enlace incorrecta

**Código de estado HTTP**: 502

| Causa | Resolución |
| --- | --- |
| La recolección de elementos no utilizados internos se está produciendo en el proceso de WebHCat |Espere a que termine la recolección de elementos no utilizados o reinicie el servicio de WebHCat |
| Tiempo de espera de una respuesta desde el servicio de ResourceManager. Este error se puede producir cuando el número de aplicaciones activas alcanza el máximo configurado (el valor predeterminado es 10.000) |Espere a que finalice los trabajos actualmente en ejecución o aumente el límite de trabajos simultáneos modificando `yarn.scheduler.capacity.maximum-applications`. Consulte [Modificación de la configuración](#modifying-configuration) para más información. |
| Al intentar recuperar todos los trabajos a través de la llamada [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) mientras `Fields` está establecido en `*` |No recupere *todos* los detalles del trabajo. En su lugar, use `jobid` para recuperar detalles de trabajos solo mayores que un id. de trabajo determinado. O bien, no use `Fields` |
| El servicio de WebHCat está inactivo durante la conmutación por error del nodo principal |Espere dos minutos y vuelva a intentar la operación |
| Hay más de 500 trabajos pendientes enviados a través de WebHCat |Espere hasta que hayan finalizado los trabajos pendientes actualmente antes de enviar más trabajos |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
