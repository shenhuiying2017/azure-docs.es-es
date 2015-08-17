<properties
    pageTitle="Configurar el área de trabajo y administrar la configuración"
    description="Obtenga información acerca de cómo configurar el área de trabajo y administrar la configuración de Visión operativa de Microsoft Azure"
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
    ms.topic="get-started-article"
    ms.date="08/05/2015"
    ms.author="banders"/>

# Configurar el área de trabajo y administrar la configuración

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Para crear una nueva área de trabajo de Visión operativa de Microsoft Azure, elija un nombre para el área de trabajo, asócielo con la cuenta y elija una ubicación geográfica. Un área de trabajo de Visión operativa es esencialmente un contenedor que incluye información de la cuenta e información de configuración sencilla para la cuenta. Usted u otros miembros de su organización pueden usar varias áreas de trabajo de Visión operativa para administrar distintos conjuntos de datos que se recopilan de todas o de algunas partes de su infraestructura de TI.

Después de crear el área de trabajo, puede realizar otras tareas a través del área de trabajo, tales como administrar Visión operativa, agregar una solución, conectar orígenes de datos, agregar registros, seleccionar cuentas de almacenamiento, ver datos de uso en el panel y administrar la configuración para cada área de trabajo.

El artículo [Incorporado en minutos](./operational-insights-onboard-in-minutes.md) muestra cómo realizar la activación rápidamente. El resto del artículo describe más detalladamente algunas de las acciones que tendrá que realizar para finalizar la activación y administrar el área de trabajo.

Hablaremos sobre todas las tareas que se realizan con frecuencia que usa en las secciones siguientes:

1. Adición de soluciones
2. Conexión de orígenes de datos
3. Adición y administración de registros
4. Administración de cuentas y usuarios

![pasos](./media/operational-insights-setup-workspace/steps.png)
## 1 Adición de soluciones

Visión operativa de Microsoft Azure incluye la funcionalidad base de evaluación de configuración, por lo que no tiene que instalar una solución para habilitarlo. Sin embargo, puede obtener una mayor funcionalidad si le agrega soluciones desde las páginas Configuración o Galería de soluciones.

Tras haber agregado una solución, se recopilan datos de los servidores de la infraestructura y se envían al servicio Visión operativa. El procesamiento por parte del servicio Visión operativa puede tardar desde unos minutos hasta varias horas. Una vez que el servicio ha procesado los datos, podrá verlos en Visión operativa.

Es posible quitar fácilmente una solución cuando ya no es necesaria. Al quitar una solución, sus datos no se enviarán a Visión operativa, lo que reduce la cantidad de datos usados por la cuota diaria.

### Soluciones admitidas por Microsoft Monitoring Agent

En este momento, los servidores conectados directamente a Visión operativa de Microsoft Azure mediante Microsoft Monitoring Agent pueden usar la mayoría de las soluciones disponibles, incluidas las siguientes:

- [Actualizaciones del sistema](operational-insights-updates.md)
- [Antimalware](operational-insights-antimalware.md)
- [Seguimiento de cambios](operational-insights-change-tracking.md)
- [Evaluación de SQL y Active Directory](operational-insights-assessment.md)

Sin embargo, las siguientes soluciones *no* son compatibles con Microsoft Monitoring Agent y requieren System Center Operations Manager (SCOM).

- [Administración de la capacidad](operational-insights-capacity.md)
- [Administración de alertas](operational-insights-alerts.md)
- [Evaluación de la configuración](operational-insights-solutions.md#configuration-assessment)

Consulte [Consideraciones de Operations Manager con Visión operativa](operational-insights-operations-manager.md) para obtener instrucciones sobre el uso de estas soluciones con Operations Manager.

La colección de registros de IIS se admite en equipos con:

- Windows Server 2012
- Windows Server 2012 R2

### Para agregar soluciones mediante la página Configuración

- Seleccione las soluciones que desee agregar y, a continuación, haga clic en **Agregar soluciones seleccionadas**. No se muestran todas las soluciones disponibles aquí. Si desea agregar soluciones que no aparecen, use el procedimiento siguiente. ![adición de soluciones](./media/operational-insights-setup-workspace/settings-add-sol.png)

### Para agregar una solución mediante la Galería de soluciones

1. En la página de información general de Visión operativa, haga clic en el icono **Galería de soluciones**. ![imagen del icono de soluciones](./media/operational-insights-setup-workspace/sol-gallery.png)
2. En la página Galería de soluciones de Visión operativa, puede obtener información acerca de cada una de las soluciones disponibles. Haga clic en el nombre de la solución que desea agregar a Visión operativa.
3. En la página de la solución que eligió, aparecerá información detallada sobre la solución. Haga clic en **Agregar**.
4. En la página de confirmación, haga clic en **Aceptar** para aceptar la declaración de privacidad y las condiciones de uso.
5. En la página de información general de Visión operativa aparece un icono nuevo para la solución que ha agregado y puede empezar a usarlo una vez que el servicio mencionado procese los datos.

### Para quitar una solución mediante la Galería de soluciones

1. En la página de información general de Visión operativa, haga clic en el icono **Galería de soluciones**.
2. En la página Galería de soluciones de Visión operativa, haga clic en **Quitar** bajo la solución que desea quitar.
3. En la página de confirmación, haga clic en **Sí** para quitar la solución.

## 2 Conexión de orígenes de datos

Existen tres maneras de conectarse a orígenes de datos:

- Vinculación de equipos directamente a Visión operativa Consulte [Conexión de equipos directamente en Visión operativa](./operational-insights-direct-agent.md) para obtener más información. ![conexión directa](./media/operational-insights-setup-workspace/attach-directly.png)
- Vinculación de grupos de administración de Operations Manager Consulte [Conexión a Vista operativa desde System Center Operations Manager](./operational-insights-connect-scom.md) para obtener más información. ![conexión de Operations Manager](./media/operational-insights-setup-workspace/attach-om.png)
- Vinculación de una cuenta de almacenamiento de Azure Consulte [Análisis de datos de servidores en Microsoft Azure](./operational-insights-analyze-data-azure.md) para obtener más información. ![conexión de Azure](./media/operational-insights-setup-workspace/attach-azure.png)

## 3 Adición y administración de registros

Antes de agregar registros, deberá tener una solución que utilizará los datos de registro instalados. A continuación, puede agregar nuevos registros para recopilar eventos y elegir qué niveles de eventos o gravedad desea recopilar para los registros. Puede recopilar:

- Registros de eventos de Windows
- Registros IIS
- Otros registros que ha agregado

![adición de registros](./media/operational-insights-setup-workspace/collect-logs.png)

### Formato de archivo de registro de IIS

El único formato de registro de IIS compatible hasta el momento es W3C. No se preocupe, es el formato más común y el formato predeterminado en IIS 7 y IIS 8. De modo que, si registra en formato nativo NCSA o IIS, Operational Insights no seleccionará esos registros. Incluso en formato W3C, verá que no todos los campos se registran de forma predeterminada. Puede obtener más información sobre el formato en [Selección de campos W3C para registrar (IIS 7)](https://technet.microsoft.com/library/cc754702(v=WS.10).aspx).


> [AZURE.TIP]Para obtener la mejor experiencia de búsqueda de registros, se recomienda seleccionar todos los campos de registro de cada sitio web mediante el uso de **Registro** en IIS. También se recomienda cambiar la programación de **Conversión de archivos de registro** para los nuevos registros a **Cada hora**, de modo que los archivos más pequeños se actualizarán en la nube, lo que supondrá un ahorro en el ancho de banda.


### Para recopilar registros de eventos de Windows desde Operations Manager o agentes directamente conectados

1. En la página de **información general**, haga clic en el icono **Configuración** y, a continuación, haga clic en la pestaña **Registros**.
2. Escriba el nombre del registro de eventos del que desea recopilar información. Si no está seguro del nombre que desea usar, seleccione las propiedades del registro de eventos de Windows en el **Visor de eventos**, copie el nombre en el campo **FullName** y péguelo en el cuadro **Recopilar eventos de los siguientes registros de eventos**.
3. Haga clic en **+** para agregar el registro.
4. Seleccione los niveles de eventos o la gravedad que desea recopilar para el registro. Los eventos **Auditoría correcta** y **Error de auditoría** no se admiten en esta versión.
5. Repita los pasos anteriores para cada registro del que desee recopilar información y luego haga clic en **Guardar**.
6. Los eventos deben aparecer en Operational Insights en unos minutos y luego puede realizar búsquedas en los datos.

### Para recopilar registros de IIS de Operations Manager o de agentes conectados directamente, siga estos pasos:

1. En la página de **información general**, haga clic en el icono **Configuración** y, a continuación, haga clic en la pestaña **Registros**.
2. En la pestaña **Registros**, en **Registros de eventos**, seleccione **Recopilar registros de Operations Manager**.


### Para recopilar registros de IIS o eventos de Windows desde Diagnósticos de Azure
Esto se configura desde el Portal de administración de Azure, no desde el portal de Visión operativa; en el área de trabajo, vaya a la pestaña **Almacenamiento** y habilite la recopilación de registros desde esa cuenta de almacenamiento.

### Después de que se configure la recopilación de registros
Después de que se configure la recopilación de registros, la directiva de recopilación se envía a agentes o a través de grupos de administración a agentes y el servicio comienza a recopilar eventos.

Puede obtener acceso a algunos desgloses iniciales de eventos de registro recopilados a partir de servidores controlados mediante la visualización de la página **Uso**.

![imagen del icono de página de uso](./media/operational-insights-setup-workspace/usage.png)


## 4 Administración de cuentas y usuarios
Administre cuentas y usuarios con la pestaña **Cuentas** en la página Configuración. Aquí puede realizar las siguientes tareas:

![pestaña de cuentas](./media/operational-insights-setup-workspace/manage-users.png)

## Agregar un usuario a un área de trabajo existente


Use los pasos siguientes para agregar un usuario o grupo a un área de trabajo de Visión operativa. El usuario o grupo será capaz de ver y actuar en todas las alertas asociadas a esta área de trabajo.

>[AZURE.NOTE]Si quiere agregar un usuario o grupo de su cuenta profesional de Azure Active Directory, primero debe asegurarse de que se ha asociado su cuenta de Visión operativa con el dominio de Active Directory. Consulte [Agregar una organización de Azure Active Directory a un área de trabajo existente](#).

### Para agregar un usuario a un área de trabajo existente
1. En Visión operativa, haga clic en el icono **Configuración**.
2. Haga clic en la pestaña **Cuentas**.
3. En la sección **Administrar usuarios**, elija el tipo de cuenta que agregar: **Cuenta profesional** o **Cuenta de Microsoft**.
    - Si elige Cuenta de Microsoft, escriba la dirección de correo electrónico del usuario asociado a la cuenta de Microsoft.
    - Si elige Cuenta profesional, puede escribir parte del nombre del grupo o usuario o el alias de correo electrónico, y aparecerá una lista de usuarios y grupos. Seleccione un usuario o grupo.
        >[AZURE.NOTE]Para obtener los mejores resultados de rendimiento, limite el número de grupos de Active Directory asociados a una sola cuenta de Visión operativa a dos: uno para administradores y otro para usuarios. Usar más grupos podría afectar al rendimiento de Visión operativa.
7. Elija el tipo de usuario o grupo que agregar: **Administrador** o **Usuario**.  
8. Haga clic en **Agregar**.

  Si va a agregar una cuenta de Microsoft, se envía una invitación para unir el área de trabajo al correo electrónico que proporcionó. Una vez que el usuario sigue las instrucciones de la invitación para unirse a Visión operativa, el usuario podrá ver las alertas y la información de cuenta para esta cuenta Visión operativa y podrá ver la información de usuario en la pestaña **Cuentas** de la página **Configuración**. Si va a agregar una cuenta profesional, el usuario podrá acceder a Visión operativa inmediatamente. ![invitación](./media/operational-insights-setup-workspace/manage-users04.png)


### ¿Cuántas áreas de trabajo necesito?
Un área de trabajo se ve como un recurso de Azure en el Portal de administración de Azure.

Puede crear un área de trabajo nueva o vincular un área de trabajo existente que puede haber abierto anteriormente para su uso con System Center Operations Manager, pero que todavía no ha asociado a una suscripción de Azure (necesaria para la facturación). Un área de trabajo representa el nivel en el que los datos se recopilan, agregan, analizan y presentan en el portal de Visión operativa. Puede tener varias áreas de trabajo para separar los datos de diferentes entornos y sistemas; cada grupo de administración de Operations Manager (y todos sus agentes) o los agentes/máquinas virtuales individuales solo pueden conectarse a una sola área de trabajo.

Cada área de trabajo puede tener varias cuentas de usuario asociadas y cada una de ellas (cuenta de Microsoft o cuenta profesional) puede tener acceso a varias áreas de trabajo de Visión operativa. De forma predeterminada, la cuenta de Microsoft o cuenta profesional que se usa para crear el área de trabajo se convierte en el administrador del área de trabajo. Después, el administrador puede invitar a cuentas de Microsoft adicionales o seleccionar usuarios de su Azure Active Directory.

## Vincular un área de trabajo existente a una suscripción de Azure

Es posible crear un área de trabajo desde [microsoft.com/oms](https://microsoft.com/oms). Sin embargo, existen ciertos límites en estas áreas de trabajo, el más notable es el límite de 500 MB/día para cargar datos si usa una cuenta gratuita. Para realizar cambios en esta área de trabajo deberá **vincular el área de trabajo existente a una suscripción de Azure**.

>[AZURE.IMPORTANT]Para vincular un área de trabajo, su cuenta de Azure debe tener acceso al área de trabajo que quiere vincular. En otras palabras, la cuenta que usa para acceder al portal de Azure debe ser **la misma** que la cuenta que usa para tener acceso al área de trabajo de Visión operativa. Si no es el caso, consulte [Agregar un usuario a un área de trabajo existente](#add-an-azure-active-directory-organization-to-an-existing-workspace).

1. Inicie sesión en el Portal de administración de Azure.
2. En la parte inferior izquierda del portal, haga clic en **+ Nuevo**.
3. Haga clic en **Servicios de aplicaciones**, desplácese a **Visión operativa** y selecciónela.
4. Haga clic en **Creación rápida**.
5. En la lista **Cuenta**, debería ver una lista de las áreas de trabajo existentes que *todavía no* se vincularon a la suscripción de Azure. Seleccione una cuenta.

  >[AZURE.NOTE]Si no ve el área de trabajo que desee vincular aquí, significa que su suscripción de Azure no tiene acceso al área de trabajo de Visión operativa. Debe conceder acceso a esta cuenta desde dentro de su área de trabajo de Visión operativa. Si ello, consulte [Agregar un usuario a un área de trabajo existente](#add-a-user-to-an-existing-workspace).

  ![vincular cuenta](./media/operational-insights-setup-workspace/link-account.png) <p> 6. Rellene los campos restantes y, luego, seleccione **Crear área de trabajo**.

## Actualización del área de trabajo a un plan de datos de pago

Existen tres planes de datos diferentes para el área de trabajo de Visión operativa: **Gratis**, **Estándar** y **Premium**. Si dispone de una plan *gratis*, puede haber alcanzado su límite de datos de 500 MB. Necesitará actualizar el área de trabajo a un '**plan de pago por uso**' para recopilar datos una vez alcanzado el límite. Puede convertir el tipo de plan en cualquier momento. Para obtener más información acerca de Visión operativa, consulte [Detalles de precios](http://azure.microsoft.com/pricing/operational-insights/).

>[AZURE.IMPORTANT]Los planes de área de trabajo solo pueden cambiarse si están *vinculados* a una suscripción de Azure. Si creó el área de trabajo en Azure o si *ya* vinculó el área de trabajo, puede ignorar este mensaje. Si creó el área de trabajo desde [opinsights.azure.com](http://opinsights.azure.com), deberá seguir los pasos descritos en [Vincular un área de trabajo existente a una suscripción de Azure](#link-an-existing-workspace-to-an-Azure-subscription).

### Cambiar el tipo de plan

En el Portal de administración de Azure, desplácese hasta el área de trabajo de Visión operativa al que quiere actualizar:

![scale](./media/operational-insights-setup-workspace/find-workspace.png)

Seleccione este área de trabajo y seleccione la pestaña **ESCALA** en la parte superior de la pantalla

![seleccionar escala](./media/operational-insights-setup-workspace/select-scale.png)

Por último, elija el plan al que quiere actualizar y haga clic en **GUARDAR**. Verá los cambios reflejados en el portal y podrá recopilar datos por encima del límite de datos "gratuito".

![seleccionar plan](./media/operational-insights-setup-workspace/plan-select.png)

## Agregar una organización de Azure Active Directory a un área de trabajo existente

Puede asociar el área de trabajo de Visión operativa a un dominio de Azure Active Directory. Esto le permite agregar usuarios desde Active Directory directamente al área de trabajo de Visión operativa, sin necesidad de una cuenta independiente de Microsoft.

### Para agregar una organización de Azure Active Directory a un área de trabajo existente

1. En la página Configuración de Visión operativa, haga clic en **Cuentas** y, a continuación, en **Agregar organización**. ![invitación](./media/operational-insights-setup-workspace/add-org.png)
2. Revise la información acerca de las cuentas profesionales y, luego, haga clic en **Siguiente**.
3. Escriba la información de identidad del administrador del dominio de Azure Active Directory y, luego, haga clic en **Iniciar sesión**.
4. Haga clic en **Conceder acceso** para permitir que Visión operativa pueda usar la información de identidad en el dominio de Active Directory. ![vinculados](./media/operational-insights-setup-workspace/ad-existing01.png)


## Edición de un tipo de usuario existente

Puede cambiar el rol de la cuenta para un usuario asociado a su cuenta de Visión operativa. Tiene las siguientes opciones de rol:

 - *Administrador*: puede administrar usuarios, ver y actuar en todas las alertas y agregar y quitar servidores

 - *Usuario*: puede ver y actuar en todas las alertas y agregar y quitar servidores

### Para editar una cuenta
1. En la página **Configuración** en la pestaña **Cuentas** en Visión operativa, seleccione el rol para el usuario que desee cambiar.
2. Haga clic en **Aceptar**.

## Eliminar a un usuario de un área de trabajo de Visión operativa

Use los pasos siguientes para eliminar a un usuario de un área de trabajo de Visión operativa. Tenga en cuenta que esto no cierra el área de trabajo del usuario. En su lugar, elimina la asociación entre ese usuario y el área de trabajo. Si un usuario está asociado a varias áreas de trabajo, dicho usuario podrá iniciar sesión en Visión operativa.

### Para eliminar a un usuario de un área de trabajo

1. En la página **Configuración** en la pestaña **Cuentas** de Visión operativa, haga clic en Quitar junto al nombre de usuario que desee quitar.
2. Haga clic en **Aceptar** para confirmar que quiere eliminar al usuario.

## Cerrar el área de trabajo de Visión operativa

Al cerrar un área de trabajo de Visión operativa, todos los datos relacionados con el área de trabajo se eliminan del servicio de Visión operativa en un plazo de 30 días a partir del cierre del área de trabajo.

Si es administrador, y hay varios usuarios asociados con el área de trabajo, se interrumpirá la asociación entre los usuarios y el área de trabajo. Si los usuarios están asociados a otras áreas de trabajo, podrán seguir usando Visión operativa con las otras áreas de trabajo. Sin embargo, si no están asociados a otras áreas de trabajo, necesitarán crear una nueva área de trabajo para usar Visión operativa.

### Para cerrar un área de trabajo de Visión operativa

1. En la página **Configuración** en la pestaña **Cuentas** de Visión operativa, haga clic en **Cerrar área de trabajo**.

2. Seleccione uno de los motivos para cerrar el área de trabajo o escriba un motivo distinto en el cuadro de texto.

3. Haga clic en **Cerrar área de trabajo**.

## Recursos adicionales
- [Requisitos de formato de registro de IIS en Azure Operational Insights](http://blogs.technet.com/b/momteam/archive/2014/09/19/iis-log-format-requirements-in-system-center-advisor.aspx)
- Vea qué otros orígenes de datos y tipos de registros nos pide la comunidad que implementemos en nuestro [foro de comentarios](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

<!---HONumber=August15_HO6-->