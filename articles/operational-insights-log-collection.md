<properties 
   pageTitle="Cómo usar Administración de registros" 
   description="Con Administración de registros de Microsoft Azure Operational Insights puede ver los eventos de registro recopilados de sus servidores supervisados" 
   services="operational-insights" 
   documentationCenter="" 
   authors="bandersmsft" 
   manager="jwhit" 
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="operational-insights" 
   ms.date="03/20/2015"
   ms.author="banders"/>

# Uso de Administración de registros 

Antes de poder usar Administración de registros en Microsoft Azure Operational Insights, debe tener instalado el paquete de inteligencia. Como este paquete debe configurarse y no recopila nada de forma predeterminada, está habilitado de forma predeterminada en todas las nuevas áreas de trabajo. Si lo ha quitado, puede agregarlo de nuevo desde la Galería de paquetes de inteligencia; consulte [Uso de la Galería para agregar o quitar paquetes de inteligencia](operational-insights-add-intelligence-packs.md). 

Puede agregar nuevos registros para recopilar eventos y elegir qué niveles de eventos o gravedad desea recopilar para los registros.
Después de configurar Administración de registros, se adoptará la directiva de recopilación y comenzará la recopilación de eventos.
Puede tener acceso a algunos desgloses iniciales de registros de eventos recopilados de sus servidores supervisados mediante el mosaico de **Administración de registros** de la página **Introducción** de Operational Insights. 

![imagen del mosaico de Administración de registros](./media/operational-insights-log-collection/overview-log-mgt.png)

El mosaico abre la página **Administración de registros** donde puede ver un resumen de los eventos contenidos en los registros.
O bien, puede usar la búsqueda directamente, como con cualquier otro tipo de dato.


En la página se detallan las siguientes categorías:

- Registros de eventos de Windows
- Registros IIS
- Otros registros que ha agregado

![imagen del panel de Administración de registros](./media/operational-insights-log-collection/gallery-logmgt-01.png)

![imagen del panel de Administración de registros](./media/operational-insights-log-collection/gallery-logmgt-02.png)

## Formato de archivo de registro de IIS

El único formato de registro de IIS compatible hasta el momento es W3C. No se preocupe, es el formato más común y el formato predeterminado en IIS 7 y IIS 8. De modo que, si registra en formato nativo NCSA o IIS, Operational Insights no seleccionará esos registros. Incluso en formato W3C, verá que no todos los campos se registran de forma predeterminada. Puede obtener más información sobre el formato en [Selección de campos W3C para registrar (IIS 7)](https://technet.microsoft.com/library/cc754702(v=WS.10).aspx). 


> [AZURE.TIP] Para obtener la mejor experiencia de búsqueda, se recomienda seleccionar todos los campos de registro de cada sitio web mediante el uso de **Registro** en IIS. También se recomienda cambiar la programación de **Conversión de archivos de registro** para los nuevos registros a **Cada hora**; de modo que, los archivos más pequeños se actualizarán en la nube, lo que supondrá un ahorro en el ancho de banda.


## Recopilación de registros de eventos de Windows desde Operations Manager o agentes directamente conectados

1. En la página **Introducción**, haga clic en el mosaico **Administración de registros**. 

2. En el panel **Administración de registros**, haga clic en el mosaico **Configurar**.
 
3. Escriba el nombre del registro de eventos del que desea recopilar información. Si no está seguro del nombre que desea usar, seleccione las propiedades del registro de eventos de Windows en el **Visor de eventos**, copie el nombre en el campo **NombreCompleto** y péguelo en el cuadro **Recopilar eventos de los siguientes registros de eventos**.

4. Haga clic en **+** para agregar el registro.

5. Seleccione los niveles de eventos o la gravedad que desea recopilar para el registro. **Los eventos Auditoría correcta** y **Error de auditoría** no se admiten en esta versión.

6. Repita los pasos anteriores para cada registro del que desee recopilar información y luego haga clic en **Guardar**.

7. Los eventos deben aparecer en Operational Insights en unos minutos y luego puede realizar búsquedas en los datos. 



## Para recopilar registros de IIS de Operations Manager o de agentes conectados directamente, siga estos pasos:

1. En la página **Introducción**, haga clic en el mosaico **Administración de registros**. 

2. En el panel **Administración de registros**, haga clic en el mosaico **Configurar**.
 
3. En **Registros de IIS**, seleccione **Recopilar registros de Operations Manager**.


## Recopilación de registros de IIS o eventos de Windows desde Diagnósticos de Azure
Esto se configura desde el Portal de administración de Azure, no desde el portal de Operational; en el área de trabajo, vaya a la pestaña **Almacenamiento** y habilite la recopilación de registros desde esa cuenta de almacenamiento.


## Trabajo con archivos de registro
 
1. En la página **Introducción**, haga clic en el mosaico **Administración de registros**.

2. En el panel **Administración de registros**, vea los registros de eventos y elija uno con el que trabajar.
  
3. Haga clic en cualquier elemento para ver información detallada al respecto en la página **Buscar**.

4. Puede realizar búsquedas adicionales desde los resultados iniciales y luego analizar y correlacionar los registros.

 
# Recursos adicionales
- [Requisitos de formato de registro de IIS en Azure Operational Insights](http://blogs.technet.com/b/momteam/archive/2014/09/19/iis-log-format-requirements-in-system-center-advisor.aspx)
- Vea qué otros orígenes de datos y tipos de registros nos pide la comunidad que implementemos en nuestro [foro de comentarios](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).


<!--HONumber=52-->