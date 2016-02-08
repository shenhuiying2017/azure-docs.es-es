<properties
   pageTitle="Administración de recomendaciones de seguridad en el Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento explica cómo las recomendaciones del Centro de seguridad de Azure ayudan a proteger los recursos de Azure y a cumplir con las directivas de seguridad."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/21/2016"
   ms.author="terrylan"/>

# Administración de recomendaciones de seguridad en el Centro de seguridad de Azure

En este documento se explica cómo las recomendaciones del Centro de seguridad de Azure le permiten proteger los recursos de Azure.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure. Se trata de una introducción al servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## ¿Qué es el Centro de seguridad de Azure?
El Centro de seguridad de Azure ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en sus recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

## ¿Cuáles son las recomendaciones de seguridad?
El Centro de seguridad de Azure analiza periódicamente el estado de seguridad de los recursos de Azure. Las recomendaciones se crean una vez que se identifican las posibles vulnerabilidades de seguridad. Las recomendaciones le guían en el proceso de configurar el control necesario.

## Implementación de recomendaciones de seguridad

### Establecimiento de las recomendaciones

En [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md) aprende a:

- Configurar directivas de seguridad.
- Activar la colección de datos.
- Elegir las recomendaciones que verá como parte de la directiva de seguridad.

Las recomendaciones de directivas actuales se centran en las actualizaciones del sistema, las reglas de línea base, antimalware, [ACL para puntos de conexión](../virtual-machines/virtual-machines-set-up-endpoints.md), [grupos de seguridad de red](../virtual-networks/virtual-networks-nsg.md) en interfaces de red y subredes, auditoría de Base de datos SQL, cifrado de datos transparente de Base de datos SQL y firewall de aplicaciones web. [Establecimiento de directivas de seguridad](security-center-policies.md) proporciona una descripción de cada opción de recomendación.

### Supervisión de las recomendaciones
Después de establecer una directiva de seguridad, el Centro de seguridad de Azure analiza el estado de seguridad de los recursos con el fin de identificar posibles vulnerabilidades. El icono **Recomendaciones** de la hoja **Centro de seguridad** permite conocer la cantidad total de recomendaciones que identifica el Centro de seguridad de Azure.

![][2]

Para ver los detalles de cada recomendación:

1. Haga clic en el **icono Recomendaciones** de la hoja **Centro de seguridad**. Se abrirá la hoja **Recomendaciones**.

Las recomendaciones aparecen en un formato de tabla, donde cada línea representa una recomendación determinada. Las columnas de esta tabla son:

- **DESCRIPCIÓN**: una explicación de la recomendación y qué se debe hacer para solucionarlo.
- **RECURSO**: a qué recursos se aplica esta recomendación.
- **ESTADO**: el estado actual de la recomendación:
    - **Abierta**: no solucionada aún
    - **En curso**: la recomendación se aplica actualmente a esos recursos; no se requiere ninguna acción de su parte.
    - **Resuelta**: la recomendación ya se completó (cuando el estado es Resuelta, el color de la línea aparece atenuado).
- **GRAVEDAD**: Describe la gravedad de esa recomendación determinada:
    - **Alta**: existe una vulnerabilidad en un recurso importante (aplicación, VM, grupo de seguridad de red) y requiere atención.
    - **Media**: Se requieren pasos adicionales o no críticos para completar un proceso o eliminar una vulnerabilidad.
    - **Baja**: se trata de una vulnerabilidad que se debe abordar, pero no requiere una atención inmediata. (De manera predeterminada, no se muestran las recomendaciones bajas pero, si opta por verlas, puede filtrar según Recomendaciones bajas).

Utilice la siguiente tabla como referencia para comprender las recomendaciones disponibles y lo que hará cada una de ellas si las aplica:

