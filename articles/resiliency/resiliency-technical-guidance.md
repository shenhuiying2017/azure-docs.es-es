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
##[Recuperación ante errores locales](./resiliency-technical-guidance-recovery-local-failures.md)
Los componentes físicos del hardware (por ejemplo, unidades, servidores y dispositivos de red) pueden dar error y los recursos se pueden agotar en un pico de carga. Esta sección describe las funcionalidades que Azure proporciona para mantener una alta disponibilidad en estas condiciones.

##[Recuperación ante una interrupción del servicio en toda la región de Azure](./resiliency-technical-guidance-recovery-loss-azure-region.md)
Los errores generalizados son poco frecuentes, pero posibles en teoría. Regiones enteras pueden quedar aisladas debido a errores en la red o dañadas físicamente debido a desastres naturales. Esta sección explica cómo usar las funcionalidades de Azure para crear aplicaciones que abarquen varias regiones geográficas.

##[Recuperación desde una instalación local a Azure](./resiliency-technical-guidance-recovery-on-premises-azure.md)
La nube altera significativamente la economía de la recuperación ante desastres, lo que permite que las organizaciones puedan usar Azure para establecer un segundo sitio para la recuperación. Esto puede hacerse a una fracción del costo que supondría crear y mantener un centro de datos secundario. Esta sección explica las funcionalidades que Azure proporciona para extender un centro de datos local a la nube.

##[Recuperación ante datos dañados o eliminación accidental](./resiliency-technical-guidance-recovery-data-corruption.md)
Las aplicaciones pueden experimentar errores que dañen los datos y los operadores pueden eliminar datos importantes de forma incorrecta. Esta sección explica las funcionalidades que ofrece Azure para realizar copias de seguridad y restauración a un estado anterior en el tiempo.

##Recursos adicionales

###[Guía técnica sobre la continuidad empresarial en Azure](./resiliency-technical-guidance.md)
Un listado detallado de los conceptos y procedimientos recomendados para mantener la continuidad empresarial y la resistencia mediante Microsoft Azure para sus aplicaciones locales, híbridas o en la nube pública.

###[Recuperación ante desastres y alta disponibilidad para aplicaciones creadas en Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md)
Información general detallada acerca de la disponibilidad y la recuperación ante desastres. Trata acerca del desafío de la replicación manual de datos de referencia y transaccionales. Las secciones finales proporcionan resúmenes de distintos tipos de topologías de recuperación ante desastres que abarcan varias regiones de Azure para conseguir el nivel más alto de disponibilidad.

###[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](../sql-database/sql-database-business-continuity.md)
Se centra exclusivamente en las técnicas de Base de datos SQL de Azure para obtener disponibilidad, basadas principalmente en estrategias de copia de seguridad y restauración. Si va a utilizar Base de datos SQL de Azure en el servicio en la nube, debe revisar este documento y los recursos relacionados.

###[Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)
Describe las opciones de disponibilidad disponibles para usted cuando utiliza la infraestructura como servicio (IaaS) para hospedar los servicios de base de datos. Describe los grupos de disponibilidad AlwaysOn, la creación de reflejo de la base de datos, el trasvase de registros y la copia de seguridad y la restauración. Tenga en cuenta que también hay varios tutoriales en la misma sección que muestran cómo utilizar estas técnicas.

###[Best Practices for designing large-scale services on Azure Cloud Services](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/) (Procedimientos recomendados para el diseño de servicios a gran escala en Servicios en la nube de Azure)
Se centra en el desarrollo de arquitecturas en la nube altamente escalables. Muchas de las técnicas que se emplean para mejorar la escalabilidad también mejoran la disponibilidad. Además, si la aplicación no puede escalarse en los momentos de mayor carga, se generará un problema de disponibilidad.

###[Copias de seguridad y restauración para SQL Server en Máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md)
Guía técnica para realizar copias de seguridad y restauración en una instancia de Microsoft SQL Server en ejecución en Máquinas virtuales de Azure.

###[Failsafe: Guidance for resilient cloud architectures](https://channel9.msdn.com/Series/FailSafe) (Failsafe: Guía para las arquitecturas resistentes en la nube)
Guía para la creación de arquitecturas resistentes en la nube y para implementar dichas arquitecturas en tecnologías de Microsoft y en escenarios específicos.

##Pasos siguientes
Este artículo forma parte de una serie que se centra en la [Guía técnica sobre resistencia en Azure](./resiliency-technical-guidance.md). Si está interesado en leer otros artículos de esta serie, empiece con el siguiente artículo titulado [Recuperación ante errores locales](./resiliency-technical-guidance-recovery-local-failures.md).

<!---HONumber=AcomDC_0525_2016-->