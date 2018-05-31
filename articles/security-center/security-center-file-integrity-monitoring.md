---
title: Supervisión de la integridad de los archivos en Azure Security Center (versión preliminar) | Microsoft Docs
description: " Aprenda a habilitar la supervisión de la integridad de los archivos en Azure Security Center. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: terrylan
ms.openlocfilehash: 722a4fd11f35f04ed22d73638f07d15c49ea3c26
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161838"
---
# <a name="file-integrity-monitoring-in-azure-security-center-preview"></a>Supervisión de la integridad de los archivos en Azure Security Center (versión preliminar)
Aprenda a configurar la supervisión de la integridad de los archivos (FIM) en Azure Security Center mediante este tutorial.

## <a name="what-is-fim-in-security-center"></a>¿Qué la supervisión de la integridad de los archivos (FIM) Security Center?
La supervisión de la integridad de los archivos (FIM), conocida también como supervisión de los cambios, examina los archivos y los registros del sistema operativo, el software de aplicación y demás para comprobar la existencia de cambios que podrían indicar un ataque. Para determinar si el estado actual del archivo es diferente del último examen del archivo, se usa un método de comparación. Puede aprovechar esta comparación para determinar si se han realizado modificaciones sospechosas o válidas en los archivos.

La supervisión de la integridad de los archivos de Security Center valida la integridad de los archivos de Windows, el Registro de Windows y los archivos de Linux. Seleccionará los archivos que quiere supervisar mediante la habilitación de FIM. Security Center supervisa los archivos con FIM habilitado en busca de actividades como:

- Eliminación y creación de archivos y del Registro
- Modificaciones de archivos (cambios en el tamaño de archivo, listas de control de acceso y hash del contenido)
- Modificaciones del Registro (cambios en el tamaño, listas de control de acceso, tipo y contenido)

Security Center recomienda entidades para supervisar, en las que se pueda habilitar fácilmente FIM. También puede definir sus propias directivas o entidades de FIM que quiere supervisar. En este tutorial se muestra cómo hacerlo.

