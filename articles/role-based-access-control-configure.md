<properties title="Role Based Access Control in Azure Preview Portal" pageTitle="Role Based Access Control in Azure Preview Portal" description="Describes how role based access control works and how to set it up" metaKeywords="" services="multiple" solutions="" documentationCenter="" authors="justinha" videoId="" scriptId="" />

<tags ms.service="multiple" ms.devlang="dotnet" ms.topic="article" ms.tgt_pltfrm="Ibiza" ms.workload="infrastructure-services" ms.date="09/12/2014" ms.author="justinha;Justinha@microsoft.com" />

<!--This is a basic template that shows you how to use mark down to create a topic that includes a TOC, sections with subheadings, links to other azure.microsoft.com topics, links to other sites, bold text, italic text, numbered and bulleted lists, code snippets, and images. For fancier markdown, find a published topic and copy the markdown or HTML you want. For more details about using markdown, see http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->

<!--Properties section (above): this is required in all topics. Please fill it out!-->

# Control de acceso basado en roles en el Portal de vista previa de Azure

Con el fin de que las organizaciones satisfagan sus requisitos de administración de acceso de forma simple y precisa, el Portal de vista previa de Azure es ahora compatible con el control de acceso basado en roles (RBAC, por sus siglas en inglés). En la [entrada de blog][entrada de blog] se proporciona una introducción rápida a la característica, mientras que en este tema se describen detalladamente los conceptos y se incluyen casos de uso adicionales.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

## Tabla de contenido

-   [RBAC en Azure][RBAC en Azure]
-   [Coexistencia de RBAC con coadministradores de suscripciones][Coexistencia de RBAC con coadministradores de suscripciones]
-   [Autorización para administración frente a operaciones de datos][Autorización para administración frente a operaciones de datos]
-   [Adición y eliminación de acceso][Adición y eliminación de acceso]
-   [Problemas conocidos del control de acceso basado en roles][Problemas conocidos del control de acceso basado en roles]
-   [Comentarios][Comentarios]
-   [Pasos siguientes][Pasos siguientes]

## <span id="whatisrbac"></span></a>RBAC en Azure

Cada una de las suscripciones de Azure está asociada a un Azure Active Directory. Los usuarios y los servicios que acceden a los recursos de la suscripción mediante el Portal de administración de Azure o la API de Administrador de recursos de Azure deben autenticarse primero con ese Azure Active Directory.

![][]

El control de acceso basado en roles de Azure permite conceder el acceso apropiado a usuarios, grupos y servicios de Azure AD mediante la asignación de roles en un ámbito de suscripción, grupo de recursos o recurso individual. El rol asignado define el nivel de acceso que los usuarios, grupos o servicios tienen en el recurso de Azure.

### Rol

Un rol es una colección de acciones que se pueden realizar en recursos de Azure. Para que un usuario o un servicio puedan llevar a cabo una acción en un recurso de Azure, deben tener asignado un rol que contenga dicha acción.

#### Roles integrados

En la primera vista previa, el control de acceso basado en roles de Azure dispone de tres roles integrados que se pueden asignar a usuarios, grupos y servicios.

-   **Propietario**: tiene control total sobre los recursos de Azure. El propietario puede realizar todas las operaciones en un recurso, incluida la administración del acceso.
-   **Colaborador**: puede realizar todas las operaciones de administración salvo las de administración del acceso. Por tanto, un colaborador no puede conceder acceso a otras personas.
-   **Lector**: solo puede ver los recursos. No puede ver los secretos que están asociados a un recurso.

La definición de los roles integrados no se puede modificar. En una próxima versión del RBAC de Azure se podrán definir roles personalizados formando un conjunto de acciones elegidas a partir de una lista de acciones disponibles que se pueden realizar en recursos de Azure.

#### Acciones y no acciones

La propiedad **acciones** de la definición de rol especifica las acciones permitidas en recursos de Azure. Las cadenas de acciones pueden utilizar caracteres comodín. La propiedad **no acciones** de la definición de rol especifica las acciones que se deben excluir de las acciones permitidas.

**Rol integrado**  | Acciones  | No acciones	
------------- | -------------  | ------------
Propietario (permitir todas las acciones)  | *  | 
Colaborador (permitir todas las acciones excepto escritura o eliminaci&oacute;n de asignaciones de roles)  | *  | Microsoft.Authorization/ * /Write, Microsoft.Authorization/ * /Delete
Lector (permitir todas las acciones)  | */Read  | 

### Asignación de roles

El acceso se concede a usuarios y servicios de Azure AD mediante la asignación de un rol apropiado en un recurso de Azure.

#### Entidades de seguridad de Azure AD

Los roles se pueden asignar a los siguientes tipos de entidades de seguridad de Azure AD:

