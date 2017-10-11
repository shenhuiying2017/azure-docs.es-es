---
title: "Configuración de clústeres de HDInsight unidos a un dominio (versión preliminar): Azure | Microsoft Docs"
description: "Aprenda a configurar y definir clústeres de HDInsight unidos a un dominio."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
ms.openlocfilehash: 9964c3dff24ef8a3a6047fe18c0f36c12c1de33d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="configure-domain-joined-hdinsight-clusters-preview"></a>Configuración de clústeres de HDInsight unidos a un dominio (versión preliminar)

Aprenda a configurar un clúster de Azure HDInsight con Azure Active Directory (Azure AD) y [Apache Ranger](http://hortonworks.com/apache/ranger/) para aprovechar la autenticación sólida y las directivas de control de acceso basado en rol (RBAC) enriquecidas.  HDInsight unido a un dominio solo se puede configurar en clústeres basados en Linux. Para más información, consulte [Introduce Domain-joined HDInsight clusters](hdinsight-domain-joined-introduction.md) (Introducción a los clústeres de HDInsight unidos a dominio (versión preliminar))

> [!IMPORTANT]
> Oozie no está habilitado en HDInsight unido a un dominio.

Este artículo es el primer tutorial de una serie:

* Crear un clúster de HDInsight conectado a Azure AD (a través de la funcionalidad de servicios de Azure Directory Domain) con Ranger Apache habilitado.
* Crear y aplicar directivas de Hive a través de Apache Ranger y permitir a los usuarios (por ejemplo, los científicos de datos) conectarse a Hive con herramientas basadas en ODBC, por ejemplo, Excel, Tableau, etc. Microsoft está trabajando para incluir otras cargas de trabajo, como HBase, Spark y Storm, a HDInsight unido a un dominio en breve.

Un ejemplo de la topología final tiene el aspecto siguiente:

![Topología de HDInsight unido a un dominio](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-topology.png)

Dado que Azure AD actualmente solo admite redes virtuales clásicas (VNet) y los clústeres de HDInsight basados en Linux solo admiten Azure Resource Manager basado en redes virtuales, la integración de Azure AD de HDInsight requiere dos redes virtuales y un emparejamiento entre ellas. Para obtener información comparativa entre dos modelos de implementación, consulte [La implementación de Azure Resource Manager frente a la implementación clásica: los modelos de implementación y el estado de los recursos](../azure-resource-manager/resource-manager-deployment-model.md). Las dos redes virtuales deben estar en la misma región que Azure AD DS.

Los nombres de servicio de Azure deben ser únicos globalmente. Los siguientes nombres se utilizan en este tutorial. Contoso es un nombre ficticio. Debe reemplazar *contoso* por un nombre diferente cuando recorra el tutorial. 

**Nombres:**

| Propiedad | Valor |
| --- | --- |
| Red virtual de Azure AD |contosoaadvnet |
| Grupo de recursos de red virtual de Azure AD |contosoaadrg |
| Directorio de Azure AD |contosoaaddirectory |
| Nombre de dominio de Azure AD |contoso (contoso.onmicrosoft.com) |
| Red virtual de HDInsight |contosohdivnet |
| Grupo de recursos de red virtual de HDInsight |contosohdirg |
| Clúster de HDInsight |contosohdicluster |

Este tutorial proporciona los pasos para configurar un clúster de HDInsight unido a un dominio. Cada sección tiene vínculos a otros artículos con información adicional.

## <a name="prerequisite"></a>Requisito previo:
* Familiarícese con [Servicios de dominio de Azure AD](https://azure.microsoft.com/services/active-directory-ds/) y su estructura de [precios](https://azure.microsoft.com/pricing/details/active-directory-ds/).
* Asegúrese de que la suscripción está en la lista blanca para esta versión preliminar pública. Puede hacerlo enviando un correo electrónico a hdipreview@microsoft.com con su identificador de suscripción.
* Un certificado SSL firmado por una autoridad de firma para el dominio. Se necesita el certificado por la configuración de LDAP seguro. Los certificados autofirmados no se pueden usar.

## <a name="procedures"></a>Procedimientos
1. Cree una red virtual clásica de Azure para su Azure AD.  
2. Cree y configure Azure AD y Azure AD DS.
3. Cree una red virtual de HDInsight en el modo de administración de recursos de Azure.
4. Empareje las dos redes virtuales.
5. Cree un clúster de HDInsight.

> [!NOTE]
> En este tutorial se supone que no tiene una cuenta de Azure AD. Si tiene una, puede omitir la parte del paso 2.
> 
> 

Hay un script de PowerShell que automatiza los pasos 3 al 7.  Para más información, consulte [Configuración de clústeres de HDInsight unidos a un dominio con Azure PowerShell](hdinsight-domain-joined-configure-use-powershell.md).

## <a name="create-an-azure-virtual-network-classic"></a>Creación de una instancia de Azure Virtual Network (clásico)
En esta sección se crea una instancia de Virtual Network (clásico) con Azure Portal. En la siguiente sección, se habilita Azure AD DS para Azure AD en la red virtual. Para más información sobre el siguiente procedimiento y el uso de otros métodos de creación de la red virtual, vea [Creación de una instancia de Virtual Network (clásico) con Azure Portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

**Para crear una red virtual clásica**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). 
2. Haga clic en **Nuevo** > **Redes** > **Red virtual**.
3. En **Seleccionar un modelo de implementación**, seleccione **Clásico** y luego haga clic en **Crear**.
4. Escriba o seleccione los siguientes valores:
   
   * **Nombre**: contosoaadvnet
   * **Espacio de direcciones**: 10.1.0.0/16
   * **Nombre de subred**: Subnet1
   * **Intervalo de direcciones de subred**: 10.1.0.0/24
   * **Suscripción**: (Seleccione una suscripción usada para crear esta red virtual.)
   * **ResourceGroup**: contosoaadrg
   * **Ubicación**: (Seleccione una región para el clúster de HDInsight.)
     
     > [!IMPORTANT]
     > Debe elegir una ubicación compatible con Azure AD DS. Para más información, consulte [Productos disponibles por región](https://azure.microsoft.com/en-us/regions/services/). 
     > 
     > Tanto la red virtual clásica como la red virtual de grupo de recursos deben estar en la misma región que Azure AD DS.
     > 
     > 
5. Haga clic en **Crear** para crear la red virtual.

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>Creación y configuración de Azure AD DS para Azure AD
En esta sección:

1. Creará Azure AD.
2. Creará usuarios de Azure AD. Estos usuarios son usuarios de dominio. El primer usuario se usa para configurar el clúster de HDInsight con Azure AD.  Los otros dos usuarios son opcionales para este tutorial. Se usarán en [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md) (Configuración de directivas de Hive para clústeres de HDInsight unidos a un dominio) al configurar las directivas de Apache Ranger.
3. Creará el grupo de administradores de controlador de dominio de AAD y agregará el usuario de Azure AD al grupo. Este usuario se usa para crear la unidad organizativa.
4. Habilitará los Servicios de dominio de Azure AD (DS de Azure AD) para Azure AD.
5. Configurará LDAPS para Azure AD. El protocolo ligero de acceso a directorios (LDAP) se usa para realizar operaciones de lectura y escritura en Azure AD.

Si prefiere usar un Azure AD existente, puede omitir los pasos 1 y 2.

**Para crear Azure AD**

1. En el [portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Nuevo** > **App Services** > **Active Directory** > **Directorio** > **Creación personalizada**. 
2. Escriba o seleccione los siguientes valores:
   
   * **Nombre**: contosoaaddirectory
   * **Nombre de dominio**: contoso.  Este nombre debe ser único globalmente.
   * **País o región**: Seleccione el país o región.
3. Haga clic en **Completo**.

**Para crear un usuario de Azure AD**

1. En el [portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Active Directory** -> **contosoaaddirectory**. 
2. Haga clic en **Usuarios** en el menú superior.
3. Haga clic en **Agregar usuario**.
4. Escriba un nombre en **Nombre de usuario** y haga clic en **Siguiente**. 
5. Configure el perfil de usuario; en **Rol**, seleccione **Administrador global** y haga clic en **Siguiente**.  El rol Administrador global se necesita para crear unidades organizativas.
6. Haga clic en **Crear** para obtener una contraseña temporal.
7. Haga una copia de la contraseña y luego haga clic en **Completo**. Más adelante en este tutorial, empleará este usuario administrador global para crear el clúster de HDInsight.

Siga el mismo procedimiento para crear dos usuarios más con el rol **Usuario**: hiveuser1 y hiveuser2. Los siguientes usuarios se usarán en [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md) (Configuración de directivas de Hive para clústeres de HDInsight unidos a un dominio).

**Para crear el grupo de administradores de controlador de dominio de AAD y agregar un usuario de Azure AD**

1. En el [portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Active Directory** > **contosoaaddirectory**. 
2. Haga clic en **Grupos** en el menú superior.
3. Haga clic en **Agregar un grupo** o **Agregar grupo**.
4. Escriba o seleccione los siguientes valores:
   
   * **Nombre**: Administradores de controlador de dominio de AAD.  No cambie el nombre del grupo.
   * **Tipo de grupo**: Seguridad.
5. Haga clic en **Completo**.
6. Haga clic en **Administradores de controlador de dominio de AAD** para abrir el grupo.
7. Haga clic en **Agregar miembros**.
8. Seleccione el primer usuario que creó en el paso anterior y haga clic en **Completo**.
9. Repita los mismos pasos para crear otro grupo denominado **HiveUsers** y agregue los dos usuarios de Hive al grupo.

Para más información, consulte [Introducción a Servicios de dominio de Azure AD - Creación del grupo "Administradores de controladores de dominio de AAD"](../active-directory-domain-services/active-directory-ds-getting-started.md).

**Para habilitar Azure AD DS para Azure AD**

1. En el [portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Active Directory** > **contosoaaddirectory**. 
2. Haga clic en **Configurar** en el menú superior.
3. Desplácese hacia abajo hasta **Servicios de dominio** y establezca los valores siguientes:
   
   * **Habilitar Servicios de dominio para este directorio**: Sí.
   * **Nombre de dominio DNS de Servicios de dominio**: Muestra el nombre de DNS predeterminado del directorio de Azure. Por ejemplo, contoso.onmicrosoft.com.
   * **Conectar Servicios de dominio a esta red virtual**: Seleccione la red virtual clásica que creó anteriormente, por ejemplo **contosoaadvnet**.
4. Haga clic en **Guardar** en la parte inferior de la página. Verá **Pendiente...** junto a **Habilitar Servicios de dominio para este directorio**.  
5. Espere hasta que **Pendiente...** desaparezca y **Dirección IP** se rellene. Se rellenarán dos direcciones IP. Estas son las direcciones IP de los controladores de dominio aprovisionados por Servicios de dominio. Cada dirección IP será visible después de que el controlador de dominio correspondiente se aprovisione y esté preparado. Anote las dos direcciones IP. Las necesitará más adelante.

Para más información, consulte [Servicios de dominio de Azure AD (versión preliminar) - Habilitación de Servicios de dominio de Azure AD](../active-directory-domain-services/active-directory-ds-getting-started-enableaadds.md).

**Para sincronizar la contraseña**

Si usa su propio dominio, necesita sincronizar la contraseña. Consulte [Servicios de dominio de Azure AD (versión preliminar): habilitación de la sincronización de contraseñas con Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).

**Para configurar LDAPS para Azure AD.**

1. Obtenga un certificado SSL firmado por una autoridad de firma para el dominio. Si desea utilizar un certificado autofirmado, póngase en contacto con hdipreview@microsoft.com para una excepción.
2. En el [portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Active Directory** > **contosoaaddirectory**. 
3. Haga clic en **Configurar** en el menú superior.
4. Desplácese a los **servicios de dominio**.
5. Haga clic en **Configurar certificado**.
6. Siga las instrucciones para especificar el archivo de certificado y la contraseña. Verá **Pendiente...** junto a **Habilitar Servicios de dominio para este directorio**.  
7. Espere hasta que **Pendiente...** desaparezca y **Certificado LDAP seguro** se rellene.  Esto puede tardar hasta 10 minutos o más.

> [!NOTE]
> Si algunas tareas en segundo plano se ejecutan en Azure AD DS, puede ver un error al cargar certificado - <i>Hay una operación realizándose para este inquilino. Inténtelo de nuevo más tarde</i>.  En caso de que se produzca este error, inténtelo de nuevo más tarde. La segunda dirección IP del controlador de dominio puede tardar hasta 3 horas en aprovisionarse.
> 
> 

Para más información, consulte [Configuración de LDAP seguro (LDAPS) para un dominio administrado con Servicios de dominio de Azure AD](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>Creación de una red virtual de Resource Manager para un clúster de HDInsight
En esta sección, creará una red virtual de Azure Resource Manager que se utilizará para el clúster de HDInsight. Para más información sobre la creación de una red virtual de Azure utilizando otros métodos, consulte [Creación de una red virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

Después de crear la red virtual, podrá configurar la red virtual de Resource Manager para usar los mismos servidores DNS que para la red virtual de Azure AD. Si ha seguido los pasos de este tutorial para crear la red virtual clásica y Azure AD, los servidores DNS son 10.1.0.4 y 10.1.0.5.

**Para crear una red virtual de Resource Manager**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo**, **Redes** y, a continuación, en **Red virtual**. 
3. En **Seleccionar un modelo de implementación**, seleccione **Resource Manager** y haga clic en **Crear**.
4. Escriba o seleccione los valores siguientes:
   
   * **Nombre**: contosohdivnet
   * **Espacio de direcciones**: 10.2.0.0/16. Asegúrese de que el intervalo de direcciones no puede solaparse con el intervalo de direcciones IP de la red virtual clásica.
   * **Nombre de subred**: Subnet1
   * **Intervalo de direcciones de subred**: 10.2.0.0/24
   * **Suscripción**: (Seleccione su suscripción de Azure.)
   * **Grupo de recursos**: contosohdirg
   * **Ubicación**: (Seleccione la misma ubicación que la red de virtual de Azure AD, es decir, contosoaadvnet.)
5. Haga clic en **Crear**.

**Para configurar DNS para la red virtual de Resource Manager**

1. En [Azure Portal](https://portal.azure.com), haga clic en **Más servicios** -> **Redes virtuales**. Asegúrese de no hacer clic en **Redes virtuales (clásico)**.
2. Haga clic en **contosohdivnet**.
3. Haga clic en **Servidores DNS** en el lado izquierdo de la nueva hoja.
4. Haga clic en **Personalizado** y escriba los siguientes valores:
   
   * 10.1.0.4
   * 10.1.0.5
     
     Estas direcciones IP del servidor DNS deben coincidir con los servidores DNS de la red virtual de Azure AD (red virtual clásica).
5. Haga clic en **Guardar**.

## <a name="peer-the-azure-ad-vnet-and-the-hdinsight-vnet"></a>Emparejamiento de la red virtual de AD Azure y la red virtual de HDInsight
**Para emparejar las dos redes virtuales**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Más servicios** en el menú izquierdo.
3. Haga clic en **Redes virtuales**. No haga clic en **Redes virtuales (clásico)**.
4. Haga clic en **contosohdivnet**.  Se trata de la red virtual de HDInsight.
5. Haga clic en **Emparejamientos** en el menú de la izquierda de la hoja.
6. Haga clic en **Agregar** en el menú superior. Se abrirá la hoja **Agregar emparejamiento**.
7. En la hoja **Agregar emparejamiento**, establezca o seleccione los valores siguientes:
   
   * **Nombre**: ContosoAADHDIVNetPeering
   * **Modelo de implementación de red virtual**: Clásico
   * **Suscripción**: Seleccione el nombre de suscripción que se utiliza para la red virtual clásica (Azure AD).
   * **Red virtual**: contosoaadvnet.
   * **Permitir acceso a red virtual**: (Comprobar)
   * **Permitir tráfico reenviado**: (Comprobar). Deje las otras dos casillas desactivadas.
8. Haga clic en **OK**.

## <a name="create-hdinsight-cluster"></a>Creación de un clúster de HDInsight
En esta sección, creará un clúster de Hadoop basado en Linux en HDInsight con Azure Portal o la [plantilla de Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Para conocer otros métodos de creación de clústeres y la descripción de la configuración, consulte [Creación de clústeres de Hadoop basados en Windows en HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Para más información sobre cómo utilizar la plantilla de Resource Manager para crear clústeres de Hadoop en HDInsight, consulte [Creación de clústeres de Hadoop basados en Windows en HDInsight mediante plantillas de Azure Resource Manager](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

**Para crear un clúster de HDInsight unido a un dominio mediante Azure Portal**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo**, **Inteligencia y análisis** y luego en **HDInsight**.
3. En la hoja **Nuevo clúster de HDInsight**, escriba o seleccione los valores siguientes:
   
   * **Nombre de clúster**: Escriba un nuevo nombre de clúster para el clúster de HDInsight unido a un dominio.
   * **Suscripción**: Seleccione una suscripción de Azure usada para crear este clúster.
   * **Configuración de clúster**:
     
     * **Tipo de clúster**: Hadoop. HDInsight unido a un dominio actualmente solo es compatible con clústeres de Hadoop.
     * **Sistema operativo**: Linux.  HDInsight unido a un dominio solo se admite en clústeres de HDInsight basado en Linux.
     * **Versión**: HDI 3.6. HDInsight unido a un dominio solo se admite en clústeres de HDInsight versión 3.6.
     * **Tipo de clúster**: PREMIUM
       
       Haga clic en **Seleccionar** para guardar los cambios.
   * **Credenciales**: Configure las credenciales para el usuario del clúster y el usuario SSH.
   * **Origen de datos**: Cree una nueva cuenta de Storage o use una cuenta existente cuenta de Storage como la cuenta de Storage para el clúster de HDInsight. La ubicación debe ser la misma que la de las dos redes virtuales.  La ubicación es también la ubicación del clúster de HDInsight.
   * **Precios**: Seleccione el número de nodos de trabajo del clúster.
   * **Configuraciones avanzadas**: 
     
     * **Unión a un dominio y red virtual o subred**: 
       
       * **Configuración de dominio**: 
         
         * **Nombre de dominio**: contoso.onmicrosoft.com
         * **Nombre de usuario de dominio**: Escriba un nombre de usuario de dominio. Este dominio debe tener los siguientes privilegios: unir máquinas al dominio y colocarlas en la unidad organizativa que especificó durante la creación del clúster; crear entidades de servicio dentro de la unidad organizativa que especificó durante la creación del clúster; y crear entradas de DNS inverso. Este usuario de dominio se convertirá en el administrador de este clúster de HDInsight unido a un dominio.
         * **Contraseña de dominio**: Escriba la contraseña de usuario de dominio.
         * **Unidad organizativa**: escriba el nombre distintivo de la unidad organizativa (UO) que desee utilizar con el clúster de HDInsight. Por ejemplo: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com. Si no existe esta unidad organizativa, el clúster de HDInsight intentará crearla. Asegúrese de que la unidad organizativa ya esté presente o de que la cuenta de dominio disponga de los permisos necesarios para crear una nueva. Si utiliza la cuenta de dominio que forma parte de los administradores AADDC, tendrá los permisos pertinentes para crear la unidad organizativa.
         * **Dirección URL de LDAPS**: ldaps://contoso.onmicrosoft.com:636
         * **Grupo de usuarios de acceso**: especifique el grupo de seguridad cuyos usuarios desea que se sincronicen con el clúster. Por ejemplo, HiveUsers.
           
           Haga clic en **Seleccionar** para guardar los cambios.
           
           ![Configuración del dominio del portal de HDInsight unido a un dominio](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **Red virtual**: contosohdivnet
       * **Subred**: Subnet1
         
         Haga clic en **Seleccionar** para guardar los cambios.        
         Haga clic en **Seleccionar** para guardar los cambios.
   * **Grupo de recursos**: Seleccione el grupo de recursos que se usa para la red virtual de HDInsight (contosohdirg).
4. Haga clic en **Crear**.  

Otra opción para crear el clúster de HDInsight unido a un dominio es utilizar la plantilla de Azure Resource Management. El siguiente procedimiento muestra cómo:

**Para crear un clúster de HDInsight unido a un dominio mediante una plantilla de Resource Manager**

1. Haga clic en la imagen siguiente para abrir una plantilla de Resource Manager en Azure Portal. La plantilla de Resource Manager se encuentra en un contenedor de blobs público. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/hdinsight-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. En la hoja **Parámetros**, escriba los siguientes valores:
   
   * **Suscripción**: (Seleccione su suscripción de Azure).
   * **Grupo de recursos**: Haga clic en **Usar existente** y especifique el mismo grupo de recursos que ha estado usando.  Por ejemplo, contosohdirg. 
   * **Ubicación**: Especifique una ubicación del grupo de recursos.
   * **Nombre de clúster**: Escriba un nombre para el clúster de Hadoop que va a crear. Por ejemplo, contosohdicluster.
   * **Tipo de clúster**: seleccione un tipo de clúster.  El valor predeterminado es **hadoop**.
   * **Ubicación**: Seleccione una ubicación para el clúster.  La cuenta de almacenamiento predeterminada usa la misma ubicación.
   * **Recuento de nodos de trabajo de clúster**: Seleccione el número de nodos de trabajo.
   * **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es **admin**.
   * **Nombre de usuario y contraseña de SSH**: el nombre de usuario predeterminado es **sshuser**.  Puede cambiarlo. 
   * **Id. de red virtual**: /subscriptions/&lt;Id.Suscripción>/resourceGroups/&lt;NombreGrupoRecursos>/providers/Microsoft.Network/virtualNetworks/&lt;NombreRedVirtual>
   * **Subred de la red virtual**: /subscriptions/&lt;Id.Suscripción>/resourceGroups/&lt;NombreGrupoRecursos>/providers/Microsoft.Network/virtualNetworks/&lt;NombreRedVirtual>/subnets/Subnet1
   * **Nombre de dominio**: contoso.onmicrosoft.com
   * **DN de unidad organizativa**: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
   * **Nombres de dominio del grupo de usuarios del clúster**: [\"HiveUsers\"]
   * **LDAPUrls**: ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**: (Escriba el nombre de usuario de administrador de dominio)
   * **DomainAdminPassword**: (Escriba la contraseña de usuario de administrador de dominio)
   * **Acepto los términos y condiciones indicados anteriormente** (Comprobar)
   * **Anclar al panel**: (Comprobar)
3. Haga clic en **Comprar**. Verá un icono nuevo llamado **Implementación para la implementación de plantilla**. Tarda aproximadamente 20 minutos en crear un clúster. Una vez creado el clúster, puede hacer clic en la hoja del clúster en el portal para abrirlo.

Después de completar el tutorial, quizá desee eliminar el clúster. Con HDInsight, los datos se almacenan en Almacenamiento de Azure, por lo que puede eliminar un clúster de forma segura cuando no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso. Para ver instrucciones sobre cómo eliminar un clúster, consulte [Administración de clústeres de Hadoop en HDInsight mediante Azure Portal](hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Pasos siguientes
* Para configurar directivas de Hive y ejecución de consultas de Hive, consulte [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md) (Configuración de directivas de los clústeres de HDInsight unidos a dominio).
* Para utilizar SSH para conectarse a clústeres de HDInsight unidos a dominio, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

