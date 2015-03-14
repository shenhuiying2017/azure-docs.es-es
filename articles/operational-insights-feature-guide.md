<properties 
	pageTitle="Guía de características de Visión operativa" 
	description="Visión operativa es un servicio de análisis que permite a los administradores de TI obtener un visión profunda en entornos locales y en la nube. Le permite interactuar con datos de máquina en tiempo real e históricos para desarrollar con rapidez una visión personalizada, y proporciona patrones desarrollados por Microsoft y la comunidad para analizar datos." 
	services="operational-insights" 
	documentationCenter="" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/06/2014" 
	ms.author="banders"/>

<h1>Guía de características de Visión operativa</h1>

Esta guía le ayuda a comprender los problemas que Visión operativa puede ayudarle a resolver, de qué consta el entorno de Visión operativa y cómo puede crear una cuenta de Visión operativa y e iniciar sesión en el servicio.

<h2 id="whatisaad">¿Qué es Visión operativa?</h2>

Visión operativa es un servicio de análisis que permite a los administradores de TI obtener un visión profunda en entornos locales y en la nube. Le permite interactuar con datos de máquina en tiempo real e históricos para desarrollar con rapidez una visión personalizada, y proporciona patrones desarrollados por Microsoft y la comunidad para analizar datos.

Con Visión operativa, puede transformar datos de máquina en inteligencia operativa. En concreto, puede:

- Administrar la capacidad de su infraestructura de servidores
- Actualizar servidores con actualizaciones del sistema
- Comprender las relaciones entre los datos en los archivos de registro de los servidores
- Proteger los servidores con antimalware
- Administrar los riesgos de seguridad con control de la seguridad
- Realizar un seguimiento de los cambios de configuración en todos los servidores
- Optimizar SQL Server
- Buscar registros de eventos e IIS
- Instalar agentes en las máquinas virtuales de Microsoft Azure IaaS
- Usar Visión operativa sin Operations Manager  

<h2 id="">Entorno de Visión operativa</h2>

El entorno de Visión operativa se compone de:

- Áreas de trabajo hospedadas por Microsoft Azure que son contenedores para las cuentas de Azure
- el servicio web de Visión operativa, que se hospeda en la nube
- cualquiera de los agentes independientes que se conectan directamente al servicio web
- o bien, un servicio adjunto a System Center Operations Manager, pero no es necesario




Si ha usado la versión anterior de Visión operativa denominada System Center Advisor, podría tener software de Advisor instalado en su entorno local. Sin embargo, el software de Advisor no es compatible con Visión operativa.

Mediante el software de Visión operativa como un servicio de Operations Manager consta de uno o varios grupos de administración y al menos un agente por grupo de administración. Los agentes de Operations Manager recopilan datos de los servidores y los analizan mediante módulos de inteligencia (similares a un módulo de administración de System Center Operations Manager). Los datos analizados se envían con regularidad de Operations Manager al servicio web de Visión operativa mediante un servidor proxy, omitiendo otros servidores, por lo que no hay ninguna carga adicional colocada en ellos.

De forma similar, los agentes instalados en equipos individuales pueden conectarse directamente al servicio web para enviar los datos recopilados para el procesamiento.

Los datos de cada módulo de inteligencia se analizan y se presentan en el portal de Visión operativa. Puede ver las alertas y las orientaciones sobre correcciones asociadas, las evaluaciones de configuración, los problemas de capacidad de infraestructura, el estado de actualización del sistema, las advertencias sobre antimalware y los datos de registro. También puede realizar búsquedas detalladas.

<h2 id="">Crear una cuenta de Visión operativa e iniciar sesión</h2>

Utilice la siguiente información para configurar una cuenta para usarla con Visión operativa de Microsoft Azure e iniciar sesión en Visión operativa.



<h3>Obtener una cuenta de Microsoft o de organización</h3>

Si desea crear una cuenta de organización, regístrese en <a href="http://go.microsoft.com/fwlink/?LinkId=396866" target="_blank">Cuenta de Microsoft para organizaciones</a>. A continuación, necesita conceder acceso a su cuenta de organización para Visión operativa en <a href="http://aka.ms/Mr1dtz" target="_blank">Windows Azure Active Directory</a>.


Si desea obtener una cuenta de Microsoft (anteriormente denominada "Windows Live ID"), regístrese en <a href="http://go.microsoft.com/fwlink/?LinkId=396868" target="_blank">Registro: cuenta de Microsoft</a>. Se concede acceso automático a Visión operativa con una cuenta de Microsoft.


<h3>Iniciar sesión en Visión operativa</h3>

1. Vaya a <a href="preview.opinsights.azure.com" target="_blank">Visión operativa de Microsoft Azure</a>  y elija Iniciar sesión en la parte superior de la página.
2. Seleccione **Cuenta Microsoft** o **Cuenta de organización** e **inicie sesión** con sus credenciales.
3. Si se le solicita, cree una cuenta de Visión operativa para asociarla con la información de inicio de sesión.
4. Seleccione **Probar vista previa** cuando se le solicite seleccionar la versión de Visión operativa que quiere usar.
5. Si se le solicita, escriba el código de invitación y haga clic en **Aplicar** para unirse a la vista previa. Si no tiene un código de invitación, en la parte inferior de la pantalla, escriba sus datos para obtener uno.

<!--HONumber=45--> 