> [!NOTE]
> La característica de supervisión de la integridad de los archivos (FIM) funciona con equipos y máquinas virtuales Windows y Linux y está disponible en el nivel Estándar de Security Center. Para obtener más información sobre los planes de tarifa de Security Center, vea [Precios](security-center-pricing.md).
FIM carga datos en el área de trabajo de Log Analytics. Se aplican cargos de datos, según la cantidad de datos que cargue. Para más información, consulte [Precios de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
>
>

> [!NOTE]
> FIM emplea la solución Azure Change Tracking para realizar el seguimiento de los cambios e identificarlos en su entorno. Cuando la supervisión de la integridad de los archivos está habilitada, tendrá un recurso **Change Tracking** de tipo solución. Si quita el recurso **Change Tracking**, se deshabilita la característica de supervisión de la integridad de los archivos en Security Center.
>
>

## <a name="which-files-should-i-monitor"></a>¿Qué archivos se deben supervisar?
Al elegir los archivos que quiere supervisar, es conveniente que piense en los archivos que son críticos para su sistema y aplicaciones. Considere la posibilidad de elegir archivos que no espera que cambien sin haberlo planeado. Al elegir archivos que las aplicaciones o el sistema operativo cambian con frecuencia (por ejemplo, archivos de registro y archivos de texto) se genera mucho ruido que dificulta la identificación de un ataque.

Security Center recomienda qué archivos se deben supervisar de forma predeterminada según los patrones de ataque conocidos que incluyen cambios en los archivos y el Registro.

## <a name="using-file-integrity-monitoring"></a>Uso de la supervisión de la integridad de los archivos
1. Abra el panel **Security Center**.
2. En el panel izquierdo, en **Protección en la nube avanzada**, seleccione **Supervisión de la integridad de los archivos**.
![Panel de Security Center][1]

Se abre la hoja **Supervisión de la integridad de los archivos**.
  ![Panel de Security Center][2]

Se proporciona la siguiente información para cada área de trabajo:

- Número total de cambios que se produjeron durante la última semana (puede ver un guion "-" si FIM no está habilitado en el área de trabajo)
- Número total de equipos y las máquinas virtuales que informan al área de trabajo
- Ubicación geográfica del área de trabajo
- Suscripción de Azure en la que se encuentra el área de trabajo

También se pueden mostrar los siguientes botones en un área de trabajo:

- ![Icono Habilitar][3] Indica que FIM no está habilitada en el área de trabajo. Al seleccionar el área de trabajo puede habilitar FIM en todas las máquinas del área de trabajo.
- ![Icono Actualizar plan][4]: indica que la suscripción o el área de trabajo no se ejecutan en el nivel Estándar de Security Center. Para usar la característica FIM, su suscripción debe ejecutarse como Estándar.  Al seleccionar el área de trabajo, puede actualizarla a Estándar. Para más información sobre el nivel Estándar y cómo realizar la actualización, consulte [Actualización al nivel Estándar de Security Center para mejorar la seguridad](security-center-pricing.md).
- Un espacio en blanco (no hay ningún botón) significa que FIM ya está habilitado en el área de trabajo.

En **Supervisión de la integridad de los archivos**, puede seleccionar un área de trabajo en la que habilitar FIM, ver el panel de supervisión de la integridad de los archivos correspondiente a ese área de trabajo o [actualizar](security-center-pricing.md) el área de trabajo a Estándar.

## <a name="enable-fim"></a>Habilitación de FIM
Para habilitar FIM en un área de trabajo, siga estos pasos:

1. En **Supervisión de la integridad de los archivos**, seleccione un área de trabajo con el botón **Habilitar**.
2. Se abre la página **Habilitar supervisión de la integridad de los archivos** y muestra el número de máquinas Windows y Linux en el área de trabajo.

   ![Habilitación de la supervisión de la integridad de los archivos][5]

   También se muestra la configuración recomendada para Windows y Linux.  Expanda **Archivos de Windows**, **Registro** y **Archivos de Linux** para ver la lista completa de elementos recomendados.

3. Desactive todas las entidades recomendadas a las que no desea aplicar FIM.
4. Seleccione **Aplicar la supervisión de la integridad de los archivos** para habilitar FIM.

> [!NOTE]
> Puede cambiar la configuración en cualquier momento. Consulte a continuación [Edición de las entidades supervisadas](security-center-file-integrity-monitoring.md#edit-monitored-items) para más información.
>
>

## <a name="view-the-fim-dashboard"></a>Visualización del panel de FIM
El panel **Supervisión de la integridad de los archivos** se muestra en las áreas de trabajo en las que está habilitado FIM. El panel de FIM se abre después de habilitar FIM en un área de trabajo o al seleccionar un área de trabajo en la ventana **Supervisión de la integridad de los archivos** que ya tiene FIM habilitado.

![Panel de Supervisión de la integridad de los archivos][6]

El panel de FIM de un área de trabajo muestra los siguientes elementos:

- Número total de máquinas conectadas al área de trabajo
- Número total de cambios que se produjeron durante el período de tiempo seleccionado
- Un desglose del tipo de cambio (archivos, registro)
- Un desglose de la categoría del cambio (modificado, agregado, quitado)

Al seleccionar Filtrar en la parte superior del panel, puede aplicar el período de tiempo del que desea ver los cambios.

![Filtro de período de tiempo][7]

La pestaña **Equipos** (que se muestra arriba) enumera todas las máquinas que informan a este área de trabajo. En el panel se muestra para cada máquina:

- Número total de cambios que se produjeron durante el período de tiempo seleccionado
- Un desglose de los cambios totales a medida que se producen cambios en los archivos o el registro

La hoja **Búsqueda de registros** se abre cuando escribe el nombre de una máquina en el campo de búsqueda o selecciona una máquina que aparecen en la pestaña Equipos. En Búsqueda de registros se muestran todos los cambios realizados durante el período de tiempo seleccionado para la máquina. Los cambios se pueden expandir para obtener más información.

![Búsqueda de registros][8]

La pestaña **Cambios** (se muestra a continuación) enumera todos los cambios del área de trabajo durante el período de tiempo seleccionado. En el panel se muestra para las entidades que se han cambiado:

- Equipo en el que se produjo el cambio
- Tipo de cambio (registro o archivo)
- Categoría del cambio (modificado, agregado, eliminado)
- Fecha y hora del cambio

![Cambios del área de trabajo][9]

Se abre la hoja **Cambiar detalles** al escribir un cambio en el campo de búsqueda o al seleccionar una entidad enumerada en la pestaña **Cambios**.

![Cambiar detalles][10]

## <a name="edit-monitored-entities"></a>Edición de las entidades supervisadas

1. Vuelva al panel **Supervisión de la integridad de los archivo** y seleccione **Configuración**.

  ![Settings][11]

  Se abre la hoja **Configuración del área de trabajo** que muestra tres pestañas: **Registro de Windows**, **Archivos de Windows** y **Archivos de Linux**. En cada pestaña se muestran las entidades que se pueden editar de esa categoría. En cada entidad de la lista, Security Center identifica si FIM está habilitado (true) o deshabilitado (false).  Al editar la entidad, puede habilitar o deshabilitar FIM.

  ![Configuración del área de trabajo][12]

2. Seleccione un elemento identityprotection. En este ejemplo se selecciona un elemento en el Registro de Windows. Se abre la hoja **Edit for Change Tracking** (Editar para Change Tracking).

  ![Editar para Change Tracking][13]

En **Edit for Change Tracking** (Editar para Change Tracking), puede:

- Habilitar (true) o deshabilitar (false) la supervisión de la integridad de los archivos
- Proporcionar o cambiar el nombre de la entidad
- Proporcionar o cambiar el valor o la ruta de acceso
- Eliminar la entidad, descarta el cambio o guardar el cambio

## <a name="add-a-new-entity-to-monitor"></a>Agregar una nueva entidad para supervisar
1. Vuelva al panel **Supervisión de la integridad de los archivo** y seleccione **Configuración** en la parte superior. Se abre la hoja **Configuración del área de trabajo**.
2. En **Configuración del área de trabajo**, seleccione la pestaña correspondiente al tipo de entidad que quiere agregar: Registro de Windows, Archivos de Windows o Archivos de Linux. En este ejemplo, hemos seleccionado **Archivos de Linux**.

  ![Adición de un nuevo elemento para supervisar][14]

3. Seleccione **Agregar**. Se abre la hoja **Add for Change Tracking** (Agregar para Change Tracking).

  ![Escribir la información solicitada][15]

4. En la página **Agregar**, escriba la información solicitada y seleccione **Guardar**.

## <a name="disable-monitored-entities"></a>Deshabilitación de las entidades supervisadas
1. Vuelva a al panel **Supervisión de la integridad de los archivos**.
2. Seleccione un área de trabajo donde esté habilitado actualmente FIM. Un área de trabajo está habilitada para FIM si le falta el botón Habilitar o Actualizar plan.

  ![Seleccionar un área de trabajo donde esté habilitado FIM][16]

3. En Supervisión de la integridad de los archivos, seleccione **Configuración**.

  ![Seleccionar Configuración][17]

4. En **Configuración del área de trabajo**, seleccione un grupo donde **Habilitado** esté establecido en True.

  ![Configuración del área de trabajo][18]

5. En **Edit for Change Tracking** (Editar para Change Tracking), establezca **Habilitado** en False.

  ![Establecer Habilitado en False][19]

6. Seleccione **Guardar**.

## <a name="disable-fim"></a>Deshabilitación de FIM
Puede deshabilitar FIM. FIM emplea la solución Azure Change Tracking para realizar el seguimiento de los cambios e identificarlos en su entorno. Al deshabilitar FIM, quita la solución Change Tracking del área de trabajo seleccionada.

1. Para deshabilitar FIM, vuelva al panel **Supervisión de la integridad de los archivos**.
2. Seleccione un área de trabajo.
3. En **Supervisión de la integridad de los archivos**, seleccione **Deshabilitar**.

  ![Deshabilitación de FIM][20]

4. Seleccione **Quitar** para deshabilitarlo.

## <a name="next-steps"></a>Pasos siguientes
En este artículo ha aprendido a usar la Supervisión de la integridad de los archivos (FIM) de Security Center. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad](security-center-recommendations.md): conozca una serie de recomendaciones que le ayudarán a proteger los recursos de Azure.
* [Supervisión del estado de seguridad](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Security Center](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
