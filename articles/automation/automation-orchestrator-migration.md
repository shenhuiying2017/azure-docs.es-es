<properties
   pageTitle="Migración de runbooks y paquetes de integración desde Orchestrator"
   description="Describe cómo migrar runbooks y paquetes de integración desde System Center Orchestrator a Automatización de Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/22/2015"
   ms.author="bwren" />


# Migración de Orchestrator a Automatización de Azure

Los runbooks de [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) se basan en actividades provenientes de paquetes de integración escritos específicamente para Orchestrator, mientras que los runbooks de Automatización de Azure se basan en flujos de trabajo de Windows PowerShell. Los runbooks gráficos de Automatización de Azure tienen una apariencia similar a los runbooks de Orchestrator, con sus actividades que representan los cmdlets de PowerShell, runbooks secundarios y recursos.

El [kit de herramientas para migración de System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) incluye herramientas que le ayudan a convertir runbooks de Orchestrator a Automatización de Azure. Además de convertir los runbooks, debe convertir los paquetes de integración con las actividades que usan en módulos de integración con cmdlets de Windows PowerShell.

A continuación se muestra el proceso básico para convertir runbooks de Orchestrator en runbooks de Automatización de Azure. Cada uno de estos pasos se describe en detalle en las secciones que aparecen a continuación.

