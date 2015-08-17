<properties
   pageTitle="Actualización de servidores con actualizaciones del sistema"
   description="Aprenda a usar la solución de actualizaciones del sistema de Visión operativa de Microsoft Azure para que le resulte más fácil aplicar las actualizaciones que faltan a los servidores de la infraestructura"
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
   ms.date="08/05/2015"
   ms.author="banders" />

# Actualización de servidores con actualizaciones del sistema

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Puede usar la solución de actualizaciones del sistema de Microsoft Azure Operational Insights para ayudarle a aplicar las actualizaciones que faltan a los servidores de su infraestructura. Instale la solución para actualizar el agente Operations Manager y el módulo de configuración básica de Visión operativa. La información actualizada se lee en los servidores supervisados y luego los datos actualizados se envían al servicio de Operational Insights en la nube para su procesamiento. Se aplica la lógica a los datos actualizados y el servicio en la nube registra los datos. Si se detectan actualizaciones que faltan, estas se muestran en el panel **Actualizaciones**. Puede usar el panel **Actualizaciones** para trabajar con actualizaciones que faltan y desarrollar un plan para aplicarlas a los servidores que las necesiten.

## Uso de actualizaciones del sistema para actualizar servidores

Para poder usar actualizaciones del sistema en Visión operativa de Microsoft Azure, debe tener instalado el paquete de la solución. Para obtener más información sobre la instalación de soluciones, consulte [Uso de la Galería de soluciones para agregar o quitar soluciones](operational-insights-setup-workspace.md). Una vez instalado, puede ver las actualizaciones que faltan en sus servidores supervisados mediante el mosaico **Evaluación de actualizaciones del sistema** en la página **Información general** de Visión operativa.

### Para trabajar con actualizaciones, siga estos pasos:

1. En la página **Información general**, haga clic en el mosaico **Evaluación de actualizaciones del sistema**. ![imagen de la página Información general](./media/operational-insights-updates/updates01.png)
2. En el panel **Actualizaciones**, vea las categorías de actualización. ![imagen de la página Actualizaciones](./media/operational-insights-updates/updates02.png)
3. Desplácese a la derecha de la página para ver la hoja **Tipo de actualizaciones que faltan** y haga clic en **Actualizaciones de seguridad**. ![imagen de la página Actualizaciones](./media/operational-insights-updates/updates03.png)
4. En la página Búsqueda, se muestra una lista de actualizaciones de seguridad que se detectaron que faltaban en la infraestructura de servidores. Haga clic en el identificador de un artículo de Knowledge Base (KBID) para obtener más información sobre la actualización que falta. En este ejemplo, *KBID 3032323*. ![imagen de la página Actualizaciones](./media/operational-insights-updates/updates04.png)
5. El explorador web abrirá el artículo de Knowledge Base que describe la actualización. ![imagen de la página Actualizaciones](./media/operational-insights-updates/updates05.png)
6. Con la información encontrada, puede crear un plan para aplicar las actualizaciones que faltan.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=August15_HO6-->