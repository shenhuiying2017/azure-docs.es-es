<properties
	pageTitle="Conexión de Operations Manager con Log Analytics | Microsoft Azure"
	description="Para mantener su inversión existente en System Center Operations Manager y usar las funcionalidades ampliadas con Log Analytics, puede integrar Operations Manager con el área de trabajo OMS."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Conexión de Operations Manager con Log Analytics

Para mantener su inversión existente en System Center Operations Manager y usar las funcionalidades ampliadas con Log Analytics, puede integrar Operations Manager con el área de trabajo OMS. Esto le permite aprovechar las oportunidades de OMS mientras sigue utilizando Operations Manager para:

- Seguir supervisando el estado de los servicios de TI con Operations Manager
- Mantener la integración con sus soluciones ITSM compatibles con la administración de incidentes y problemas
- Administrar el ciclo de vida de los agentes implementados en máquinas virtuales IaaS en nube pública y en local que supervise con Operations Manager

La integración con System Center Operations Manager agrega valor a su estrategia de operaciones de servicio al aprovechar la velocidad y eficacia de OMS para recopilar, almacenar y analizar datos de Operations Manager. OMS ayuda a correlacionar y a avanzar para identificar los errores de los problemas y a sacar a la superficie reapariciones para respaldar el proceso de administración de problema existente. La flexibilidad del motor de búsqueda para examinar el rendimiento, eventos y datos de alertas con abundantes paneles y funcionalidades de informes para exponer estos datos de maneras significativas muestra la solidez que ofrece OMS como complemento de Operations Manager.

Los agentes que informan al grupo de administración de Operations Manager recopilan datos de los servidores en función de los orígenes de datos de Log Analytics y de las soluciones habilitadas en su suscripción de OMS. Dependiendo de la solución habilitada, los datos de estas soluciones se envían directamente desde un servidor de administración de Operations Manager al servicio web de OMS o bien, debido al volumen de los datos recopilados en el sistema administrado por agentes, se envían directamente desde el agente al servicio web de OMS. El servidor de administración no reenvía directamente los datos OMS al servicio web de OMS, nunca se almacenan en la base de datos de Operations Manager o OperationsManagerDW. Cuando un servidor de administración pierde conectividad con el servicio web de OMS, almacena en caché los datos localmente hasta que la comunicación se restablezca con OMS. Si el servidor de administración está sin conexión debido a un mantenimiento planeado o a una interrupción imprevista, otro servidor de administración del grupo de administración reanudará la conectividad de OMS.

El siguiente diagrama muestra la relación entre System Center Operations Manager y OMS.

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

## Requisitos del sistema
Antes de comenzar, revise los detalles siguientes para comprobar que cumple los requisitos previos necesarios.

- OMS admite únicamente Operations Manager 2012 SP1 UR6 y versiones posteriores y Operations Manager 2012 R2 UR2 y versiones posteriores. Se agregó compatibilidad con proxy en Operations Manager 2012 SP1 UR7 y Operations Manager 2012 R2 UR3.
- Una suscripción de OMS. Para más información, consulte [Get started with Log Analytics](log-analytics-get-started.md) (Introducción a Log Analytics).

## Conexión de Operations Manager con OMS
Ejecute la siguiente serie de pasos para configurar el grupo de administración de Operations Manager para conectarse a una de las áreas de trabajo de OMS.

1. En la consola de Operations Manager, seleccione el área de trabajo **Administración**.
2. Expanda el nodo de Operations Management Suite y haga clic en **Conexión**.
3. Haga clic en el vínculo **Register to Operations Management Suite** (Registrar en Operations Management Suite).
4. En la página **Asistente para incorporación a Operations Management Suite: Autenticación**, escriba la dirección de correo electrónico o el número de teléfono y la contraseña de la cuenta de administrador que está asociada a su suscripción de OMS y haga clic en **Iniciar sesión**.
5. Una vez correctamente autenticado, en la página **Operations Management Suite Onboarding Wizard: Select Workspace** (Asistente para incorporación de Operations Management Suite: Área de trabajo), se le pedirá que seleccione el área de trabajo de OMS. Si tiene más de un área de trabajo, seleccione la desea registrar con el grupo de administración de Operations Manager en la lista desplegable y haga clic en **Siguiente**.
    >[AZURE.NOTE] Operations Manager solo admite un área de trabajo de OMS a la vez. Se quitan de OMS la conexión y los equipos que se registraron en OMS con el área de trabajo anterior.
