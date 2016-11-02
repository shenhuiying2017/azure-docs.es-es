<properties
    pageTitle="Administración del acceso en Log Analytics | Microsoft Azure"
    description="Para administrar el acceso en Log Analytics, puede usar una serie de tareas administrativas en usuarios, cuentas, áreas de trabajo de OMS y cuentas de Azure."
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
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="banders"/>


# <a name="manage-access-to-log-analytics"></a>Administración del acceso en Log Analytics

Para administrar el acceso en Log Analytics, tendrá que realizar una serie de tareas administrativas en los usuarios, cuentas, áreas de trabajo de OMS y cuentas de Azure. Para crear una nueva área de trabajo en Operations Management Suite (OMS), debe elegir un nombre para el área de trabajo, asociarlo con su cuenta y seleccionar una ubicación geográfica. En esencia, un área de trabajo es un contenedor donde se incluyen los datos de la cuenta e información básica de la configuración de la cuenta. Tanto usted como otros miembros de la organización pueden usar varias áreas de trabajo de OMS para administrar diferentes conjuntos de datos, recopilados a partir de toda la infraestructura de TI o de algunos de sus componentes.

En el artículo [Get started with Log Analytics](log-analytics-get-started.md) (Introducción a Log Analytics), se explica cómo empezar a trabajar rápidamente con esta herramienta y se describen con más detalle algunas de las acciones que deberá realizar para administrar el acceso a OMS.

Aunque es muy posible que al principio no tenga que ejecutar todas las tareas administrativas, en las siguientes secciones, vamos a explicar todas las tareas frecuentes que puede necesitar:

- Determinación del número de áreas de trabajo necesarias
- Administración de cuentas y usuarios
- Incorporación de un grupo a un área de trabajo existente
- Vincular un área de trabajo existente a una suscripción de Azure
- Actualización de un área de trabajo a un plan de datos de pago
- Modificación de un tipo de plan de datos
- Agregar una organización de Azure Active Directory a un área de trabajo existente
- Cerrar el área de trabajo de OMS

## <a name="determine-the-number-of-workspaces-you-need"></a>Determinación del número de áreas de trabajo necesarias

Un área de trabajo es un recurso de Azure y un contenedor donde los datos se recopilan, se agregan, se analizan y se presentan en el portal de OMS.

Es posible crear varias áreas de trabajo de Log Analytics de OMS; asimismo, los usuarios pueden tener acceso a una o varias de estas. Por lo general, se prefiere minimizar el número de áreas de trabajo, ya que de este modo se podrán consultar y poner en correlación la mayoría de los datos. En esta sección se describe cuándo puede resultar útil la creación de más de un área de trabajo.

En la actualidad, un área de trabajo de Log Analytics proporciona lo siguiente:

- Una ubicación geográfica para el almacenamiento de datos
- Granularidad para la facturación
- Aislamiento de datos

Dadas estas características, podría ser interesante crear varias áreas de trabajo en los siguientes casos:

- Una empresa internacional que necesita que los datos estén almacenados en regiones concretas por motivos de soberanía de datos o cumplimiento normativo.
- Un usuario de Azure que desea evitar los gastos de transferencia de datos de salida manteniendo un área de trabajo de Log Analytics en la misma región que los recursos de Azure que esta administra.
- Se desea asignar los gastos a diferentes departamentos o grupos de negocios en función del uso que hagan. Cuando se crea un área de trabajo para cada departamento o grupo de negocios, la factura y la declaración de uso de Azure reflejan los cargos de cada área de trabajo de manera independiente.
- Un proveedor de servicio administrado que necesita mantener los datos de Log Analytics para cada cliente que administra aislados de los datos de otro cliente.
- Se administran varios clientes y se desea que cada cliente, departamento o grupo de negocio vea sus propios datos, pero no los de otros clientes, departamentos o grupos de negocio.

Cuando se utilizan agentes para recopilar datos, se puede configurar cada uno de ellos para que informe al área de trabajo especificada.

