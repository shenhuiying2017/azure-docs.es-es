<properties
	pageTitle="¿Qué es Automatización de Azure?"
	description="Conozca qué valor proporciona Automatización de Azure y obtenga respuestas a preguntas habituales para comenzar a crear y usar runbooks."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="09/17/2015"
	ms.author="bwren"/>

# ¿Qué es Automatización de Azure?

Automatización de Microsoft Azure ofrece a los usuarios una manera de automatizar las tareas manuales, propensas a errores, con una ejecución prolongada y repetidas con frecuencia que se realizan normalmente en un entorno empresarial y en la nube. En este artículo se ofrecen respuestas breves a preguntas habituales sobre Automatización de Azure. Puede consultar otros artículos de esta biblioteca para obtener información más detallada sobre los diferentes temas.

## ¿Qué valor ofrece Automatización de Azure?

Automatización de Azure ahorra tiempo y aumenta la confiabilidad de las tareas administrativas periódicas que se realizan en entornos empresariales y en la nube. Puede implementar estos procesos como runbooks que pueden realizar varias tareas sin intervención humana e incluso programar que se realicen automáticamente a intervalos regulares.

## ¿Qué es un runbook?

Un runbook es un conjunto de tareas que realizan algún proceso automatizado en Automatización de Azure. Puede ser un proceso sencillo como iniciar una máquina virtual o crear una entrada de registro, o puede ser un runbook complejo que combina otros runbooks más pequeños para llevar a cabo un proceso complejo en varios recursos o incluso en varias nubes.

Por ejemplo, podría tener un proceso manual existente para aprovisionar una nueva máquina virtual que incluye varios pasos, como crear la máquina virtual, conectarla a una red, asignarle una dirección IP y, a continuación, notificar al usuario que está lista. En lugar de realizar manualmente cada uno de estos pasos, podría crear un runbook que realizara todas estas tareas como un solo proceso. Debería iniciar el runbook, especificar la información necesaria como el nombre de máquina virtual, la dirección IP y el correo electrónico del destinatario, y sentarse mientras el proceso se completara.


## ¿Qué pueden automatizar los runbooks?

Los runbooks de Automatización de Azure se basan en Windows PowerShell o en el flujo de trabajo de PowerShell, por lo que hacen todo lo que puede hacer PowerShell. Si una aplicación o servicio tiene una API, un runbook puede trabajar con ella. Si tiene un módulo de PowerShell para ella, puede cargar el módulo en Automatización de Azure e incluir estos cmdlets en el runbook. Los runbooks de Automatización de Azure se ejecutan en la nube de Azure de modo que tienen acceso a cualquier recurso en la nube o recurso externo al que se puede tener acceso desde la nube. Con [Trabajo híbrido de runbook](automation-hybrid-runbook-worker.md), los runbooks pueden ejecutarse en su centro de datos local para administrar recursos locales.


## ¿Dónde puedo obtener runbooks?

La [Galería de runbooks](http://msdn.microsoft.com/library/azure/dn781422.aspx) contiene runbooks de Microsoft y de la comunidad que puede usar sin cambios en su entorno o personalizarlos para sus propios fines. También son útiles como referencias para aprender a crear sus propios runbooks. Incluso puede aportar a la galería runbooks propios que crea que pueden resultar útiles para otros usuarios.


## ¿Cómo creo mis propios runbooks?

Puede [crear sus propios runbooks](http://msdn.microsoft.com/library/azure/dn643637.aspx) desde cero o modificarlos desde la [Galería de runbooks](http://msdn.microsoft.com/library/azure/dn781422.aspx) para sus propios requisitos. Hay tres diferentes [tipos de runbooks](automation-runbook-types.md) diferentes que puede elegir en función de sus requisitos y de la experiencia de PowerShell. Si prefiere trabajar directamente con el código de PowerShell, puede usar un [runbook de PowerShell ](automation-runbook-types.md#powershell-runbooks) o un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) que edite sin conexión o [mediante el editor de texto](http://msdn.microsoft.com/library/azure/dn879137.aspx) en el portal de Azure. Si prefiere editar un runbook sin estar expuesto al código subyacente, puede crear un [runbook gráfico](automation-runbook-types.md#graphical-runbooks) con el [editor gráfico](automation-graphical-authoring-intro.md) en el portal de vista previa de Azure.


## ¿Cómo se relaciona Automatización de Azure con otras herramientas de automatización?

[Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) está diseñado para automatizar las tareas de administración en la nube privada. Se instala localmente en su centro de datos como componente de [Windows Azure Pack](http://www.microsoft.com/server-cloud/products/windows-azure-pack/default.aspx). SMA y Automatización de Azure usan el mismo formato de runbook basado en Windows PowerShell y en el flujo de trabajo de Windows PowerShell, pero SMA no admite los [runbooks gráficos](automation-graphical-authoring-intro.md).

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) está previsto para la automatización de recursos locales. Usa un formato de runbook distinto al de Automatización de Azure y Service Management Automation, y tiene una interfaz gráfica para crear runbooks sin necesidad de usar scripting. Sus runbooks se componen de actividades de paquetes de integración escritas específicamente para Orchestrator.

## ¿Dónde puedo obtener más información?

Hay una amplia gama de recursos disponibles para obtener más información sobre Automatización de Azure y crear sus propios runbooks.

- La **Biblioteca de Automatización de Azure** es donde está ahora mismo. Los artículos de esta biblioteca proporcionan documentación completa sobre la configuración y administración de Automatización de Azure y para crear sus propios runbooks.
- Los [cmdlets de Azure PowerShell](http://msdn.microsoft.com/library/jj156055.aspx) proporcionan información para automatizar operaciones de Azure mediante Windows PowerShell. Los runbooks usan estos cmdlets para trabajar con recursos de Azure.
- [Blog de administración](http://azure.microsoft.com/blog/topics/management) ofrece la información más reciente sobre Automatización de Azure y otras tecnologías de administración de Microsoft. Suscríbase a este blog para mantenerse al día con las novedades del equipo de Automatización de Azure.
- El [foro de automatización](http://go.microsoft.com/fwlink/p/?LinkId=390561) le permite publicar preguntas acerca de Automatización de Azure que serán atendidas por Microsoft y la comunidad de automatización.

## ¿Puedo proporcionar comentarios?

**Envíenos sus comentarios** Si está buscando una solución de runbook o un módulo de integración de Automatización de Azure, publique una solicitud de script en el centro de scripts. Si tiene comentarios o solicitudes de características para Automatización de Azure, publíquelos en [Voz del usuario](http://feedback.windowsazure.com/forums/34192--general-feedback). Gracias.

<!---HONumber=Oct15_HO3-->