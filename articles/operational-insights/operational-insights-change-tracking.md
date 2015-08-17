<properties
   pageTitle="Identificación de las diferencias con el seguimiento de cambios"
   description="Use la solución de seguimiento de cambios de configuración en Visión operativa de Microsoft Azure para que le ayude a identificar fácilmente y los cambios de los servicios de Windows y el software que se producen en su entorno."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/05/2015"
   ms.author="banders" />

# Identificación de las diferencias con el seguimiento de cambios

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Puede usar la solución de seguimiento de cambios de configuración en Visión operativa de Microsoft Azure para que le ayude a identificar fácilmente los cambios de los servicios de Windows y el software que se producen en su entorno. La identificación de estos cambios de configuración puede ayudarle a identificar problemas operativos.

Instale la solución para actualizar el agente Operations Manager y el módulo de configuración básica de Visión operativa. Los cambios en los servicios de Windows y el software instalados en los servidores supervisados se leen y, a continuación, se envían los datos al servicio de Vista operativa en la nube para su procesamiento. Se aplica la lógica a los datos recibidos y el servicio de nube registra los datos. Cuando se detectan cambios, se muestran los servidores con los cambios en el panel de seguimiento de cambios. Con la información en el panel de seguimiento de cambios, puede ver fácilmente los cambios realizados en la infraestructura de servidores.

## Uso de seguimiento de cambios

Para poder utilizar el seguimiento de cambios en Visión operativa, debe tener instalada la solución de inteligencia. Para obtener más información sobre cómo instalar soluciones, consulte [Uso de la Galería de soluciones para agregar o quitar soluciones](operational-insights-setup-workspace.md).

Una vez instalado, puede ver el resumen de cambios para los servidores supervisados mediante el icono **Seguimiento de cambios** en la página **Información general** de Visión operativa.

![imagen del icono de seguimiento de cambios](./media/operational-insights-change-tracking/overview-change-track.png)

Puede ver los cambios en la infraestructura y, a continuación, profundizar en los detalles de las siguientes categorías:

- Cambios realizados por el tipo de configuración para los servicios de Windows y el software

- Cambios de software en aplicaciones y actualizaciones para los servidores individuales

- Número total de cambios de software para cada aplicación

- Cambios de servicio de Windows para servidores individuales

![imagen del panel de seguimiento de cambios](./media/operational-insights-change-tracking/gallery-changetracking-01.png) ![imagen del panel de seguimiento de cambios](./media/operational-insights-change-tracking/gallery-changetracking-02.png)

### Para ver los cambios de algún tipo de cambio

1. En la página **Información general**, haga clic en el icono **Seguimiento de cambios**.

2. En el panel **Seguimiento de cambios**, revise la información de resumen en una de las hojas de tipo de cambio y, a continuación, haga clic en una para obtener información detallada acerca de la misma en la página de **búsqueda de registros**.

3. En cualquiera de las páginas de búsqueda de registros, puede ver los resultados por tiempo, resultados detallados y el historial de búsqueda de registros. También puede filtrar por las facetas para restringir los resultados.


[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=August15_HO6-->