Si usa System Center Operations Manager, cada grupo de administración de Operations Manager se puede conectar con una sola área de trabajo. Se puede instalar Microsoft Monitoring Agent en los equipos administrados por Operations Manager y hacer que el agente informe tanto a Operations Manager como a una diferente área de trabajo de Log Analytics.

### <a name="workspace-information"></a>Información del área de trabajo

En el portal OMS, puede ver la información del área de trabajo y elegir si desea recibir información de Microsoft.

#### <a name="view-workspace-information"></a>Visualización de la información de un área de trabajo

1. En OMS, haga clic en el icono **Configuración** .
2. Haga clic en la pestaña **Cuentas** .
3. Haga clic en la pestaña **Información del área de trabajo**.  
  ![Información del área de trabajo](./media/log-analytics-manage-access/workspace-information.png)

## <a name="manage-accounts-and-users"></a>Administración de cuentas y usuarios

Cada área de trabajo puede tener varias cuentas asociadas, y cada cuenta de usuario (ya sea una cuenta de Microsoft o una cuenta profesional) puede tener acceso a varias áreas de trabajo de OMS.

De forma predeterminada, la cuenta de Microsoft o cuenta profesional que se usa para crear el área de trabajo se convierte en el administrador del área de trabajo. Después, el administrador puede invitar a otras cuentas de Microsoft o seleccionar usuarios de Azure Active Directory.

La concesión de acceso a los usuarios en el área de trabajo de OMS se controla en dos lugares:

- En Azure, se puede usar el control de acceso basado en rol para proporcionar acceso a la suscripción de Azure y a los recursos de Azure asociados. También se utiliza para el acceso a PowerShell y la API de REST.
- En el portal OMS, acceso solo al portal OMS, no a la suscripción de Azure asociada.

Si concede a los usuarios acceso al portal OMS, pero no a la suscripción a Azure vinculada, los iconos de las soluciones Automation, Backup y Site Recovery no mostrarán datos a los usuarios cuando inicien sesión en el portal OMS.

Para que todos los usuarios puedan ver los datos en estas soluciones, asegúrese de que, como mínimo, tienen acceso de **lectura** en la cuenta de Automatización, el Almacén de copia de seguridad y el almacén de Site Recovery vinculados al área de trabajo de OMS.   

### <a name="managing-access-to-log-analytics-using-the-azure-portal"></a>Administración del acceso a Log Analytics mediante el Portal de Azure

Si concede acceso a los usuarios al área de trabajo de Log Analytics mediante los permisos de Azure (por ejemplo, en Azure Portal), esos mismos usuarios pueden tener acceso al portal de Log Analytics. Si los usuarios se encuentran en Azure Portal, pueden navegar al portal OMS haciendo clic en la tarea **Portal OMS** al visualizar el recurso del área de trabajo de Log Analytics.

Algunos aspectos relativos a Azure Portal que deben tenerse en cuenta:

- Esto no es *Control de acceso basado en rol*. Si tiene permisos de acceso de *lector* en Azure Portal para el área de trabajo de Log Analytics, puede realizar cambios mediante el portal de OMS. El portal de OMS tiene un concepto de Administrador, Colaborador y Usuario de solo lectura. Si la cuenta con la que ha iniciado sesión está en Azure Active Directory vinculada al área de trabajo, será administrador en el portal OMS; de lo contrario, será colaborador.

- Si inicia sesión en el portal OMS mediante http://mms.microsoft.com, verá la lista **Seleccionar un área de trabajo** de manera predeterminada. Solo contiene las áreas de trabajo que se agregaron mediante el portal OMS. Para ver las áreas de trabajo a las que tiene acceso con las suscripciones de Azure, será preciso que especifique un inquilino como parte de la dirección URL. Por ejemplo:

  `mms.microsoft.com/?tenant=contoso.com` A menudo, el identificador del inquilino es la última parte de la dirección de correo electrónico con la que inicia sesión.

- Si la cuenta con la que inicia sesión se encuentra en la instancia de Azure Active Directory del inquilino, que es lo que suele pasar si no se inicia sesión como CSP, será *administrador* en el portal OMS. Si la cuenta no se encuentra en la instancia de Azure Active Directory del inquilino, será *usuario* en el portal OMS.