|Recomendación|Descripción|
|-----|-----|
|Habilitar la colección de datos para suscripciones/máquinas virtuales|Se recomienda activar la recopilación de datos en la directiva de seguridad para cada una de las suscripciones o para máquinas virtuales seleccionadas.|
|Resolver el error de coincidencia de reglas de línea base|Recomienda alinear las configuraciones del SO con las líneas base recomendadas; por ejemplo, no permitir guardar las contraseñas.|
|Aplicar actualizaciones del sistema|Recomienda implementar las actualizaciones del sistema faltantes a las VM (solo VM de Windows).|
|Configurar ACL para puntos de conexión|Recomienda configurar una lista de control de acceso para restringir el acceso entrante a las VM (solo VM clásicas).|
|Agregar un firewall de aplicaciones web|Recomienda implementar un Firewall de aplicaciones web (WAF) para puntos de conexión web (solo VM de Administrador de recursos).|
|Finalizar la configuración del firewall de aplicaciones web|Para completar la configuración de un WAF, el tráfico se debe redirigir a la aplicación del WAF. Esta recomendación completará los cambios de configuración necesarios.|
|Habilitar antimalware|Recomienda aprovisionar antimalware a las VM (solo VM de Windows).|
|Habilitar grupos de seguridad de red en interfaces de red/subredes|Recomienda habilitar los grupos de seguridad de red (NSG) en interfaces de red y subredes (solo VM de Administrador de recursos).|
|Restringir el acceso a través de puntos de conexión externos públicos|Recomienda configurar reglas de tráfico de entrada para los NSG.|
|Habilitar Auditoría SQL de servidor|Recomienda habilitar Auditoría para servidores SQL Server de Azure (solo el servicio SQL de Azure, sin incluir la instancia SQL que se ejecuta en sus máquinas virtuales).|
|Habilitar Auditoría SQL de base de datos|Recomienda habilitar Auditoría para bases de datos SQL de Azure (solo el servicio SQL de Azure, sin incluir la instancia SQL que se ejecuta en sus máquinas virtuales).|
|Habilitar Cifrado de datos transparente en bases de datos SQL|Recomienda habilitar el cifrado de bases de datos SQL (solo el servicio SQL de Azure).|
|Implementar el Agente de VM|Permite ver las VM que requieren el Agente de VM. El Agente de VM debe estar instalado en las VM para aprovisionar Detección de revisiones, Detección de línea base y Antimalware. El Agente de VM está instalado de manera predeterminada en las VM implementadas desde Azure Marketplace. El artículo [VM Agent and Extensions – Part 2](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) (Agente de VM y extensiones, parte 2) proporciona información sobre cómo instalar el Agente de VM.|

Puede filtrar y descartar las recomendaciones.

1. Haga clic en **filtro** en la hoja **Recomendaciones**. Se abrirá la hoja Filtro y podrá seleccionar los valores de gravedad y de estado que quiera ver.

    ![][3]

2. Si determina que una recomendación no corresponde, puede descartar la recomendación y, luego, filtrarla para que no aparezca en la vista. Hay dos formas de descartar una recomendación. Haga clic con el botón derecho en un elemento y seleccione **Descartar**, o bien mantenga el mouse sobre un elemento, haga clic en los tres puntos que aparecen a la derecha y seleccione **Descartar**. Puede ver las recomendaciones descartadas haciendo clic en **Filtro** y seleccionando **Descartado**.

    ![][4]

### Aplicación de las recomendaciones
Después de revisar todas las recomendaciones, podrá decidir cuáles debe aplicar primero. Se recomienda usar la clasificación de gravedad como el parámetro principal para evaluar las recomendaciones que se deben aplicar primero. Con la recomendación antimalware, veamos un ejemplo sobre cómo aplicar una recomendación:

1. En la hoja **Recomendaciones**, seleccione **Habilitar antimalware**. ![][5]