6. En la página **Operations Manager Suite Onboarding Wizard: Summary** (Asistente para incorporación de Operations Manager Suite: Resumen), confirme la configuración y, si es correcta, haga clic en **Crear**.
7. En la página **Operations Management Suite Onboarding Wizard: Finish** (Asistente para incorporación de Operations Management Suite: Finalizar), haga clic en **Cerrar**.

### Adición de equipos administrados por agente
Después de configurar la integración con el área de trabajo OMS, esto solo establece una conexión con OMS, no se recopilan datos de los agentes que informan al grupo de administración. Esto no será posible hasta que configure qué los equipos administrados por agente específicos recopilarán los datos para Log Analytics. Puede seleccionar los objetos de equipo individualmente o un grupo que contenga los objetos de equipos Windows. No puede seleccionar un grupo que contenga las instancias de otra clase, como discos lógicos o bases de datos SQL.

1. Abra la consola de Operations Manager y seleccione el área de trabajo **Administración**.
2. Expanda el nodo de Operations Management Suite y haga clic en **Conexión**.
3. Haga clic en el vínculo **Agregar un equipo/grupo** debajo del título Acciones en el lado derecho del panel.
4. En el cuadro de diálogo **Búsqueda de equipos** puede seleccionar los equipos o grupos supervisados por Operations Manager. Seleccione los equipos o grupos que quiera incorporar a OMS, haga clic en **Agregar** y, después, haga clic en **Aceptar**.

Puede ver los equipos y grupos configurados para recopilar datos desde el nodo Equipos administrados de Operations Management Suite en el área de trabajo **Administración** de la consola de Operations. Desde aquí puede agregar o quitar equipos y grupos según sea necesario.

### Configuración del proxy de OMS en la consola de Operations
Realice los pasos siguientes si un servidor proxy interno se encuentra entre el grupo de administración y el servicio web de OMS. Esta configuración se administra centralmente desde el grupo de administración y se distribuye en sistemas administrados por agente que se incluyen en el ámbito para recopilar datos para OMS. Esto es beneficioso cuando determinadas soluciones omiten el servidor de administración y envían datos directamente al servicio web de OMS.

1. Abra la consola de Operations Manager y seleccione el área de trabajo **Administración**.
2. Expanda Operations Management Suite y haga clic en **Conexiones**.
3. En la vista Conexión de OMS, haga clic en **Configurar servidor proxy**.
4. En la página **Operations Management Suite Wizard: Proxy Server** (Asistente de configuración de Operations Management Suite: servidor proxy), seleccione **Uso de un servidor proxy para acceder a Operations Management Suite**, escriba la dirección URL con el número de puerto, por ejemplo, http://corpproxy:80 y después haga clic en **Finalizar**.

Si el servidor proxy requiere autenticación, realice los pasos siguientes para configurar las credenciales y las opciones necesarias para propagar a los equipos administrados que notificarán a OMS en el grupo de administración.

