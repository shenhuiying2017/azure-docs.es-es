<properties 
   pageTitle="Actualización de servidores con actualizaciones del sistema"
   description="Aprenda a usar el paquete de inteligencia de actualizaciones del sistema de Microsoft Azure Operational Insights para ayudarle a aplicar las actualizaciones que faltan a los servidores de su infraestructura"
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

# Actualización de servidores con actualizaciones del sistema

Puede usar el paquete de inteligencia de actualizaciones del sistema de Microsoft Azure Operational Insights para ayudarle a aplicar las actualizaciones que faltan a los servidores de su infraestructura Instale el paquete de inteligencia para actualizar el agente Operations Manager y el módulo de configuración básica para Visión operativa. La información actualizada se lee en los servidores supervisados y luego los datos actualizados se envían al servicio de Operational Insights en la nube para su procesamiento. Se aplica la lógica a los datos actualizados y el servicio en la nube registra los datos. Si se encuentra que faltan actualizaciones, estas se muestran en el panel **Actualizaciones**. Puede usar el panel **Actualizaciones** para trabajar con actualizaciones que faltan y desarrollar un plan para aplicarlas a los servidores que las necesiten.

## Uso de actualizaciones del sistema para actualizar servidores

Antes de poder usar actualizaciones del sistema en Microsoft Azure Operational Insights, debe tener instalado el paquete de inteligencia. Para obtener más información sobre cómo instalar los paquetes de inteligencia, consulte [Uso de la Galería para agregar o eliminar paquetes de inteligencia](../operational-insights-add-intelligence-packs.md). Después de que se ha instalado, puede ver las actualizaciones que faltan en sus servidores supervisados mediante el mosaico **Evaluación de actualizaciones del sistema** en la página **Introducción** de Operational Insights. 

![imagen del mosaico Evaluación de actualizaciones del sistema](./media/operational-insights-updates/overview-update.png)

El mosaico abre el panel **Actualizaciones** donde puede ver un resumen general de las actualizaciones que faltan. En la página se detallan las siguientes categorías:

- Servidores a los que les faltan actualizaciones de seguridad

- Servidores que no se han actualizado recientemente

- Actualizaciones que se deben aplicar a servidores específicos

- Tipo de actualizaciones que faltan

Puede hacer clic en cualquier mosaico o elemento para ver sus detalles en la página **Buscar** y obtener más información sobre la actualización que falta. 

![imagen del panel Actualizaciones](./media/operational-insights-updates/gallery-sysupdate-01.png)

![imagen del panel Actualizaciones](./media/operational-insights-updates/gallery-sysupdate-02.png)

## Resultados de búsqueda##
Los resultados de búsqueda de actualizaciones incluyen:

- Server

- Título de la actualización

- Id. de Knowledge Base

- Producto al que se aplica la actualización

- Gravedad de la actualización

- fecha de publicación

Los resultados de búsqueda de *servidor* incluyen:

- Nombre de servidor

- Nombre de la versión del sistema operativo

- Método de habilitación de la actualización automática

- Días desde la última actualización

- Versión del agente de Windows Update

## Para trabajar con actualizaciones, siga estos pasos:

1. En la página **Introducción**, haga clic en el mosaico **Evaluación de actualizaciones del sistema**.

2. En el panel **Actualizaciones**, vea las categorías actualizadas y elija una con la que trabajar.

3. Haga clic en un mosaico o en cualquier elemento para ver información detallada sobre él en la página **Buscar**.

4. Con la información que ha encontrado, puede crear un plan para aplicar las actualizaciones que faltan.


<!--HONumber=52--> 