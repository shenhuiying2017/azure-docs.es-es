---
title: 'Actualizaciones graduales de aplicaciones: Azure SQL Database | Microsoft Docs'
description: "Aprenda a usar la replicación geográfica en Base de datos SQL para admitir las actualizaciones en línea de la aplicación en la nube."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 58f42859-1e37-463c-a3d8-a3ca2e867148
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/16/2016
ms.author: sashan
ms.openlocfilehash: 4b59c8aa3dea3e8fba692ab66420295a09210d3b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Administración de actualizaciones graduales de aplicaciones en la nube mediante la replicación geográfica activa de SQL Database
> [!NOTE]
> La [replicación geográfica activa](sql-database-geo-replication-overview.md) ahora está disponible para todas las bases de datos en todos los niveles.
> 

Aprenda a usar la [replicación geográfica](sql-database-geo-replication-overview.md) en SQL Database para permitir actualizaciones graduales de la aplicación en la nube. Como la actualización es una operación problemática, pues debe ser parte del diseño y planeamiento de continuidad. En este artículo veremos dos métodos diferentes de organizar el proceso de actualización y comentaremos las ventajas y desventajas de cada opción. Para este artículo, usaremos una aplicación simple que consta de un sitio web conectado a una base de datos única como su capa de datos. Nuestro objetivo consiste en actualizar la versión 1 de la aplicación a la versión 2 sin que se observe ningún impacto importante en la experiencia del usuario final. 

Al evaluar las opciones de actualización, se deben considerar los factores siguientes:

* El impacto sobre la disponibilidad de las aplicaciones durante las actualizaciones. Durante cuánto tiempo la función de aplicación debe estar limitada o degradada.
* La capacidad para deshacer el proceso en caso de errores durante la actualización.
* La vulnerabilidad de la aplicación si se produce un error grave no relacionado durante la actualización.
* El costo total en dólares.  Incluye redundancia adicional y costos incrementales de los componentes temporales usados en el proceso de actualización. 

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>La actualización de las aplicaciones que dependen de las copias de seguridad de base de datos para recuperación ante desastres.
Si la aplicación se basa en copias de seguridad automáticas y utiliza la restauración geográfica para la recuperación ante desastres, normalmente se implementa en una única región de Azure. En este caso el proceso de actualización implica la creación de una implementación de copia de seguridad de todos los componentes de la aplicación implicados en la actualización. Para minimizar la interrupción para el usuario final, se aprovechará del Administrador de tráfico de Azure (WATM) con el perfil de conmutación por error.  En el siguiente diagrama se ilustra el entorno operativo antes del proceso de actualización. El punto de conexión <i>contoso-1.azurewebsites.net</i> representa una ranura de producción de la aplicación que debe actualizarse. Para habilitar la capacidad de revertir la actualización, necesita crear un espacio de almacenamiento provisional con una copia totalmente sincronizada de la aplicación. Los siguientes pasos son necesarios para preparar la aplicación para la actualización:

1. Cree una ranura de almacenamiento provisional para la actualización. Para ello, cree una base de datos secundaria (1) e implemente un sitio web idéntico en la misma región de Azure. Supervise la base de datos secundaria para ver si se completa el proceso de propagación.
2. Cree un perfil de conmutación por error en WATM con <i>contoso-1.azurewebsites.net</i> como punto de conexión en línea y <i>contoso 2.azurewebsites.net</i> como punto de conexión desconectado. 

> [!NOTE]
> Tenga en cuenta los pasos preparatorios no afectarán a la aplicación de la ranura de producción y puede funcionar en modo de acceso completo.
>  

![Configuración de la replicación geográfica de la Base de datos SQL. Recuperación ante desastres en la nube.](media/sql-database-manage-application-rolling-upgrade/Option1-1.png)

Una vez completados los pasos preparatorios, la aplicación está preparada para la actualización real. En el siguiente diagrama se ilustran los pasos implicados en el proceso de actualización. 

1. Establezca la base de datos principal en la ranura de producción en modo de solo lectura (3). Esto garantizará que la instancia de producción de la aplicación (V1) seguirá siendo de solo lectura durante la actualización, con lo que se impide la divergencia de datos entre las instancias de base de datos de V1 y V2.  
2. Desconecte la base de datos secundaria con el modo de finalización planeada (4). Se creará una copia independiente totalmente sincronizada de la base de datos principal. Esta base de datos se actualizará.
3. Cambie la base de datos principal al modo de lectura y escritura, y ejecute el script de actualización en la ranura de almacenamiento provisional (5).     

![Configuración de replicación geográfica de SQL Database. Recuperación ante desastres en la nube.](media/sql-database-manage-application-rolling-upgrade/Option1-2.png)

