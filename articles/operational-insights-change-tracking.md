<properties 
   pageTitle="Identificación de las diferencias con el seguimiento de cambios"
   description="Use el paquete de inteligencia seguimiento de cambios de configuración en Visión operativa de Microsoft Azure para ayudarle a identificar fácilmente el software y los cambios de los servicios de Windows que se producen en su entorno"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Identificación de las diferencias con el seguimiento de cambios

Puede usar el paquete de inteligencia de seguimiento de cambios de configuración en Visión operativa de Microsoft Azure para ayudarle a identificar fácilmente los cambios de los servicios de Windows y software que se producen en su entorno. La identificación de estos cambios de configuración puede ayudarle a identificar problemas de funcionamiento.

Instale el paquete de inteligencia para actualizar el agente Operations Manager y el módulo de configuración básica para Visión operativa. Los cambios en los servicios de Windows y el software instalados en los servidores supervisados se leen y, a continuación, se envían los datos al servicio de Vista operativa en la nube para su procesamiento. Se aplica la lógica a los datos recibidos y el servicio de nube registra los datos. Cuando se detectan cambios, se muestran los servidores con los cambios en el panel de seguimiento de cambios. Con la información en el panel de seguimiento de cambios, puede ver fácilmente los cambios realizados en la infraestructura de servidores.

## Uso de seguimiento de cambios

Para poder utilizar el seguimiento de cambios en Vista operativa, debe tener instalado el paquete de inteligencia. Para obtener más información sobre cómo instalar los paquetes de inteligencia, consulte [Uso de la Galería para agregar o eliminar paquetes de inteligencia](operational-insights-add-intelligence-packs.md). 

Una vez instalado, puede ver el resumen de cambios para los servidores supervisados mediante el icono **Seguimiento de cambios** en la página **Información general** en Vista operativa. 

![imagen del icono de seguimiento de cambios](./media/operational-insights-change-tracking/overview-change-track.png)

Puede ver los cambios en la infraestructura y, a continuación, profundizar en los detalles de las siguientes categorías:

- Cambios realizados por el tipo de configuración para los servicios de Windows y el software

- Cambios de software en aplicaciones y actualizaciones para los servidores individuales

- Número total de cambios de software para cada aplicación

- Cambios de servicio de Windows para servidores individuales

![imagen del panel de seguimiento de cambios](./media/operational-insights-change-tracking/gallery-changetracking-01.png)
![imagen del panel de seguimiento de cambios](./media/operational-insights-change-tracking/gallery-changetracking-02.png)

### Para ver los cambios de algún tipo de cambio

1. En la página **Información general**, haga clic en el icono **Seguimiento de cambios**.

2. En el panel de **seguimiento de cambios**, revise la información de resumen en uno de los módulos de tipo de cambio y, a continuación, haga clic en uno para obtener información detallada acerca de él en la página de **búsqueda**.

3. En cualquiera de las páginas de búsqueda, puede ver los resultados por tiempo, resultados detallados e historial de búsqueda. También puede filtrar por las facetas para restringir los resultados.

4. En cualquier página de búsqueda, puede **exportar** los detalles de resultados en un archivo CSV que puede abrir con Excel u otra aplicación que permita las tareas de visualización o modificación.


<!--HONumber=52-->