<properties 
	pageTitle="Administración de Copia de seguridad de Azure con Automatización de Azure" 
	description="Obtenga información acerca de cómo el servicio de Automatización de Azure se puede usar para administrar Copia de seguridad de Azure." 
	services="backup, automation" 
	documentationCenter="" 
	authors="eamonoreilly" 
	manager="jwinter" 
	editor=""/>

<tags 
	ms.service="backup" 
	ms.workload="storage-backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="eamono"/>


#Administración de Copia de seguridad de Azure con Automatización de Azure

Esta guía presenta el servicio Automatización de Azure y cómo se puede usar para simplificar la administración de Copia de seguridad de Azure.

## ¿Qué es Automatización de Azure?

[Automatización de Azure](http://azure.microsoft.com/services/automation/) es un servicio de Azure para simplificar la administración en la nube mediante la automatización de procesos. Mediante Automatización de Azure, se pueden automatizar las tareas de ejecución prolongada, manuales, propensas a errores y que se repiten con frecuencia para aumentar la confiabilidad, la eficiencia y el valioso tiempo para su organización.

Automatización de Azure proporciona un motor de ejecución de flujo de trabajo altamente confiable y de alta disponibilidad que realiza la escalación para satisfacer sus necesidades a medida que crece la organización. En Automatización de Azure, los sistemas de terceros pueden interrumpir los procesos manualmente o en intervalos programados para que las tareas se realicen justo cuando sea necesario.

Reduzca la sobrecarga operativa y libere al personal de TI/DevOps para concentrarse en el trabajo que proporciona valor al negocio trasladando las tareas de administración en la nube para que se ejecuten automáticamente mediante Automatización de Azure.


## ¿Cómo puede ayudar Automatización de Azure a administrar Copia de seguridad de Azure?

Copia de seguridad se puede administrar en Automatización de Azure mediante los cmdlets de PowerShell que están disponibles en el [módulo MSOnlineBackup de Windows](https://technet.microsoft.com/es-es/library/hh770400.aspx). Automatización de Azure puede llamar a estos cmdlets de PowerShell, por lo que es posible realizar todas las tareas de administración de Copia de seguridad dentro del servicio. También puede emparejar estos cmdlets en Automatización de Azure con los cmdlets para otros servicios de Azure, para automatizar tareas complejas a través de los servicios de Azure y sistemas de terceros.


## Pasos siguientes

Ahora que ha aprendido los aspectos básicos de Automatización de Azure y cómo se puede usar para administrar Copia de seguridad de Azure, siga estos vínculos para obtener más información acerca de Automatización de Azure.

* Consulte la [Guía de introducción](http://go.microsoft.com/fwlink/?LinkId=390560) a Automatización de Azure.
 

<!---HONumber=62-->