1.  Descargue el [kit de herramientas para migración de System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all), que contiene las herramientas y los módulos analizados en este artículo.
2.  Instale el [módulo de actividades estándar](#standard-activities-module) en Automatización de Azure. Esto incluye versiones convertidas de las actividades estándar de Orchestrator que los runbooks convertidos pueden usar.
2.  Instale los [módulos de integración de System Center Orchestrator](#system-center-orchestrator-integration-modules) en Automatización de Azure para los paquetes de integración que los runbooks usan.
3.  Convierta los paquetes de integración personalizados y de terceros con el [convertidor de paquetes de integración](#integration-pack-converter) e instálelos en Automatización de Azure.
4.  Vuelva a crear manualmente los recursos globales de Orchestrator en Automatización de Azure, debido a que no existe un método automatizado para realizar esta migración.
5.  Convierta los runbooks de Orchestrator con el [convertidor de runbooks](#runbook-converter-coming-soon) (próximamente) e instálelos en Automatización de Azure.
6.  Configure un [trabajo híbrido de runbook](#hybrid-runbook-worker) en su centro de datos local para ejecutar los runbooks convertidos.

## Service Management Automation

[Service Management Automation](http://technet.microsoft.com/library/dn469260.aspx) (SMA) almacena y ejecuta runbooks en su centro de datos local como Orchestrator y usa los mismos módulos de integración que Automatización de Azure. Cuando se encuentre disponible, el [convertidor de runbooks](#runbook-converter-coming-soon) convertirá los runbooks de Orchestrator en runbooks gráficos, a pesar de que no son compatibles con SMA. De todos modos puede instalar el [módulo de actividades estándar](#standard-activities-module) y los [módulos de integración de System Center Orchestrator](#system-center-orchestrator-integration-modules) en SMA, pero deberá [reescribir manualmente los runbooks](http://technet.microsoft.com/library/dn469262.aspx).

## Trabajo híbrido de runbook

Los runbooks de Orchestrator se almacenan en un servidor de base de datos y se ejecutan en servidor de runbooks, ambos en su centro de datos local. Los runbooks de Automatización de Azure se almacenan en la nube de Azure y se pueden ejecutar en su centro de datos local, con un [trabajo híbrido de runbook](automation-hybrid-runbook-worker.md). Es de esta manera que normalmente ejecutará los runbooks convertidos desde Orchestrator, dado que están diseñados para ejecutarse en servidores locales.

## Convertidor de paquetes de integración

El convertidor de paquetes de integración convierte paquetes de integración creados con Orchestrator Integration Tool (OIT) en módulos de integración basados en Windows PowerShell que se pueden importar a Automatización de Azure o a Service Management Automation.

Cuando ejecuta el convertir de paquetes de integración, aparece un asistente que le permitirá seleccionar un archivo de paquete de instalación (.oip). A continuación, el asistente enumera las actividades incluidas en ese paquete de integración y le permite seleccionar las que se migrarán. Cuando completa el asistente, se crea un módulo que incluye un cmdlet correspondiente para cada una de las actividades del paquete de integración original.


### Parámetros

Las propiedades de una actividad en el paquete de integración se convierten en parámetros del cmdlet correspondiente en el módulo de integración. Los cmdlets de Windows PowerShell tienen un conjunto de [parámetros comunes](http://technet.microsoft.com/library/hh847884.aspx) que se pueden usar con todos los cmdlets. Por ejemplo, el parámetro -Verbose hace que un cmdlet genere información detallada acerca de su operación. Ningún cmdlet puede tener un parámetro con el mismo nombre que un parámetro común. Si una actividad sí tiene una propiedad con el mismo nombre que un parámetro común, el asistente le solicitará que cambie el nombre del parámetro.

### Actividad de supervisión

Los runbooks de supervisión en Orchestrator comienzan con una [actividad de supervisión](http://technet.microsoft.com/library/hh403827.aspx) y se ejecutan de manera constante, mientras esperan que un evento determinado los invoque. Automatización de Azure no admite los runbooks de supervisión, por lo que no se convertirá ninguna actividad de supervisión del paquete de integración. En su lugar, se crea un cmdlet de marcador de posición en el módulo de integración para la actividad de supervisión. Este cmdlet no tiene ninguna funcionalidad, sino que permite la instalación de cualquier runbook convertir que lo use. Este runbook no se podrá ejecutar en Automatización de Azure, pero sí se podrá instalar para que el usuario pueda modificarlo.

### Paquetes de integración que no se pueden convertir

Los paquetes de integración que no se crearon con OIT, incluidos algunos creados por Microsoft, no se pueden convertir con esta herramienta. Los paquetes de integración que proporciona Microsoft se han convertido en módulos de integración, de manera que pueden instalarse en Automatización de Azure o en Service Management Automation.


## Módulo de actividades estándar

Orchestrator incluye un conjunto de [actividades estándar](http://technet.microsoft.com/library/hh403832.aspx) que no están incluidas en un paquete de integración, pero que son usadas por muchos runbooks. El módulo Actividades estándar es un módulo de integración que incluye un cmdlet equivalente para cada una de estas actividades. Debe instalar este módulo de integración en Automatización de Azure antes de importar cualquier runbook convertido que usa una actividad estándar.

Además de admitir los runbooks convertidos, es posible que un usuario familiarizado con Orchestrator pueda usar los cmdlets del módulo de actividades estándar para crear nuevos runbooks en Automatización de Azure. A pesar de que la funcionalidad de todas las actividades estándar se puede realizar con cmdlets, es posible que funcionen de manera diferente. Los cmdlets del módulo de actividades estándar convertidas funcionarán de la misma manera que sus actividades correspondientes y usarán los mismos parámetros. Esto puede ayudar al autor del runbook de Orchestrator existente en su transición a runbooks de Automatización de Azure.

## Módulos de integración de System Center Orchestrator
Microsoft proporciona [paquetes de integración](http://technet.microsoft.com/library/hh295851.aspx) para crear runbooks a fin de automatizar componentes de System Center y otros productos. Actualmente, cuando descarga algunos de estos paquetes de integración desde [TechNet](http://www.microsoft.com/download/details.aspx?id=39622), no se pueden convertir con el convertidor de paquetes de integración debido a un problema conocido, el que se corregirá con la versión RC del kit de herramientas para migración de System Center Orchestrator. Los [módulos de integración de System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47324&WT.mc_id=rss_alldownloads_all) incluyen las versiones convertidas de estos paquetes de integración que se pueden importar en Automatización de Azure y Service Management Automation antes de esta versión.

## Convertidor de runbooks (próximamente)

Esta herramienta convertirá runbooks de Orchestrator en runbooks gráficos que se pueden importar a Automatización de Azure. En este sitio se proporcionarán más detalles sobre esta herramienta en cuanto se encuentren disponibles.

## Artículos relacionados

- [System Center 2012: Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
- [Trabajo híbrido de runbook](automation-hybrid-runbook-worker.md)
- [Actividades estándar de Orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
 

<!---HONumber=62-->