-   **Usuarios**: los roles se pueden asignar a usuarios de la organización que están en el Azure AD al que está asociada la suscripción de Azure. Los roles también se pueden asignar a usuarios externos que dispongan de una cuenta Microsoft (por ejemplo, <joe@outlook.com>). Para ello, se debe usar la acción Invitar a fin de asignar al usuario un rol en el Portal de vista previa de Azure. Al asignar un rol a un usuario externo de una cuenta Microsoft, se crea una cuenta de invitado en Azure AD. Si esta cuenta de invitado se deshabilita en el directorio, el usuario externo no podrá acceder a los recursos de Azure para los que se le haya concedido acceso.
-   **Grupos**: los roles se pueden asignar a grupos de seguridad de Azure AD. Si un usuario pasa a pertenecer a un grupo que tiene acceso, ese usuario dispondrá automáticamente de acceso a un recurso. Del mismo modo, si el usuario se quita del grupo, pierde automáticamente el acceso al recurso. En lugar de asignar roles directamente a los usuarios, lo más recomendable es administrar el acceso por grupos mediante la asignación de roles a grupos y la adición de usuarios a esos grupos. El RBAC de Azure no permite asignar roles a listas de distribución.
    La capacidad de asignar roles a grupos permite a una organización extender a la nube su modelo de control de acceso local existente; de esta forma, los grupos de seguridad que ya tienen establecido un control de acceso local se pueden reutilizar para controlar el acceso a los recursos en el Portal de vista previa de Azure. Para obtener más información sobre las distintas opciones de sincronización de usuarios y grupos de un directorio local, vea [Descripción general de la integración de directorios][Descripción general de la integración de directorios]. Azure AD Premium también ofrece una [característica de administración de grupos delegada][característica de administración de grupos delegada] mediante la cual se puede delegar la capacidad de crear y administrar grupos en usuarios que no sean administradores de Azure AD.
-   **Entidades de seguridad de servicio**: las identidades de servicio se representan como entidades de seguridad en el directorio. Se autentican con Azure AD y se comunican entre ellas de forma segura. Para que los servicios tengan acceso a los recursos de Azure, se pueden asignar roles vía el módulo Azure para Windows PowerShell a la entidad de seguridad de servicio de Azure AD que representa a ese servicio.

#### Ámbito de recursos

El acceso no tiene por qué concederse a toda la suscripción. Los roles también se pueden asignar para grupos de recursos y recursos individuales. En el RBAC de Azure, los recursos heredan las asignaciones de roles de sus recursos primarios. Por tanto, si a un usuario, grupo o servicio solo se le concede acceso a uno de los grupos de recursos de una suscripción, únicamente podrá acceder a ese grupo de recursos —y a los recursos que este contenga—, pero no a otros grupos de recursos de la suscripción. Otro ejemplo: un grupo de seguridad se puede agregar al rol de lector para un grupo de recursos y al rol de colaborador para una base de datos que esté dentro de ese grupo de recursos.

![][1]

## <span id="coexist"></span></a>Coexistencia de RBAC con coadministradores de suscripciones

El administrador y los coadministradores de suscripciones seguirán teniendo acceso total a los portales de Azure y a las API de administración. En el modelo RBAC, se les asigna el rol de propietario en el ámbito de suscripción.

Sin embargo, el nuevo modelo RBAC solo es compatible con el Portal de vista previa de Azure y las API de Administrador de recursos de Azure. Los usuarios y los servicios a los que se les asignan roles RBAC no pueden acceder al Portal de administración de Azure ni a las API de administración de servicios. Los usuarios agregados al rol de propietario de una suscripción en el Portal de vista previa de Azure no se convierten en coadministradores de la suscripción en el Portal de Azure completo.

Si desea conceder a un usuario acceso a un recurso de Azure que todavía no está disponible para su administración a través del Portal de vista previa de Azure, agréguelo a los coadministradores de suscripción mediante el Portal de administración de Azure completo. El Bus de servicio y los Servicios en la nube son ejemplos de recursos que actualmente no se pueden administrar mediante RBAC.

## <span id="authmgmt"></span></a>Autorización para administración frente a operaciones de datos

El control de acceso basado en roles solo es compatible con operaciones de administración de los recursos de Azure del Portal de vista previa de Azure y las API de Administrador de recursos de Azure. No todas las operaciones de nivel de datos para recursos de Azure se pueden autorizar mediante RBAC. Por ejemplo, la creación, la lectura, la actualización o la eliminación de cuentas de almacenamiento se puede controlar mediante RBAC; pero la creación, la lectura, la actualización o la eliminación de blobs o tablas de la cuenta de almacenamiento aún no se puede controlar mediante RBAC. Del mismo modo, la creación, la lectura, la actualización o la eliminación de una base de datos de SQL se puede controlar mediante RBAC; pero la creación, la lectura, la actualización o la eliminación tablas de base de datos de SQL aún no se puede controlar mediante RBAC.

