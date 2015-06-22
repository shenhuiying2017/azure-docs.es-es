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
    ms.topic="article" 
    ms.date="04/30/2015" 
    ms.author="banders"/>

# Configurar el área de trabajo y administrar la configuración 

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Para crear una nueva área de trabajo de Visión operativa de Microsoft Azure, elija un nombre para el área de trabajo, asócielo con la cuenta y elija una ubicación geográfica. Un área de trabajo de Visión operativa es esencialmente un contenedor que incluye información de la cuenta e información de configuración sencilla para la cuenta. Usted u otros miembros de su organización pueden usar varias áreas de trabajo de Visión operativa para administrar distintos conjuntos de datos que se recopilan de todas o de algunas partes de su infraestructura de TI.

Después de crear el área de trabajo, puede realizar otras tareas a través del área de trabajo, tales como administrar Visión operativa, ver los datos de uso en el panel, seleccionar las cuentas de almacenamiento y conectar directamente agentes o conectarse a System Center Operations Manager. También puede administrar la configuración de cada área de trabajo.

Para obtener más información sobre cómo crear un área de trabajo con Azure, vea el vídeo siguiente:

> [AZURE.VIDEO creating-a-workspace-for-azure-customers-opinsights]


## ¿Cuántas áreas de trabajo necesito?
Un área de trabajo se ve como un recurso de Azure en el Portal de administración de Azure.

Puede crear un área de trabajo nueva o vincular un área de trabajo existente de vista previa que puede haber abierto anteriormente para su uso con System Center Operations Manager, pero que todavía no ha asociado a una suscripción de Azure (necesaria para la facturación). Un área de trabajo representa el nivel en el que los datos se recopilan, agregan, analizan y presentan en el portal de Visión operativa. Puede tener varias áreas de trabajo para separar los datos de diferentes entornos y sistemas; cada grupo de administración de Operations Manager (y todos sus agentes) o los agentes/máquinas virtuales individuales solo pueden conectarse a una sola área de trabajo.

Cada área de trabajo puede tener varias cuentas de usuario asociadas y cada una de ellas (cuenta de Microsoft o cuenta profesional) puede tener acceso a varias áreas de trabajo de Visión operativa. De forma predeterminada, la cuenta de Microsoft o cuenta profesional que se usa para crear el área de trabajo se convierte en el administrador del área de trabajo. Después, el administrador puede invitar a cuentas de Microsoft adicionales o seleccionar usuarios de su Azure Active Directory.

##<a id="linkworkspace"></a>Vincular un área de trabajo existente a una suscripción de Azure