1. Abra la consola de Operations Manager y seleccione el área de trabajo **Administración**.
2. En **RunAs Configuration**, seleccione **Perfiles**.
3. Abra el perfil **System Center Advisor Run As Profile Proxy** (Proxy del perfil de ejecución de System Center Advisor).
4. En el Asistente para crear perfiles de ejecución, haga clic en Agregar para usar una cuenta de ejecución. Puede crear una nueva [cuenta de ejecución](https://technet.microsoft.com/library/hh321655.aspx) o usar una existente. Esta cuenta debe contar con los permisos suficientes para pasar a través del servidor proxy.
5. Para establecer la cuenta que se va a administrar, elija **una clase seleccionada, un grupo o un objeto**, haga clic en **Seleccionar...** y después en **Grupo...** para abrir el cuadro **Búsqueda de grupos**.
6. Busque y seleccione **Grupo de servidores de supervisión de Microsoft System Center Advisor**. Haga clic en **Aceptar** después de seleccionar el grupo para cerrar el cuadro **Búsqueda de grupos**.
7.	Haga clic en **Aceptar** para cerrar el cuadro **Agregar una cuenta de ejecución**.
8.	Haga clic en **Guardar** para completar el asistente y guardar los cambios.

Después de crear la conexión y de configurar qué agentes van a recopilar los datos, e informar sobre ellos, en OMS, se aplica la siguiente configuración en el grupo de administración, no necesariamente en orden:

- Se crea la cuenta de ejecución **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**. Está asociada con el perfil de ejecución **Microsoft System Center Advisor Run As Profile Blob** (Blob del perfil de ejecución de Microsoft System Center Advisor) y está dirigida a dos clases: **Servidor de recopilación** y **Grupo de administración de Operations Manager**.
- Se crean dos conectores. La primera se denomina **Microsoft.SystemCenter.Advisor.DataConnector** y se configura automáticamente con una suscripción que reenviará todas las alertas generadas desde instancias de todas las clases en el grupo de administración para Log Analytics de OMS. El segundo conector es **Advisor Connector**, que es responsable de comunicarse con el servicio web de OMS y de compartir datos.
- Se agregarán los gentes y grupos que haya seleccionado para recopilar datos del grupo de administración al **grupo de servidores de supervisión de Microsoft System Center Advisor**.

## Actualizaciones del módulo de administración
Después de finalizar la configuración, el grupo de administración de Operations Manager establece una conexión con el servicio de OMS. El servidor de administración se sincronizará con el servicio web y recibirá información de configuración actualizada en forma de módulos de administración para las soluciones que ha habilitado que se integran con Operations Manager. Operations Manager buscará actualizaciones para esos módulos de administración de estos módulos y los descargará e importará automáticamente cuando estén disponibles. Hay dos reglas en particular que controlan este comportamiento:

- **Microsoft.SystemCenter.Advisor.MPUpdate**: actualiza los módulos de administración base de OMS. Se ejecuta cada doce (12) horas de forma predeterminada.
- **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule**: actualiza los módulos de administración de las soluciones habilitados en el área de trabajo. Se ejecuta cada cinco (5) minutos de forma predeterminada.

Puede invalidar estas dos reglas para evitar la descarga automática mediante la deshabilitación o modificación de la frecuencia de sincronización del servidor de administración con OMS para determinar si está disponible un nuevo módulo de administración y si debe descargarse. Siga los pasos [Reemplazo de una regla o monitor](https://technet.microsoft.com/library/hh212869.aspx) para modificar el parámetro **Frequency** con un valor en segundos para cambiar la programación de sincronización o modificar el parámetro **Enabled** para deshabilitar las reglas. Dirige las invalidaciones para todos los objetos de la clase del grupo de administración de Operations Manager.

Si desea seguir el proceso de control de cambios existente para controlar las versiones del módulo de administración en el grupo de administración de producción, puede deshabilitar las reglas y habilitarlas durante determinadas horas cuando se permiten las actualizaciones. Si tiene un desarrollo o un grupo de administración de control de calidad en su entorno y tiene conectividad a Internet, puede configurar ese grupo de administración con un área de trabajo de OMS para admitir este escenario. Esto le permitirá revisar y evaluar las versiones iterativas de los módulos de administración de OMS antes de publicarlas en el grupo de administración de producción.

## Validación de la integración de Operations Manager con OMS
Hay varias maneras en las que puede comprobar que la integración de OMS con Operations Manager es correcta.

### Para confirmar la integración desde el portal de OMS

1.	En el portal de OMS, haga clic en el icono **Configuración**.
2.	En el menú superior, haga clic en **Connected Sources** (Orígenes conectados).
3.	En la sección de System Center Operations Manager, verá el estado **1 MGMT Group Connected** (1 grupo MGMT conectado) y en la tabla debajo de él, aparece el nombre del grupo de administración con el número de agentes y el estado de cuando se recibieron los datos por última vez.

### Para confirmar la integración desde la consola de Operations

1.	Abra la consola de Operations Manager y seleccione el área de trabajo **Administración**.
2.	Haga clic en el nodo **Módulos de administración** y en el cuadro de texto **Buscar:**, escriba **Advisor** o **Intelligence**.
3.	Dependiendo de las soluciones habilitadas, verá un módulo de administración correspondiente mostrado en los resultados de búsqueda. Por ejemplo, si ha habilitado la solución de administración de alertas, el módulo de administración de Administración de alertas de Microsoft System Center Advisor estará en la lista.

## Pasos siguientes

- Consulte [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Incorporación de soluciones de Log Analytics desde la galería de soluciones) para agregar funcionalidad y recopilar información.
- Consulte [Configure proxy and firewall settings in Log Analytics](log-analytics-proxy-firewall.md) (Configuración del proxy y del firewall en Log Analytics) si la organización usa un servidor proxy o un firewall, para que los agentes se puedan comunicar con el servicio de Log Analytics.

<!---HONumber=AcomDC_0504_2016-->