Si la actualización se completó correctamente, ya está listo para cambiar los usuarios finales a la copia almacenada provisionalmente. Ahora se convertirá en la ranura de producción de la aplicación.  Esto implica unos pocos pasos más, tal como se muestra en el diagrama siguiente.

1. Cambie el punto de conexión en línea en el perfil de WATM a <i>contoso-2.azurewebsites.net</i>, que señala a la versión V2 del sitio web (6). Ahora se convierte en el espacio de producción con la aplicación V2 y el tráfico de usuarios finales se dirige a él.  
2. Si ya no necesita los componentes de la aplicación V1, puede quitarlos (7).   

![Configuración de replicación geográfica de SQL Database. Recuperación ante desastres en la nube.](media/sql-database-manage-application-rolling-upgrade/Option1-3.png)

Si el proceso de actualización es incorrecto, por ejemplo debido a un error en el script de actualización, la ranura de almacenamiento provisional debe considerarse en peligro. Para revertir la aplicación al estado previo a la actualización, simplemente revierta la aplicación en el espacio de producción al acceso completo. En el diagrama siguiente se muestran los pasos implicados.    

1. Establezca la copia de la base de datos en modo de lectura y escritura (8). Esto restaurará la funcionalidad de V1 completa en la ranura de producción.
2. Realice el análisis de causa raíz y quite los componentes en peligro de la ranura de almacenamiento provisional (9). 

En este punto, la aplicación es totalmente funcional y se pueden repetir los pasos de actualización.

> [!NOTE]
> La reversión no requiere cambios en el perfil de WATM, ya que apunta a <i>contoso-1.azurewebsites.net</i> como el punto de conexión activo.
> 
> 

![Configuración de replicación geográfica de SQL Database. Recuperación ante desastres en la nube.](media/sql-database-manage-application-rolling-upgrade/Option1-4.png)

La principal **ventaja** de esta opción es que se puede actualizar una aplicación en una sola región mediante un conjunto de pasos sencillos. El costo de la actualización es relativamente bajo. La principal **contrapartida** es que si se produce un error grave durante la actualización, la recuperación al estado previo a la actualización implicará que haya que volver a implementar la aplicación en una región distinta y restaurar la base de datos desde la copia de seguridad mediante la restauración geográfica. Este proceso dará como resultado un tiempo de inactividad considerable.   

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Actualización de aplicaciones que dependen de la replicación geográfica para la recuperación ante desastres
Si la aplicación aprovecha la replicación geográfica para la continuidad empresarial, se implementa en al menos dos regiones diferentes con una implementación activa en la región primaria y una implementación en espera en la región de copia de seguridad. Además de los factores mencionados anteriormente, el proceso de actualización debe garantizar que:

* La aplicación permanece protegida frente a errores catastróficos en todo momento durante el proceso de actualización.
* Los componentes con redundancia geográfica de la aplicación se actualizan en paralelo con los componentes activos.

Para lograr estos objetivos, aprovechará el Administrador de tráfico de Azure (WATM) con el perfil de conmutación por error con un punto de conexión activo y tres de copia de seguridad.  En el siguiente diagrama se ilustra el entorno operativo antes del proceso de actualización. Los sitios web <i>contoso-1.azurewebsites.net</i> y <i>contoso-dr.azurewebsites.net</i> representan una ranura de producción de la aplicación con redundancia geográfica completa. Para habilitar la capacidad de revertir la actualización, necesita crear un espacio de almacenamiento provisional con una copia totalmente sincronizada de la aplicación. Dado que debe asegurarse de que la aplicación puede recuperarse rápidamente en caso de que se produzca un error grave durante el proceso de actualización, el espacio de almacenamiento provisional tiene que tener también redundancia geográfica. Los siguientes pasos son necesarios para preparar la aplicación para la actualización:

1. Cree una ranura de almacenamiento provisional para la actualización. Para ello, cree una base de datos secundaria (1) e implemente una copia idéntica del sitio web en la misma región de Azure. Supervise la base de datos secundaria para ver si se completa el proceso de propagación.
2. Cree una base de datos secundaria con redundancia geográfica en la ranura de almacenamiento provisional mediante la replicación geográfica de la base de datos secundaria a la región de copia de seguridad (esto se denomina "replicación geográfica encadenada"). Supervise la copia de seguridad secundaria para ver si se completa el proceso de propagación (3).
3. Cree una copia en espera del sitio web de la región de copia de seguridad y vincúlela a la base de datos secundaria con redundancia geográfica (4).  
4. Agregue los puntos de conexión adicionales <i>contoso-2.azurewebsites.net</i> y <i>contoso-3.azurewebsites.net</i> al perfil de conmutación por error de WATM como puntos de conexión desconectados (5). 