- Si desea navegar directamente a un portal al que tenga acceso a través de los permisos de Azure, será preciso que especifique el recurso como parte de la dirección URL. Esta dirección URL se puede obtener mediante PowerShell.

  Por ejemplo, `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  La dirección URL será similar a esta: `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`


### <a name="managing-users-in-the-oms-portal"></a>Administración de usuarios en el portal de OMS

Los usuarios y grupos se administran en la pestaña **Administrar usuarios** de la pestaña **Cuentas** de la página Configuración. En esta página, podrá realizar las tareas que se describen en las secciones siguientes.  

![administrar usuarios](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

#### <a name="add-a-user-to-an-existing-workspace"></a>Agregar un usuario a un área de trabajo existente

Siga estos pasos para agregar un usuario o un grupo a un área de trabajo de OMS. El usuario o grupo será capaz de ver y actuar en todas las alertas asociadas a esta área de trabajo.

>[AZURE.NOTE] Si quiere agregar un usuario o un grupo de la cuenta de organización de Azure Active Directory, primero debe asegurarse de que su cuenta de OMS esté asociada con el dominio de Active Directory. Consulte [Agregar una organización de Azure Active Directory a un área de trabajo existente](#add-an-azure-active-directory-organization-to-an-existing-workspace).

1. En OMS, haga clic en el icono **Configuración** .
2. Haga clic en la pestaña **Cuentas** y, después, en la pestaña **Administrar usuarios**.
3. En la sección **Administrar usuarios**, elija el tipo de cuenta que desea agregar: **Cuenta de organización**, **Cuenta Microsoft** o **Soporte técnico de Microsoft**.
    - Si elige Cuenta de Microsoft, escriba la dirección de correo electrónico del usuario asociado a la cuenta de Microsoft.
    - Si elige Cuenta profesional, puede escribir parte del nombre del grupo o usuario o el alias de correo electrónico, y aparecerá una lista de usuarios y grupos. Seleccione un usuario o grupo.
    - Use el servicio de soporte técnico de Microsoft para dar a un ingeniero de este servicio acceso temporal a su área de trabajo para ayudar en la solución de problemas.

    >[AZURE.NOTE] Para optimizar el rendimiento, limite a tres el número de grupos de Active Directory asociados a una única cuenta de OMS: uno para los administradores, otro para los colaboradores y otro para los usuarios de solo lectura. Si utiliza más grupos, el rendimiento de Log Analytics podría verse afectado.

5. Elija el tipo de usuario o grupo que desea agregar: **Administrador**, **Colaborador** o **Usuario de solo lectura**.  
6. Haga clic en **Agregar**.

  Si va a agregar una cuenta de Microsoft, se envía una invitación para unir el área de trabajo al correo electrónico que proporcionó. Después de seguir las instrucciones que se especifican en la invitación para unirse a OMS, el usuario podrá ver las alertas y los datos de esta cuenta de OMS, mientras que usted podrá ver la información del usuario en la pestaña **Cuentas** de la página **Configuración**.
  Si agrega una cuenta de organización, el usuario podrá acceder a Log Analytics inmediatamente.  
  ![invitación por correo electrónico](./media/log-analytics-manage-access/setup-workspace-invitation-email.png)

#### <a name="edit-an-existing-user-type"></a>Edición de un tipo de usuario existente

Puede cambiar el rol de cuenta de un usuario asociado a su cuenta de OMS. Tiene las siguientes opciones de rol:

 - *Administrador*: puede administrar usuarios, ver y actuar en todas las alertas y agregar y quitar servidores

 - *Colaborador*: puede ver y actuar en todas las alertas y agregar y quitar servidores

 - *Usuario de solo lectura*: los usuarios con este rol podrán hacer lo siguiente:
   1. Agregar o quitar soluciones. Se oculta la galería de soluciones.
   2. Agregar, modificar o eliminar iconos en **Mi panel**.
   3. Vea las páginas de **Configuración**. Las páginas están ocultas.
   4. En la vista de búsqueda, la configuración de PowerBI, las búsquedas guardadas y las tareas de alertas están ocultas.


#### <a name="to-edit-an-account"></a>Para editar una cuenta

1. En OMS, haga clic en el icono **Configuración** .
2. Haga clic en la pestaña **Cuentas** y, después, en la pestaña **Administrar usuarios**.
3. Seleccione el rol del usuario que desea cambiar.
2. En el cuadro de diálogo de confirmación, haga clic en **Sí**.

### <a name="remove-a-user-from-a-oms-workspace"></a>Eliminación de un usuario de un área de trabajo de OMS

Siga los pasos siguientes para quitar un usuario de un área de trabajo de OMS. Tenga en cuenta que esto no cierra el área de trabajo del usuario. En su lugar, elimina la asociación entre ese usuario y el área de trabajo. Si un usuario tiene asociadas varias áreas de trabajo, podrá continuar iniciando sesión en OMS y ver el resto de áreas de trabajo.

1. En OMS, haga clic en el icono **Configuración** .
2. Haga clic en la pestaña **Cuentas** y, después, en la pestaña **Administrar usuarios**.
3. Haga clic en **Quitar** junto al nombre de usuario que desea eliminar.
4. En el cuadro de diálogo de confirmación, haga clic en **Sí**.


### <a name="add-a-group-to-an-existing-workspace"></a>Incorporación de un grupo a un área de trabajo existente

1.  Siga los pasos 1 a 4 indicados más arriba para agregar un usuario a un área de trabajo existente.
2.  En **Elegir usuario/grupo**, seleccione **Grupo**.
    ![add a group to an existing workspace](./media/log-analytics-manage-access/add-group.png)
3.  Especifique el nombre para mostrar o la dirección de correo electrónico del grupo que quiere agregar.
4.  Seleccione el grupo en los resultados de la lista y después haga clic en **Agregar**.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Vincular un área de trabajo existente a una suscripción de Azure

Se pueden crear áreas de trabajo en el sitio web [microsoft.com/oms](https://microsoft.com/oms) .  Sin embargo, existen ciertos límites en estas áreas de trabajo, el más notable es el límite de 500 MB/día para cargar datos si usa una cuenta gratuita. Para realizar cambios en este área de trabajo deberá *vincular el área de trabajo existente a una suscripción de Azure*.

>[AZURE.IMPORTANT] Para vincular un área de trabajo, su cuenta de Azure debe tener acceso al área de trabajo que quiere vincular.  En otras palabras, la cuenta que utilice para tener acceso al Portal de Azure deberá ser **la misma** cuenta con la que tiene acceso al área de trabajo de OMS. Si no es el caso, consulte [Agregar un usuario a un área de trabajo existente](#add-a-user-to-an-existing-workspace).

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Para vincular un área de trabajo a una suscripción de Azure en el portal OMS

Para vincular un área de trabajo a una suscripción de Azure en el portal OMS, el usuario que ha iniciado sesión debe tener una cuenta de Azure de pago. El área de trabajo que use activamente se vincula a la cuenta de Azure.

1. En OMS, haga clic en el icono **Configuración** .
2. Haga clic en la pestaña **Cuentas** y, luego, en la pestaña **Azure Subscription & Data Plan** (Suscripción y plan de datos de Azure).
3. Haga clic en el plan de datos que desee usar.
4. Haga clic en **Guardar**.  
  ![suscripción y planes de datos](./media/log-analytics-manage-access/subscription-tab.png)

Su nuevo plan de datos se muestra en la cinta de opciones del portal OMS, que se encuentra en la parte superior de la página web.

![cinta de opciones de OMS](./media/log-analytics-manage-access/data-plan-changed.png)

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Para vincular un área de trabajo a una suscripción de Azure en Azure Portal

1.  Inicie sesión en el [Portal de Azure](http://portal.azure.com).
2.  Busque **Log Analytics (OMS)** y selecciónelo.
3.  Aparecerá una lista con las áreas de trabajo existentes. Haga clic en **Agregar**.  
    ![lista de áreas de trabajo](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4.  En **OMS Workspace** (Área de trabajo de OMS), haga clic en **Or link existing** (O vincular existente).  
    ![vincular existente](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5.  Haga clic en **Configurar los valores obligatorios**.  
    ![configure required settings](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6.  Aparecerá una lista con las áreas de trabajo que aún no están vinculadas con su cuenta de Azure. Seleccione un área de trabajo.  
    ![seleccionar áreas de trabajo](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7.  Si es necesario, puede cambiar los valores de los elementos siguientes:
    - La suscripción
    - Grupos de recursos
    - Ubicación
    - Plan de tarifa   
        ![cambiar valores](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8.  Haga clic en **Crear**. Ahora, el área de trabajo está vinculada a su cuenta de Azure.

>[AZURE.NOTE] Si no ve el área de trabajo que quiere vincular, significa que su suscripción de Azure no tiene acceso al área de trabajo de OMS que creó a través del sitio web de OMS.  Debe conceder acceso a esta cuenta desde el área de trabajo de OMS a través del sitio web de OMS. Si ello, consulte [Agregar un usuario a un área de trabajo existente](#add-a-user-to-an-existing-workspace).



## <a name="upgrade-a-workspace-to-a-paid-data-plan"></a>Actualización de un área de trabajo a un plan de datos de pago

En OMS, existen tres tipos de planes de datos de áreas de trabajo: **Gratis**, **Estándar** y **Premium**.  Si dispone de un plan *gratis* , puede haber alcanzado su límite de datos de 500 MB.  Para recopilar datos una vez alcanzado el límite, será preciso que actualice el área de trabajo a un ***plan de pago por uso***. Puede cambiar de tipo de plan en cualquier momento.  Para más información sobre los precios de OMS, consulte [Precios de Operations Management Suite](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx).

>[AZURE.IMPORTANT] Los planes de área de trabajo solo pueden cambiarse si están *vinculados* a una suscripción de Azure.  Si creó el área de trabajo en Azure o si *ya* vinculó el área de trabajo, puede ignorar este mensaje.  Si creó el área de trabajo con el [sitio web de OMS](http://www.microsoft.com/oms), necesitará seguir los pasos descritos en [Vincular un área de trabajo existente a una suscripción de Azure](#link-an-existing-workspace-to-an-azure-subscription).

### <a name="using-entitlements-from-the-oms-add-on-for-system-center"></a>Uso de los derechos del complemento de OMS para System Center

El complemento de OMS para System Center da derecho al plan Premium de Log Analytics de OMS, que se describe en [Precios de Operations Management Suite](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx).

Al adquirir el complemento de OMS para System Center, este se agrega como un derecho en el contrato de System Center. Una suscripción de Azure creada en virtud de este contrato puede hacer uso de este derecho. De este modo, por ejemplo, podrá tener varias áreas de trabajo de OMS que usen el derecho del complemento de OMS.

Para asegurarse de que el uso de un área de trabajo de OMS se realiza con arreglo a los derechos del complemento de OMS, deberá:

1. vincular el área de trabajo de OMS a una suscripción de Azure incluida en un contrato Enterprise que contemple tanto la compra del complemento de OMS como el uso de la suscripción de Azure, y
2. seleccionar el plan Premium para el área de trabajo.

Cuando revise su uso en el portal de Azure o de OMS, no verá los derechos del complemento OMS. Sin embargo, podrá ver estos derechos en Enterprise Portal.  

Si necesita cambiar la suscripción de Azure a la que está vinculada el área de trabajo de OMS, puede usar el cmdlet [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) de Azure PowerShell.

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Uso del compromiso de Azure en contratos Enterprise

Si decide optar por los precios unitarios de los componentes de OMS, pagará cada componente de OMS por separado y su uso se reflejará en la factura de Azure.

Si previamente ha pagado una cierta cantidad a Azure como parte de la inscripción Enterprise al que están vinculadas sus suscripciones de Azure, el uso de Log Analytics se descontará de este importe.

Si necesita modificar la suscripción de Azure a la que está vinculada el área de trabajo de OMS, puede usar el cmdlet [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) de Azure PowerShell.  



### <a name="to-change-a-workspace-to-a-paid-data-plan"></a>Para cambiar un área de trabajo a un plan de datos de pago

1.  Inicie sesión en el [Portal de Azure](http://portal.azure.com).
2.  Busque **Log Analytics (OMS)** y selecciónelo.
3.  Aparecerá una lista con las áreas de trabajo existentes. Seleccione un área de trabajo.  
    ![lista de áreas de trabajo](./media/log-analytics-manage-access/manage-access-change-plan01.png)
4.  En **Configuración**, haga clic en **Plan de tarifa**.  
    ![pricing tier](./media/log-analytics-manage-access/manage-access-change-plan02.png)
5.  En **Plan de tarifa**, seleccione un plan de datos y haga clic en **Seleccionar**.  
    ![seleccionar plan](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6.  Cuando actualice la vista del portal de Azure, verá que **Plan de tarifa** se ha actualizado con el plan seleccionado.  
    ![actualizar plan de tarifa](./media/log-analytics-manage-access/manage-access-change-plan04.png)

Ahora podrá recopilar datos sin los límites del nivel "gratuito".


## <a name="add-an-azure-active-directory-organization-to-an-existing-workspace"></a>Agregar una organización de Azure Active Directory a un área de trabajo existente

Puede asociar el área de trabajo de Log Analytics (OMS) con un dominio de Azure Active Directory. De este modo, podrá agregar usuarios de Active Directory directamente al área de trabajo de OMS sin necesidad de tener una cuenta de Microsoft diferente.

Al crear el área de trabajo desde el Portal de Azure, o vincular el área de trabajo a una suscripción de Azure, su instancia de Azure Active Directory quedará vinculada como su cuenta de organización.

Al crear el área de trabajo desde el portal de OMS, se le pedirá que la vincule a una suscripción de Azure y a una cuenta de organización.

### <a name="to-add-an-azure-active-directory-organization-to-an-existing-workspace"></a>Para agregar una organización de Azure Active Directory a un área de trabajo existente

1. En la página Configuración de OMS, haga clic en **Cuentas** y luego en la pestaña **Información del área de trabajo**.  
2. Revise la información de las cuentas de organización y haga clic en **Agregar organización**.  
    ![agregar organización](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Especifique los datos de identidad del administrador del dominio de Azure Active Directory. A continuación, verá un mensaje de confirmación en el que se indica que el área de trabajo se ha vinculado al dominio de Azure Active Directory.
    ![confirmación de área de trabajo vinculada](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

>[AZURE.NOTE] Una vez que la cuenta esté vinculada a una cuenta de organización, la vinculación no se podrá eliminar ni cambiar.

## <a name="close-your-oms-workspace"></a>Cerrar el área de trabajo de OMS

Cuando se cierra un área de trabajo de OMS, todos los datos relacionados con el área de trabajo se eliminan del servicio de OMS en un plazo máximo de 30 días a partir del cierre del área de trabajo.

Si es administrador y hay varios usuarios asociados al área de trabajo, se interrumpirá la asociación entre los usuarios y el área de trabajo. Si los usuarios están asociados a otras áreas de trabajo, podrán seguir utilizando OMS con esas otras áreas de trabajo. Sin embargo, si no están asociados a otras áreas de trabajo, deberán crear un área de trabajo nueva para poder usar OMS.

### <a name="to-close-an-oms-workspace"></a>Para cerrar un área de trabajo de OMS

1. En OMS, haga clic en el icono **Configuración** .
2. Haga clic en la pestaña **Cuentas** y, después, en la pestaña **Información del área de trabajo**.
3. Haga clic en **Cerrar área de trabajo**.
4. Seleccione uno de los motivos para cerrar el área de trabajo o escriba un motivo distinto en el cuadro de texto.
5. Haga clic en **Cerrar área de trabajo**.

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Conexión de equipos Windows a Log Analytics](log-analytics-windows-agents.md) para agregar agentes y recopilar información.
- [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md) para agregar funcionalidad y recopilar información.
- [Configure proxy and firewall settings in Log Analytics](log-analytics-proxy-firewall.md) si la organización utiliza un servidor proxy o un firewall para que los agentes puedan comunicarse con el servicio Log Analytics.



<!--HONumber=Oct16_HO2-->


