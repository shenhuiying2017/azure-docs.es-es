<properties 
    pageTitle="Paquetes de inteligencia de Visión operativa" 
    description="Para agregar funcionalidad a Visión operativa puede usar los paquetes de inteligencia." 
    services="operational-insights" 
    documentationCenter="" 
    authors="bandersmsft" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="operational-insights" 
    ms.workload="operational-insights" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/20/2015" 
    ms.author="banders"/>

# Paquetes de inteligencia de Visión operativa

Visión operativa de Microsoft Azure incluye el módulo base de evaluación de la configuración. Sin embargo, puede obtener una mayor funcionalidad si agrega paquetes de inteligencia desde la página de información general.

![imagen del icono de paquetes de inteligencia](./media/operational-insights-add-intelligence-pack/overview-intelpacks.png)

Tras haber agregado un paquete de inteligencia, se recopilan datos de los servidores de la infraestructura y se envían al servicio Visión operativa. El procesamiento por parte del servicio Visión operativa puede tardar desde unos minutos hasta varias horas. Una vez que el servicio ha procesado los datos, podrá verlos en Visión operativa.

Un paquete de inteligencia puede quitarse fácilmente cuando ya no es necesario. Al quitar un paquete de inteligencia, sus datos no se enviarán a Visión operativa, lo que reduce la cantidad de datos usados por la cuota diaria.

## Paquetes de inteligencia admitidos por Microsoft Monitoring Agent

En este momento, los servidores conectados directamente a Visión operativa de Microsoft Azure mediante Microsoft Monitoring Agent pueden usar la mayoría de los paquetes de inteligencia disponibles, incluidos los siguientes:

- [Actualizaciones del sistema](operational-insights-updates.md)

- [Administración de registros](operational-insights-log-collection.md)

- [Antimalware](operational-insights-antimalware.md)

- [Seguimiento de cambios](operational-insights-change-tracking.md)

- [Evaluación de SQL y Active Directory](operational-insights-assessment.md)

Sin embargo, los siguientes paquetes de inteligencia *no* son compatibles con Microsoft Monitoring Agent:

- [Administración de la capacidad](operational-insights-capacity.md)

- [Evaluación de la configuración](operational-insights-configuration-assessment.md)

La colección de registros de IIS se admite en equipos con:

- Windows Server 2012

- Windows Server 2012 R2

### Para agregar un paquete de inteligencia


1. En la página de información general de Visión operativa, haga clic en el icono de **paquetes de inteligencia**.


2. En la página Galería de paquetes de inteligencia de Visión operativa, puede obtener información sobre cada uno de los paquetes de inteligencia disponibles. Haga clic en el nombre del paquete de inteligencia que desea agregar a Visión operativa.


3. En la página del paquete de inteligencia que ha elegido, se muestra información detallada sobre este. Haga clic en **Add**.


4. En la página de confirmación, haga clic en **Accept** (Aceptar) para indicar que está de acuerdo con la declaración de privacidad y los términos de uso.


5. En la página de información general de Visión operativa aparece un icono nuevo para el paquete de inteligencia que ha agregado y puede empezar a usarlo una vez que el servicio mencionado procese los datos.




### Para quitar un paquete de inteligencia



1. En la página de información general de Visión operativa, haga clic en el icono de **paquetes de inteligencia**.


2. En la página Galería de paquetes de inteligencia de Visión operativa, haga clic en **Remove** (Quitar) en el paquete de inteligencia que desea quitar.


3. En la página de confirmación, haga clic en **Yes** (Sí) para quitar el paquete de inteligencia.




<!--HONumber=52-->