> [!NOTE]
> Tenga en cuenta los pasos preparatorios no afectarán a la aplicación de la ranura de producción y puede funcionar en modo de acceso completo.
> 
> 

![Configuración de replicación geográfica de SQL Database. Recuperación ante desastres en la nube.](media/sql-database-manage-application-rolling-upgrade/Option2-1.png)

Una vez completados los pasos preparatorios, la ranura de almacenamiento provisional estará preparada para la actualización. En el siguiente diagrama se ilustran los pasos de actualización:

1. Establezca la base de datos principal en la ranura de producción en modo de solo lectura (6). Esto garantizará que la instancia de producción de la aplicación (V1) seguirá siendo de solo lectura durante la actualización, con lo que se impide la divergencia de datos entre las instancias de base de datos de V1 y V2.  
2. Desconecte la base de datos secundaria de la misma región con el modo de finalización planeada (7). Se creará una copia independiente totalmente sincronizada de la base de datos principal, que se convertirá automáticamente en principal después de la finalización. Esta base de datos se actualizará.
3. Cambie la base de datos principal de la ranura de almacenamiento provisional en modo de lectura y escritura, y ejecute el script de actualización (8).    

![Configuración de replicación geográfica de SQL Database. Recuperación ante desastres en la nube.](media/sql-database-manage-application-rolling-upgrade/Option2-2.png)

Si la actualización se completó correctamente, ya está listo para cambiar los usuarios finales a la versión V2 de la aplicación. En el siguiente diagrama se ilustran los pasos implicados.

1. Cambie el punto de conexión activo en el perfil de WATM a <i>contoso-2.azurewebsites.net</i>, que ahora señala a la versión V2 del sitio web (9). Ahora se convierte en un espacio de producción con la aplicación V2 y el tráfico de usuarios finales se dirige a ella. 
2. Si ya no necesita la aplicación V1, puede quitarla con seguridad (10 y 11).  

![Configuración de replicación geográfica de SQL Database. Recuperación ante desastres en la nube.](media/sql-database-manage-application-rolling-upgrade/Option2-3.png)

Si el proceso de actualización es incorrecto, por ejemplo debido a un error en el script de actualización, la ranura de almacenamiento provisional debe considerarse en peligro. Para revertir la aplicación al estado previo a la actualización, simplemente reviértala para usar la aplicación en el espacio de producción con acceso completo. En el diagrama siguiente se muestran los pasos implicados.    

1. Establezca la base de datos principal en la ranura de producción en modo de lectura y escritura (12). Esto restaurará la funcionalidad de V1 completa en la ranura de producción.
2. Realice el análisis de causa raíz y quite los componentes en peligro de la ranura de almacenamiento provisional (13 y 14). 

En este punto, la aplicación es totalmente funcional y se pueden repetir los pasos de actualización.

> [!NOTE]
> La reversión no requiere cambios en el perfil de WATM, ya que apunta a <i>contoso-1.azurewebsites.net</i> como el punto de conexión activo.
> 
> 

![Configuración de replicación geográfica de SQL Database. Recuperación ante desastres en la nube.](media/sql-database-manage-application-rolling-upgrade/Option2-4.png)

La principal **ventaja** de esta opción es que puede actualizar tanto la aplicación como su copia con redundancia geográfica en paralelo, sin poner en peligro la continuidad del negocio durante la actualización. La principal **contrapartida** es que requiere redundancia doble de cada componente de aplicación y, por tanto, implica un mayor costo. También implica un flujo de trabajo más complicado. 

## <a name="summary"></a>Resumen
Los dos métodos de actualización que se describen en el artículo difieren en complejidad y en costo, pero ambos se centran en minimizar el tiempo en que el usuario final está limitado a las operaciones de solo lectura. La duración del script de actualización define directamente ese tiempo. No depende del tamaño de la base de datos, del nivel de servicio que haya seleccionado, de la configuración del sitio web y de otros factores que no se pueden controlar fácilmente. Esto es porque todos los pasos preparatorios se desacoplan de los pasos de actualización y pueden realizarse sin que afecten a la aplicación de producción. La eficacia del script de actualización es el factor clave que determina la experiencia del usuario final durante las actualizaciones. Por tanto, la mejor manera de que pueda mejorarlo es centrar sus esfuerzos en hacer que el script de actualización sea lo más eficiente posible.  

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md).
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte cómo [restaurar una base de datos a partir de las copias de seguridad automatizadas](sql-database-recovery-using-backups.md).
* Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).