Es posible crear un área de trabajo desde [opinsights.azure.com](http://opinsights.azure.com). Sin embargo, existen ciertos límites en estas áreas de trabajo, el más notable es el límite de 500 MB/día para cargar datos si usa una cuenta gratuita. Para realizar cambios en esta área de trabajo deberá **vincular el área de trabajo existente a una suscripción de Azure**.

>[AZURE.IMPORTANT]Para vincular un área de trabajo, su cuenta de Azure debe tener acceso al área de trabajo que quiere vincular. En otras palabras, la cuenta que usa para acceder al portal de Azure debe ser **la misma** que la cuenta que usa para tener acceso al área de trabajo de Visión operativa. Si no es el caso, consulte [Agregar un usuario a un área de trabajo existente](#addusertoexistingworkspace).

1. Inicie sesión en el Portal de administración de Azure.
2. En la parte inferior izquierda del portal, haga clic en **+ Nuevo**.
3. Haga clic en **Servicios de aplicaciones**, desplácese a **Visión operativa** y selecciónela.
4. Haga clic en **Creación rápida**.
5. En la lista **Cuenta**, debería ver una lista de las áreas de trabajo existentes que *todavía no* se vincularon a la suscripción de Azure. Seleccione una cuenta.

	>[AZURE.NOTE][Agregar un usuario a un área de trabajo existente](#addusertoexistingworkspace)![vincular cuenta](./media/operational-insights-setup-workspace/link-account.png)<p>
6. Rellene los campos restantes y, luego, seleccione **Crear área de trabajo**.

## Actualizar el área de trabajo a un plan de pago

Existen tres planes diferentes para el área de trabajo de Visión operativa: **gratis**, **estándar** y **premium**. Si dispone de una plan *gratis*, puede haber alcanzado su límite de datos de 500 MB. Necesitará actualizar el área de trabajo a un '**plan pago por uso**' para recopilar datos una vez alcanzado el límite. Puede convertir el tipo de plan en cualquier momento. Para obtener más información acerca de Visión operativa, consulte [Detalles de precios](http://azure.microsoft.com/pricing/operational-insights/).

>[AZURE.IMPORTANT]Los planes de área de trabajo solo pueden cambiarse si están *vinculados* a una suscripción de Azure. Si creó el área de trabajo en Azure o si *ya* vinculó el área de trabajo, puede ignorar este mensaje. Si creó el área de trabajo desde [opinsights.azure.com](http://opinsights.azure.com), deberá seguir los pasos descritos en [Vincular un área de trabajo existente a una suscripción de Azure](#linkworkspace).

### Cambiar el tipo de plan

En el Portal de administración de Azure, desplácese hasta el área de trabajo de Visión operativa al que quiere actualizar:

![scale](./media/operational-insights-setup-workspace/find-workspace.png)

Seleccione este área de trabajo y seleccione la pestaña **ESCALA** en la parte superior de la pantalla

![seleccionar escala](./media/operational-insights-setup-workspace/select-scale.png)

Por último, elija el plan al que quiere actualizar y haga clic en **GUARDAR**. Verá los cambios reflejados en el portal y podrá recopilar datos por encima del límite de datos "gratuito".

![seleccionar plan](./media/operational-insights-setup-workspace/plan-select.png)

## Cambiar el nombre del área de trabajo

Si usted es el administrador de un área de trabajo de Visión operativa de Microsoft Azure, puede cambiar el nombre del área de trabajo.

###Para cambiar el nombre del área de trabajo

1. Haga clic en el nombre del área de trabajo. <p> ![nombre de área de trabajo](./media/operational-insights-setup-workspace/settings01.png) <p>
2. Haga clic en el icono de configurar. <p> ![icono de configurar](./media/operational-insights-setup-workspace/settings02.png) <p>
3. En la página **Configuración** de Visión operativa, en la sección **Administrar cuentas de usuario**, haga clic en **Administrar usuarios**. <p> ![administrar usuarios](./media/operational-insights-setup-workspace/settings03.png) <p>
4. En el portal de Visión operativa, en la página **Configuración**, escriba el nombre nuevo en el campo **Nombre del área de trabajo**.

5. Haga clic en **Guardar**.

## Cambiar la información de usuario

Puede cambiar el nombre asociado a un usuario de Visión operativa, pero no se puede cambiar el nombre de la cuenta de Microsoft que está asociada a ese usuario.

Para los usuarios con una cuenta de Microsoft, también puede cambiar la configuración de notificación. Si usa una cuenta profesional a través de Azure Active Directory, actualmente no puede usar la característica **Notificaciones** en Visión operativa.

### Para cambiar la información de usuario
1. En la página **Configuración** de Visión operativa, en la sección **Información del usuario**, escriba el nombre en los campos **Nombre** y **Apellidos**.

2. Para los usuarios de cuentas de Microsoft, cambie la configuración de notificación. De forma predeterminada, todos los usuarios de cuentas reciben una notificación cuando se genera una alerta. Si quiere que los usuarios dejen de recibir estas notificaciones, desactive la opción **Recibir notificaciones de correo electrónico de nuevas alertas de Visión operativa**.

3. Haga clic en **Guardar**.

## Cambiar ajustes de configuración

De forma predeterminada, todos los usuarios que están asociados a un área de trabajo de Visión operativa recibirán un correo electrónico que resume las alertas de evaluación de configuración que se han generado en los últimos siete días. En la página **Configuración**, los usuarios pueden controlar si se reciben estas notificaciones de correo electrónico.

>[AZURE.NOTE]Las notificaciones solo están disponibles para los usuarios con una cuenta de Microsoft. Si usa una cuenta profesional a través de Azure Active Directory, actualmente no puede usar la característica **Notificaciones** en Visión operativa.

¿No ve mensajes de correo electrónico que cree que debería ver? Compruebe los filtros de correo no deseado. Asegúrese de que los correos electrónicos de *operationalinsights@opinsights.azure.com* no se están ignorando.

1. En la página **Configuración** de Visión operativa, en la sección **Información del usuario**, desactive la opción **Recibir notificaciones de correo electrónico de nuevas alertas de Visión operativa**.

2. Haga clic en **Guardar**.

##<a id="addusertoexistingworkspace"></a>Agregar un usuario a un área de trabajo existente


Use los pasos siguientes para agregar un usuario o grupo a un área de trabajo de Visión operativa. El usuario o grupo será capaz de ver y actuar en todas las alertas asociadas a esta área de trabajo.

>[AZURE.NOTE]Si quiere agregar un usuario o grupo de su cuenta profesional de Azure Active Directory, primero debe asegurarse de que se ha asociado su cuenta de Visión operativa con el dominio de Active Directory. Consulte [Agregar una organización de Azure Active Directory a un área de trabajo existente](#).

### Para agregar un usuario a un área de trabajo existente
1. Haga clic en el nombre del área de trabajo.
2. Haga clic en el icono de configurar.
3. En la página **Configuración** de Visión operativa, en la sección **Administrar cuentas de usuario**, haga clic en **Administrar usuarios**. <p> ![administrar usuarios](./media/operational-insights-setup-workspace/settings04.png) <p>
4. En la ventana **Administrar usuarios**, haga clic en **Agregar**. <p> ![página de configuración](./media/operational-insights-setup-workspace/manage-users01.png) <p>
5. Si su cuenta de Visión operativa está asociada con Azure Active Directory, especifique **Cuenta profesional**.

    >[AZURE.NOTE]<p>![agregar tipo de cuenta de usuario](./media/operational-insights-setup-workspace/manage-users02.png)<p>
6. Escriba la nueva información de usuario para la cuenta de Microsoft o la cuenta profesional. Si va a agregar una cuenta profesional, puede escribir parte del nombre del usuario o del grupo o el alias de correo electrónico y, luego, haga clic en **Comprobar nombres** para buscar el usuario o grupo específico.
 
    >[AZURE.NOTE]Para obtener los mejores resultados de rendimiento, limite el número de grupos de Active Directory asociados a una sola cuenta de Visión operativa a dos: uno para administradores y otro para usuarios. Usar más grupos podría afectar al rendimiento de Visión operativa.

7. Seleccione el rol para este nuevo usuario: **administrador** o **usuario**. <p> ![agregar rol de usuario del área de trabajo](./media/operational-insights-setup-workspace/manage-users03.png) <p>
8. Haga clic en **Aceptar**.
    
    Si va a agregar una cuenta de Microsoft, se envía una invitación para unir la cuenta al correo electrónico que proporcionó. Una vez que el usuario sigue las instrucciones de la invitación para unirse a Visión operativa, el usuario podrá ver las alertas y la información de cuenta para esta cuenta Visión operativa y podrá ver la información de usuario en la ventana **Administrar usuarios**.
 
    Si va a agregar una cuenta profesional, el usuario podrá acceder a Visión operativa inmediatamente. <p> ![invitación](./media/operational-insights-setup-workspace/manage-users04.png) <p>
## Agregar una organización de Azure Active Directory a un área de trabajo existente

Puede asociar el área de trabajo de Visión operativa a un dominio de Azure Active Directory. Esto le permite agregar usuarios desde Active Directory directamente al área de trabajo de Visión operativa, sin necesidad de una cuenta independiente de Microsoft.

### Para agregar una organización de Azure Active Directory a un área de trabajo existente

1. En la página Configuración de Visión operativa, haga clic en **Agregar organización**. <p> ![invitación](./media/operational-insights-setup-workspace/add-org.png) <p>
2. Revise la información acerca de las cuentas profesionales y, luego, haga clic en **Siguiente**.

3. Escriba la información de identidad del administrador del dominio de Azure Active Directory y, luego, haga clic en **Iniciar sesión**.

4. Haga clic en **Conceder acceso** para permitir que Visión operativa pueda usar la información de identidad en el dominio de Active Directory. <p> ![vinculados](./media/operational-insights-setup-workspace/ad-existing01.png)

## Editar la cuenta de un usuario existente

Puede cambiar el rol de la cuenta para un usuario asociado a su cuenta de Visión operativa. Tiene las siguientes opciones de rol:

 - *Administrador*: puede administrar usuarios, ver y actuar en todas las alertas y agregar y quitar servidores

 - *Usuario*: puede ver y actuar en todas las alertas y agregar y quitar servidores

### Para editar una cuenta
1. En la página **Configuración** de Visión operativa, en la sección **Administrar cuentas de usuario**, haga clic en **Administrar usuarios**.

2. En la ventana **Administrar usuarios**, seleccione el nombre del usuario que quiere cambiar y, luego, haga clic en **Editar usuario**.

3. Seleccione el rol para este usuario: **administrador** o **usuario**.

4. Haga clic en **Aceptar**.

## Eliminar a un usuario de un área de trabajo de Visión operativa

Use los pasos siguientes para eliminar a un usuario de un área de trabajo de Visión operativa. Tenga en cuenta que esto no cierra el área de trabajo del usuario. En su lugar, elimina la asociación entre ese usuario y el área de trabajo. Si un usuario está asociado a varias áreas de trabajo, dicho usuario podrá iniciar sesión en Visión operativa.

### Para eliminar a un usuario de un área de trabajo

1. En la página **Configuración** de Visión operativa, en la sección **Administrar cuenta de usuario**, haga clic en **Administrar usuarios**.

2. En la ventana **Administrar usuarios**, haga clic en el nombre del usuario que quiere eliminar y, luego, haga clic en **Eliminar usuario**.

3. Haga clic en **Aceptar** para confirmar que quiere eliminar al usuario.

## Cerrar el área de trabajo de Visión operativa

Al cerrar un área de trabajo de Visión operativa, todos los datos relacionados con el área de trabajo se eliminan del servicio de Visión operativa en un plazo de 30 días a partir del cierre del área de trabajo.

Si es administrador, y hay varios usuarios asociados con el área de trabajo, se interrumpirá la asociación entre los usuarios y el área de trabajo. Si los usuarios están asociados a otras áreas de trabajo, podrán seguir usando Visión operativa con las otras áreas de trabajo. Sin embargo, si no están asociados a otras áreas de trabajo, necesitarán crear una nueva área de trabajo para usar Visión operativa.

### Para cerrar un área de trabajo de Visión operativa

1. En la página **Configuración** de Visión operativa, en la sección **Cerrar área de trabajo**, haga clic en **Cerrar área de trabajo**.

2. Seleccione uno de los motivos para cerrar el área de trabajo o escriba un motivo distinto en el cuadro de texto.

3. Haga clic en **Cerrar área de trabajo**.


<!--HONumber=54--> 