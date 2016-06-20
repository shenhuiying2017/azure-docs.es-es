<properties
   pageTitle="Índice de la guía técnica sobre resistencia | Microsoft Azure"
   description="Índice de artículos técnicos para entender y diseñar aplicaciones resistentes, con alta disponibilidad y con tolerancia a errores y para planear la recuperación ante desastres y la continuidad empresarial"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Guía técnica sobre resistencia en Azure

##Introducción
El cumplimiento de los requisitos de recuperación ante desastres y alta disponibilidad requiere dos tipos de conocimientos: 1) conocimientos técnicos detallados de las funcionalidades de una plataforma en la nube y 2) conocimientos sobre cómo diseñar correctamente un servicio distribuido. Esta serie de artículos trata acerca del primer tipo: funcionalidades y limitaciones de la plataforma de Azure en relación con la resistencia (a veces también referida como continuidad empresarial). Si está interesado en el segundo tipo de conocimientos, consulte la serie de artículos que se centra en [Recuperación ante desastres y alta disponibilidad para aplicaciones de Azure](https://aka.ms/drtechguide). Aunque esta serie de artículos trata sobre patrones de arquitectura y diseño, este no es el centro de atención principal de esta serie. El lector debe consultar el material descrito en la sección de [recursos adicionales](#additional-resources) para obtener una guía sobre el diseño.

La información se organiza en los siguientes artículos:
##[Recuperación ante errores locales](resiliency-technical-guidance-recovery-local-failures.md)
Los componentes físicos del hardware (por ejemplo, unidades, servidores y dispositivos de red) pueden dar error y los recursos se pueden agotar en un pico de carga. Esta sección describe las funcionalidades que Azure proporciona para mantener una alta disponibilidad en estas condiciones.

##[Recuperación ante una interrupción del servicio en toda la región de Azure](resiliency-technical-guidance-recovery-loss-azure-region.md)
Los errores generalizados son poco frecuentes, pero posibles en teoría. Regiones enteras pueden quedar aisladas debido a errores en la red o dañadas físicamente debido a desastres naturales. Esta sección explica cómo usar las funcionalidades de Azure para crear aplicaciones que abarquen varias regiones geográficas.

##[Recuperación desde una instalación local a Azure](resiliency-technical-guidance-recovery-on-premises-azure.md)
La nube altera significativamente la economía de la recuperación ante desastres, lo que permite que las organizaciones puedan usar Azure para establecer un segundo sitio para la recuperación. Esto puede hacerse a una fracción del costo que supondría crear y mantener un centro de datos secundario. Esta sección explica las funcionalidades que Azure proporciona para extender un centro de datos local a la nube.

##[Recuperación ante datos dañados o eliminación accidental](resiliency-technical-guidance-recovery-data-corruption.md)
Las aplicaciones pueden experimentar errores que dañen los datos y los operadores pueden eliminar datos importantes de forma incorrecta. Esta sección explica las funcionalidades que ofrece Azure para realizar copias de seguridad y restauración a un estado anterior en el tiempo.

##Recursos adicionales

###[Guía técnica sobre la continuidad empresarial en Azure](resiliency-technical-guidance.md)
En este artículo se describe un listado detallado de los conceptos y procedimientos recomendados para mantener la continuidad empresarial y la resistencia mediante Microsoft Azure para sus aplicaciones locales, híbridas o en la nube pública.

###[Recuperación ante desastres y alta disponibilidad para aplicaciones creadas en Microsoft Azure](resiliency-disaster-recovery-high-availability-azure-applications.md)
En este artículo se incluye información general detallada sobre la disponibilidad y la recuperación ante desastres. Trata acerca del desafío de la replicación manual de datos de referencia y transaccionales. Las secciones finales proporcionan resúmenes de distintos tipos de topologías de recuperación ante desastres que abarcan varias regiones de Azure para conseguir el nivel más alto de disponibilidad.

###[High availability checklist](resiliency-high-availability-checklist.md) (Lista de comprobación de alta disponibilidad)
En este artículo se incluye una lista de características, servicios y diseños que se van a usar para aumentar la resistencia y la disponibilidad de la aplicación.

###[Información orientativa acerca de la resistencia del servicio de Microsoft Azure](resiliency-service-guidance-index.md)
En este artículo se incluye un índice de servicios de Azure y vínculos a sus instrucciones para recuperación ante desastres, así como instrucciones de diseño.

###[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](../sql-database/sql-database-business-continuity.md)
Este artículo se centra exclusivamente en las técnicas de Base de datos SQL de Azure para obtener disponibilidad, basadas principalmente en estrategias de copia de seguridad y restauración. Si va a utilizar Base de datos SQL de Azure en el servicio en la nube, debe revisar este documento y los recursos relacionados.

###[Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)
En este artículo se describen las opciones de disponibilidad a las que puede acceder cuando utiliza la infraestructura como servicio (IaaS) para hospedar los servicios de base de datos. Describe los grupos de disponibilidad AlwaysOn, la creación de reflejo de la base de datos, el trasvase de registros y la copia de seguridad y la restauración. Tenga en cuenta que también hay varios tutoriales en la misma sección que muestran cómo utilizar estas técnicas.

###[Best Practices for designing large-scale services on Azure Cloud Services (Procedimientos recomendados para el diseño de servicios a gran escala en Servicios en la nube de Azure)](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/)
Este artículo se centra en el desarrollo de arquitecturas en la nube altamente escalables. Muchas de las técnicas que se emplean para mejorar la escalabilidad también mejoran la disponibilidad. Además, si la aplicación no puede escalarse en los momentos de mayor carga, se generará un problema de disponibilidad.

###[Copias de seguridad y restauración para SQL Server en Máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md)
En este artículo se proporciona una guía técnica para realizar la copia de seguridad y restauración de Microsoft SQL Server que se ejecuta en Máquinas virtuales de Azure.

###[Failsafe: Guidance for resilient cloud architectures (Failsafe: Guía para las arquitecturas resistentes en la nube)](https://channel9.msdn.com/Series/FailSafe)
En este artículo se proporcionan instrucciones para la creación de arquitecturas resistentes en la nube y para implementar dichas arquitecturas en tecnologías de Microsoft y en escenarios específicos.

###[Technical case study: Using cloud technologies to improve disaster recovery](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery) (Caso técnico de uso: uso de tecnologías de nube para mejorar la recuperación ante desastres)
Este artículo es un caso técnico de uso que muestra cómo Microsoft IT usa Azure para mejorar la recuperación ante desastres.

##Pasos siguientes
Este artículo forma parte de una serie que se centra en la [Guía técnica sobre resistencia en Azure](resiliency-technical-guidance.md). Si está interesado en leer otros artículos de esta serie, empiece con el siguiente artículo titulado [Guía técnica sobre resistencia en Azure: recuperación ante errores locales en Azure](resiliency-technical-guidance-recovery-local-failures.md).

<!---HONumber=AcomDC_0608_2016-->