2. En la hoja **Instalar antimalware**, seleccione desde la lista de máquinas virtuales sin antimalware habilitado y haga clic en **Instalar antimalware**.
3. Se abre la hoja **Nuevo recurso** que le permite seleccionar la solución antimalware que desea usar. Seleccione **Microsoft Antimalware**.
4. Aparece información adicional sobre la solución antimalware. Seleccione **Crear**.
5. Escriba las opciones de configuración requeridas en la hoja **Agregar extensión** y seleccione **Aceptar**. ![][6]

[Microsoft Antimalware](../azure-security/azure-security-antimalware.md) ahora está activo en la máquina virtual seleccionada.

### Implementación de soluciones de socios recomendadas

Una recomendación puede indicar implementar una solución de seguridad integrada de un asociado de Microsoft. Veamos un ejemplo de cómo hacer esto:

1. Vuelva a la hoja **Recomendaciones**.
2.	Seleccione la recomendación **Proteger la aplicación web con Firewall de aplicaciones web**. Se abrirá la hoja **Aplicaciones web no protegidas**. ![][7]
3. Seleccione una aplicación web; se abrirá la hoja **Agregar un Firewall de aplicaciones web**.
4. Seleccione **Firewall de aplicaciones web de Barracuda**. Se abrirá una hoja con información sobre **Firewall de aplicaciones web de Barracuda**.
5. Haga clic en **Crear** en la hoja de información. Se abrirá la hoja **Nuevo Firewall de aplicaciones web** donde puede realizar los pasos para la **Configuración de VM** y brindar **Información sobre el WAF**.
6. Seleccione **Configuración de VM**. En la hoja **Configuración de VM**, se ingresa la información requerida para poner en marcha la máquina virtual que ejecutará el WAF. ![][8]
7. Vuelva a la hoja **Nuevo Firewall de aplicaciones web** y seleccione **Información de WAF**. En la hoja **Información de WAF**, configure el WAF. El paso 6 le permite configurar la máquina virtual en que se ejecutará el WAF y el paso 7 le permite aprovisionar el WAF.

8. Vuelva a la hoja **Recomendaciones**. Una vez que creó el WAF, se generó una entrada nueva, **Finalizar la configuración del Firewall de aplicaciones web**. Esto le permite saber que debe completar el proceso de conectar el WAF dentro de la Red virtual de Azure para que pueda proteger la aplicación. ![][9]

9. Seleccione **Finalizar la configuración del Firewall de aplicaciones web** y se abrirá una hoja nueva. Podrá ver que hay una aplicación web que necesita que el tráfico se redirija.
10. Seleccione la aplicación web y se abrirá una hoja con los pasos necesarios para finalizar la configuración del firewall de aplicaciones web. Complete los pasos, haga clic en **Restringir tráfico** y el Centro de seguridad de Azure hará la conexión por usted. ![][10]

Los registros de ese WAF ahora están totalmente integrados. El Centro de seguridad de Azure puede comenzar a recopilar y analizar automáticamente los registros para comunicarle las alertas de seguridad importantes.

## Pasos siguientes
En este documento, pudo conocer las recomendaciones de seguridad del Centro de seguridad de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): Obtenga información sobre cómo configurar las directivas de seguridad.
- [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md): obtenga información acerca de cómo supervisar el estado de los recursos de Azure
- [Administración de alertas de seguridad y respuesta a estas en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): Obtenga información sobre cómo administrar alertas de seguridad y responder a estas.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): Encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): Encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[2]: ./media/security-center-recommendations/recommendations-tile.png
[3]: ./media/security-center-recommendations/filter-recommendations.png
[4]: ./media/security-center-recommendations/dismiss-recommendations.png
[5]: ./media/security-center-recommendations/select-enable-antimalware.png
[6]: ./media/security-center-recommendations/install-antimalware.png
[7]: ./media/security-center-recommendations/secure-web-application.png
[8]: ./media/security-center-recommendations/vm-configuration.png
[9]: ./media/security-center-recommendations/finalize-waf.png
[10]: ./media/security-center-recommendations/restrict-traffic.png

<!---HONumber=AcomDC_0128_2016-->