## <span id="addremoveaccess"></span></a>Adición y eliminación de acceso

Veamos un ejemplo de cómo un propietario de recursos de una organización puede administrar el acceso. En este escenario, hay varias personas trabajando en distintos proyectos de prueba y producción que se compilan mediante recursos de Azure, y se quieren seguir los procedimientos recomendados para la concesión de acceso. Los usuarios deben tener acceso a todos los recursos que necesitan, pero solo a estos. Se desea reaprovechar todas las inversiones realizadas en procesos y herramientas a fin de usar los grupos de seguridad que se controlan en un Active Directory local. Las secciones siguientes describen cómo configurar el acceso a estos recursos:

-   [Agregar acceso][Agregar acceso]
-   [Quitar acceso][Quitar acceso]
-   [Agregar o quitar acceso para usuario externo][Agregar o quitar acceso para usuario externo]

### 

<p>
<span id="add"></span></a>Agregar acceso

</h2>
</p>
Aquí se resumen de los requisitos de acceso y su configuración en Azure.

| Usuario/Grupo                 | Requisito de acceso                                                 | rol y ámbito de acceso                                                                                                |
|-------------------------------|---------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| Todo el equipo de Jill Santos | Leer todos los recursos de Azure                                    | Agregar el grupo de AD que representa al equipo de Jill Santos al rol de lector para la suscripción de Azure          |
| Todo el equipo de Jill Santos | Crear y administrar todos los recursos del grupo de recursos Prueba | Agregar el grupo de AD que representa al equipo de Jill Santos al rol de colaborador para el grupo de recursos Prueba |
| Brock                         | Crear y administrar todos los recursos del grupo de recursos Prod   | Agregar Brock al rol de colaborador para el grupo de recursos Prod                                                    |

En primer lugar, vamos a agregar el acceso Leer para todos los recursos de la suscripción. Haga clic en **Examinar \> Todo \> Suscripciones**.

![][2]

Haga clic en *nombre de su suscripción* \*\* \> Lector \> Agregar\*\*. En la lista de usuarios y grupos, seleccione o escriba el nombre del grupo de Active Directory.

![][3]

Después agregue el mismo equipo al rol de colaborador del grupo de recursos Prueba. Haga clic en el grupo de recursos para abrir su hoja de propiedades. En **Roles**, haga clic en **Colaborador \> Agregar** y escriba el nombre del equipo.

![][4]

Para agregar Brock al rol de colaborador del grupo de recursos Prod, haga clic en el grupo de recursos, después en **Colaborador \> Agregar** y escriba el nombre de Brock.

![][5]

Las asignaciones de roles también se pueden administrar mediante el módulo de Microsoft Azure para Windows PowerShell. En el ejemplo siguiente se muestra cómo agregar la cuenta de Brock mediante el cmdlet New-AzureRoleAssignment en lugar del portal:

    PS C:PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDBgt; New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Para obtener más información sobre el uso de Windows PowerShell para agregar y quitar acceso, vea el artículo sobre cómo [administrar el control de acceso basado en roles con Windows PowerShell][administrar el control de acceso basado en roles con Windows PowerShell].

### 

<p>
<h3><a id="remove"></a>Quitar acceso</h2>

</h2>
</p>
Las asignaciones también se pueden quitar fácilmente. Supongamos que se desea quitar un usuario llamado Brad Adams del rol de lector en un grupo de recursos denominado TestDB. Abra la hoja del grupo de recursos y haga clic en **Lector \> Brad Adams \> Quitar**.

![][6]

En el ejemplo siguiente se muestra cómo quitar Brad Adams mediante el cmdlet Remove-AzureRoleAssignment:

    PS C:PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDBgt; Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

### 

<p>
<h3><a id="addremoveext"></a>Agregar o quitar acceso para usuario externo</h2>

</h2>
</p>
La pestaña **Configurar** de un directorio incluye opciones para controlar el acceso de usuarios externos. Estas opciones solo las puede cambiar un administrador global de directorios en la UI (no hay ningún método de Windows PowerShell o API) del Portal de Azure completo.
Para abrir la pestaña **Configurar** en el Portal de Azure completo, haga clic en **Active Directory** y después en el nombre del directorio.

![][7]

A continuación, puede editar las opciones para controlar el acceso de usuarios externos.

![][8]

De forma predeterminada, los invitados no pueden enumerar el contenido del directorio y, por tanto, no ven ningún usuario o grupo en la **Lista de miembros**. Pueden buscar un usuario si escriben la dirección de correo electrónico completa del usuario y luego conceden acceso. El conjunto de restricciones predeterminadas para los usuarios son:

-   No pueden enumerar usuarios ni grupos en el directorio.
-   Pueden ver información limitada de un usuario si conocen la dirección de correo electrónico del usuario.
-   Pueden ver información limitada de un grupo si conocen el nombre del grupo.

La capacidad de los invitados para ver información limitada de un usuario o grupo les permite invitar a otras personas y ver algunos detalles de la gente con la que colaboran.

Pasemos al proceso de agregar acceso para un usuario externo. Agregaremos un usuario externo al mismo rol de lector para el grupo de recursos TestDB con el fin de que el usuario pueda ayudar a depurar un error. Abra la hoja del grupo de recursos, haga clic en **Lector \> Agregar \> Invitar** y escriba la dirección de correo electrónico del usuario que desee agregar.

![][9]

Cuando se agrega un usuario externo, se crea un invitado en el directorio. En adelante, y del mismo modo que cualquier otro usuario del directorio, ese invitado se puede agregar a un grupo o quitar de un grupo, o se puede agregar o quitar de un rol de forma individual.

También se puede quitar un invitado de un rol siguiendo el mismo procedimiento que con cualquier usuario. Aunque se quite un invitado de un rol en un recurso, el invitado no se quita del directorio.

## <span id="knownissues"></span></a>Problemas conocidos del control de acceso basado en roles

Si se encuentra con un problema al usar la característica de control de acceso basado en roles durante la vista previa, vea el artículo sobre [solución de problemas del control de acceso basado en roles][solución de problemas del control de acceso basado en roles] para obtener información acerca de los problemas conocidos.

## <span id="feedback"></span></a>Comentarios

Pruebe el RBAC de Azure y envíenos sus [comentarios][comentarios].

## <span id="next"></span></a>Pasos siguientes

Los recursos siguientes le servirán de ayuda para el control de acceso basado en roles:

-   [Administrar el control de acceso basado en roles con Windows PowerShell][administrar el control de acceso basado en roles con Windows PowerShell]
-   [Administrar el control de acceso basado en roles con XPLAT CLI][Administrar el control de acceso basado en roles con XPLAT CLI]
-   [Solución de problemas del control de acceso basado en roles][solución de problemas del control de acceso basado en roles]
-   [Azure Active Directory][Azure Active Directory]
-   [Azure Active Directory Premium y Basic][Azure Active Directory Premium y Basic]
-   [Cómo se asocian las suscripciones a Azure con Azure AD][Cómo se asocian las suscripciones a Azure con Azure AD]
-   Para una introducción a la administración de grupos de autoservicio para grupos de seguridad, vea el [blog del equipo de Active Directory][blog del equipo de Active Directory]

<!--Image references-->

  [entrada de blog]: http://go.microsoft.com/fwlink/?LinkId=511576
  [RBAC en Azure]: #whatisrbac
  [Coexistencia de RBAC con coadministradores de suscripciones]: #coexist
  [Autorización para administración frente a operaciones de datos]: #authmgmt
  [Adición y eliminación de acceso]: #addremoveaccess
  [Problemas conocidos del control de acceso basado en roles]: #knownissues
  [Comentarios]: #feedback
  [Pasos siguientes]: #next
  []: ./media/role-based-access-control-configure/RBACSubAuthDir.png
  [Descripción general de la integración de directorios]: http://technet.microsoft.com/library/jj573653.aspx
  [característica de administración de grupos delegada]: http://msdn.microsoft.com/library/azure/dn641267.aspx
  [1]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
  [Agregar acceso]: #add
  [Quitar acceso]: #remove
  [Agregar o quitar acceso para usuario externo]: #addremoveext
  [2]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
  [3]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
  [4]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
  [5]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
  [administrar el control de acceso basado en roles con Windows PowerShell]: http://azure.microsoft.com/es-es/documentation/articles/role-based-access-control-powershell/
  [6]: ./media/role-based-access-control-configure/RBACRemoveRole.png
  [7]: ./media/role-based-access-control-configure/RBACDirConfigTab.png
  [8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
  [9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
  [solución de problemas del control de acceso basado en roles]: http://azure.microsoft.com/es-es/documentation/articles/role-based-access-control-troubleshooting/
  [comentarios]: http://aka.ms/azurerbacfeedback
  [Administrar el control de acceso basado en roles con XPLAT CLI]: http://azure.microsoft.com/es-es/documentation/articles/role-based-access-control-xplat-cli/
  [Azure Active Directory]: http://msdn.microsoft.com/library/azure/jj673460.aspx
  [Azure Active Directory Premium y Basic]: http://msdn.microsoft.com/en-us/library/azure/dn532272.aspx
  [Cómo se asocian las suscripciones a Azure con Azure AD]: http://msdn.microsoft.com/en-us/library/azure/dn629581.aspx
  [blog del equipo de Active Directory]: http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx
