<properties
   pageTitle="Preguntas más frecuentes sobre la versión de vista previa pública del servicio Copia de seguridad de Azure | Microsoft Azure"
   description="Esta versión de las preguntas más frecuentes es compatible con la versión de vista previa pública del servicio Copia de seguridad de Azure. Respuestas a las preguntas más frecuentes sobre agente de copia de seguridad, copia de seguridad y retención, recuperación, seguridad y otras preguntas comunes sobre la solución de Copia de seguridad de Azure."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="solución de copia de seguridad; servicio de copia de seguridad"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="07/01/2016"
	 ms.author="trinadhk; markgal; jimpark;"/>

# Versión de vista previa pública del servicio Copia de seguridad de Azure: preguntas más frecuentes

> [AZURE.SELECTOR]
- [Preguntas más frecuentes acerca del servicio Copia de seguridad para el modo clásico](backup-azure-backup-faq.md)
- [Preguntas más frecuentes acerca del servicio Copia de seguridad para el modo ARM](backup-azure-backup-ibiza-faq.md)

Este artículo proporciona información específica sobre la versión de vista previa pública del servicio Copia de seguridad de Azure. Este artículo se actualizará cuando lleguen nuevas preguntas frecuentes y complementa la sección de [preguntas más frecuentes de Copia de seguridad de Azure](backup-azure-backup-faq). La sección de preguntas más frecuentes acerca de Copia de seguridad de Azure proporciona el conjunto completo de preguntas y respuestas sobre este servicio.

Las preguntas acerca de Copia de seguridad de Azure se pueden realizar en la sección Disqus de este artículo o de otros artículos relacionados. También se pueden publicar preguntas sobre el servicio Copia de seguridad de Azure en el [foro de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## ¿Qué hay en la versión de vista previa pública?
La versión de vista previa pública presenta el almacén de Servicios de recuperación y la compatibilidad con ARM a la hora de proteger las máquinas virtuales de Azure. El almacén de Servicios de recuperación constituye la última generación de almacenes. Es el almacén utilizado por los servicios Copia de seguridad de Azure y Azure Site Recovery (ASR). Considérelo como el almacén de la versión v.2.

## Almacenes de Servicios de recuperación y Copia de seguridad

**P1. Los almacenes de Servicios de recuperación son de la versión v.2, ¿son todavía compatibles los almacenes de Copia de seguridad (versión v.1)?** <br/> R1. Sí, los almacenes de Copia de seguridad son todavía compatibles. Cree almacenes de Copia de seguridad en el portal de clásico. Cree almacenes de Servicios de recuperación en el Portal de Azure.

**P2. ¿Puedo migrar un almacén de Copia de seguridad a un almacén de Servicios de recuperación?** <br/> R2. Por desgracia no, en este momento no puede migrar el contenido de un almacén de Copia de seguridad a un almacén de Servicios de recuperación. Estamos trabajando para agregar esta funcionalidad, pero no está disponible como parte de la versión previa pública.

**P3. ¿Son compatibles los almacenes de Servicios de recuperación con las máquinas virtuales de las versiones v.1 o v.2?** <br/> R3. Los almacenes de Servicios de recuperación son compatibles con las máquinas virtuales de las versiones v.1 o v.2. Puede hacer copias de seguridad de una máquina virtual creada en el portal de clásico (de la versión V.1) o de una máquina virtual creada en el Portal de Azure (de la versión V.2) en un almacén de Servicios de recuperación.


## Compatibilidad de ARM con las máquinas virtuales de Azure

**P1. ¿Existen limitaciones en cuanto a la compatibilidad de ARM con las máquinas virtuales de Azure?** <br/> R1. Los cmdlets de PowerShell para ARM no están actualmente disponibles. Debe utilizar la interfaz de usuario del Portal de Azure para agregar recursos a un grupo de recursos.

<!---HONumber=AcomDC_0706_2016-->