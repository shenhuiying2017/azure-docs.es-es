<properties
   pageTitle="Índice de la guía técnica sobre resistencia | Microsoft Azure"
   description="Índice de artículos técnicos para entender y diseñar aplicaciones resistentes, con alta disponibilidad y con tolerancia a errores, así como para planear la recuperación ante desastres y la continuidad empresarial"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#Guía técnica sobre resistencia en Azure

##Introducción

El cumplimiento de los requisitos alta disponibilidad y recuperación ante desastres requiere dos tipos de conocimientos:

- Conocimiento técnico detallado de las funcionalidades de una plataforma de nube
- Conocimiento de cómo diseñar correctamente un servicio distribuido

En esta serie de artículos se trata el primer tipo: las funcionalidades y limitaciones de la plataforma de Azure en relación con la resistencia (que a veces también se denomina continuidad empresarial). Si está interesado en el segundo tipo de conocimientos, consulte la serie de artículos que se centran en la [recuperación ante desastres y alta disponibilidad para aplicaciones creadas en Microsoft Azure](https://aka.ms/drtechguide). Aunque en esta serie de artículos se tocan la arquitectura y los patrones de diseño, no se centran en esos temas. Para obtener una guía de diseño, puede consultar el material de la sección [Recursos adicionales](#additional-resources).

La información se organiza en los siguientes artículos:

- [Recuperación ante errores locales](resiliency-technical-guidance-recovery-local-failures.md). Se pueden producir errores en el hardware físico (por ejemplo, unidades, servidores y dispositivos de red). Los recursos se pueden agotar cuando haya picos de carga. En este artículo se describen las funcionalidades que proporciona Azure para mantener una alta disponibilidad en estas condiciones.

- [Recuperación ante una interrupción del servicio en toda la región de Azure](resiliency-technical-guidance-recovery-loss-azure-region.md). Los errores generalizados son muy poco frecuentes, pero en teoría pueden producirse. Regiones completas pueden quedar aisladas debido a errores en la red o pueden sufrir daños físicos debido a desastres naturales. En este artículo se explica cómo usar Azure para crear aplicaciones que abarquen varias regiones geográficas.

- [Recuperación desde un centro de datos local a Azure](resiliency-technical-guidance-recovery-on-premises-azure.md). La nube altera considerablemente la economía de la recuperación ante desastres, lo que permite que las organizaciones usen Azure para establecer un segundo sitio para la recuperación. Esto se puede hacer a una fracción del costo que supondría crear y mantener un centro de datos secundario. En este artículo se explican las funcionalidades que proporciona Azure para extender un centro de datos local a la nube.

- [Recuperación ante datos dañados o eliminación accidental](resiliency-technical-guidance-recovery-data-corruption.md). Las aplicaciones pueden tener errores que dañen los datos. Los operadores pueden eliminar incorrectamente datos importantes. En este artículo se explica lo que ofrece Azure para realizar copias de seguridad de datos y su restauración a un momento dado anterior.

##Recursos adicionales

- [Recuperación ante desastres y alta disponibilidad para aplicaciones creadas en Microsoft Azure](resiliency-disaster-recovery-high-availability-azure-applications.md). En este artículo se incluye información general detallada sobre la disponibilidad y la recuperación ante desastres. Trata acerca del desafío de la replicación manual de datos de referencia y transaccionales. Las secciones finales proporcionan resúmenes de distintos tipos de topologías de recuperación ante desastres que abarcan varias regiones de Azure para conseguir el nivel más alto de disponibilidad.

- [High-availability checklist](resiliency-high-availability-checklist.md) (Lista de comprobación de alta disponibilidad). En este artículo se incluye una lista de características, servicios y diseños que se pueden ayudarle a aumentar la resistencia y disponibilidad de cualquier aplicación.

- [Información orientativa acerca de la resistencia del servicio de Microsoft Azure](resiliency-service-guidance-index.md). En este artículo se incluye un índice de los servicios de Azure y se proporcionan vínculos tanto a la guía para recuperación ante desastres como a la guía de diseño.

- [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](../sql-database/sql-database-business-continuity.md). En este artículo se especifican las técnicas de Base de datos SQL de Azure para lograr disponibilidad. Se centra principalmente en las estrategias de copia de seguridad y restauración. Si va a utilizar Base de datos SQL de Azure en el servicio en la nube, debe revisar este documento y los recursos relacionados.

- [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md). En este artículo se describen las opciones de disponibilidad que se pueden explorar cuando se utiliza la infraestructura como servicio (IaaS) para hospedar servicios de base de datos. Describe los grupos de disponibilidad AlwaysOn, la creación de reflejo de la base de datos, el trasvase de registros y la copia de seguridad y la restauración. Varios tutoriales muestran cómo utilizar estas técnicas.

- [Best Practices for designing large-scale services on Azure Cloud Services](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/) (Procedimientos recomendados para el diseño de servicios a gran escala en Servicios en la nube de Azure). Este artículo se centra en el desarrollo de arquitecturas en la nube altamente escalables. Muchas de las técnicas que se emplean para mejorar la escalabilidad también mejoran la disponibilidad. Además, si la aplicación no se puede escalar cuando aumenta la carga, la escalabilidad pasará a ser un problema de disponibilidad.

- [Copias de seguridad y restauración para SQL Server en Máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md). En este artículo se proporciona una guía técnica para realizar copias de seguridad y restauraciones de Microsoft SQL Server cuando se ejecuta en Máquinas virtuales de Azure.

- [Failsafe: Guidance for resilient cloud architectures](https://channel9.msdn.com/Series/FailSafe) (Failsafe: guía para arquitecturas en la nube resistentes). En este artículo se proporcionan instrucciones para la creación de arquitecturas resistentes en la nube y para implementar dichas arquitecturas en tecnologías de Microsoft y en escenarios específicos.

- [Technical case study: Using cloud technologies to improve disaster recovery](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery) (Caso técnico práctico: uso de tecnologías de la nube para mejorar la recuperación ante desastres). Este caso práctico muestra la forma en que el equipo de TI de Microsoft ha usado Azure para mejorar la recuperación ante desastres.

##Pasos siguientes

Este artículo forma parte de una serie que se centra en la guía técnica sobre la resistencia en Azure. Si está interesado en leer otros artículos de esta serie, puede empezar por [Guía técnica sobre resistencia en Azure: recuperación ante errores locales en Azure](resiliency-technical-guidance-recovery-local-failures.md).

<!---HONumber=AcomDC_0824_2016-->