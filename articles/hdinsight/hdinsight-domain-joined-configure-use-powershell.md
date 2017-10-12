---
title: "Configuración de clústeres de HDInsight unidos a un dominio con Azure PowerShell | Microsoft Docs"
description: "Aprenda a configurar y definir clústeres de HDInsight unidos a un dominio con Azure PowerShell."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: a13b2f7a-612d-4800-bc92-7fc0524f3e89
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
ms.openlocfilehash: 9da76bb5f649817cd2f027f3d0eb46d58a996b4f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-domain-joined-hdinsight-clusters-preview-using-azure-powershell"></a>Configuración de clústeres de HDInsight unidos a un dominio (versión preliminar) con Azure PowerShell
Vea cómo configurar un clúster de HDInsight de Azure con Azure Active Directory (Azure AD) y [Apache Ranger](http://hortonworks.com/apache/ranger/) con Azure PowerShell. Se proporciona un script de Azure PowerShell para realizar la configuración más rápidamente y con menos errores. HDInsight unido a un dominio solo se puede configurar en clústeres basados en Linux. Para más información, consulte [Introduce Domain-joined HDInsight clusters](hdinsight-domain-joined-introduction.md) (Introducción a los clústeres de HDInsight unidos a dominio (versión preliminar))

> [!IMPORTANT]
> Oozie no está habilitado en HDInsight unido a un dominio.

Una configuración típica de un clúster de HDInsight unidos a un dominio implica los pasos siguientes:

1. Cree una red virtual clásica de Azure para su Azure AD.  
2. Cree y configure Azure AD y Azure AD DS.
3. Agregue una VM a la red virtual clásica para crear una unidad organizativa. 
4. Cree una unidad organizativa para Azure AD DS.
5. Cree una red virtual de HDInsight en el modo de administración de recursos de Azure.
6. Configure zonas DNS inversas para Azure AD DS.
7. Empareje las dos redes virtuales.
8. Cree un clúster de HDInsight.

El script de PowerShell proporcionado realiza los pasos del 3 al 7. Debe seguir los pasos 1 y 2 manualmente.  Si prefiere no usar Azure PowerShell, consulte [Configuración de clústeres de HDInsight unidos a un dominio](hdinsight-domain-joined-configure.md). 

Un ejemplo de la topología final tiene el aspecto siguiente:

![Topología de HDInsight unido a un dominio](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-topology.png)

Dado que Azure AD actualmente solo admite redes virtuales clásicas (VNet) y los clústeres de HDInsight basados en Linux solo admiten Azure Resource Manager basado en redes virtuales, la integración de Azure AD de HDInsight requiere dos redes virtuales y un emparejamiento entre ellas. Para obtener información comparativa entre dos modelos de implementación, consulte [La implementación de Azure Resource Manager frente a la implementación clásica: los modelos de implementación y el estado de los recursos](../azure-resource-manager/resource-manager-deployment-model.md). Las dos redes virtuales deben estar en la misma región que Azure AD DS.

> [!NOTE]
> En este tutorial se supone que no tiene una cuenta de Azure AD. Si tiene una, puede omitir la parte del paso 2.
> 
> 

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* Familiarícese con [Servicios de dominio de Azure AD](https://azure.microsoft.com/services/active-directory-ds/) y su estructura de [precios](https://azure.microsoft.com/pricing/details/active-directory-ds/).
* Asegúrese de que la suscripción está en la lista blanca para esta versión preliminar pública. Puede hacerlo enviando un correo electrónico a hdipreview@microsoft.com con su identificador de suscripción.
* Un certificado SSL firmado por una autoridad de firma para el dominio. Se necesita el certificado por la configuración de LDAP seguro. Los certificados autofirmados no se pueden usar.
* Azure PowerShell.  Consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

## <a name="create-an-azure-classic-vnet-for-your-azure-ad"></a>Cree una red virtual clásica de Azure para su Azure AD.
Encontrará instrucciones [aquí](hdinsight-domain-joined-configure.md#create-an-azure-virtual-network-classic).

## <a name="create-and-configure-azure-ad-and-azure-ad-ds"></a>Cree y configure Azure AD y Azure AD DS.
Encontrará instrucciones [aquí](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad).

## <a name="run-the-powershell-script"></a>Ejecución del script de PowerShell
El script de PowerShell se puede descargar desde [GitHub](https://github.com/hdinsight/DomainJoinedHDInsight). Extraiga el archivo .zip y guarde los archivos localmente.

**Para editar el script de PowerShell**

1. Abra run.ps1 mediante Windows PowerShell ISE o cualquier editor de texto.
2. Rellene los valores de las siguientes variables:
   
   * **$SubscriptionName**: nombre de la suscripción de Azure donde desea crear el clúster de HDInsight. Ya ha creado una red virtual clásica en esta suscripción y va a crear una red virtual de Azure Resource Manager para el clúster de HDInsight en la suscripción.
   * **$ClassicVNetName**: red virtual clásica que contiene Azure AD DS. Esta red virtual debe estar en la misma suscripción que se indicó anteriormente. Esta red virtual debe crearse mediante Azure Portal y no mediante el portal clásico. Si sigue las instrucciones de [Configuración de clústeres de HDInsight unidos a un dominio (versión preliminar)](hdinsight-domain-joined-configure.md#create-an-azure-virtual-network-classic), el nombre predeterminado es contosoaadvnet.
   * **$ClassicResourceGroupName**: nombre de grupo de Resource Manager para la red virtual clásica que esté mencionada anteriormente. Por ejemplo, contosoaadrg. 
   * **$ArmResourceGroupName**: nombre del grupo de recursos en el que desea crear el clúster de HDInsight. Puede usar el mismo grupo de recursos que $ArmResourceGroupName.  Si no existe el grupo de recursos, el script lo crea.
   * **$ArmVNetName**: nombre de la red virtual de Resource Manager en la que desea crear el clúster de HDInsight. Esta red virtual se colocará en $ArmResourceGroupName.  Si la red virtual no existe, el script de PowerShell la creará. Si existe, debe formar parte del grupo de recursos que se proporciona anteriormente.
   * **$AddressVnetAddressSpace**: el espacio de direcciones de red para la red virtual de Resource Manager. Asegúrese de que este espacio de direcciones está disponible. Este espacio de direcciones no puede superponerse al espacio de direcciones de la red virtual clásica. Por ejemplo, “10.1.0.0/16”
   * **$ArmVnetSubnetName**: nombre de la subred virtual de Resource Manager en la que desea colocar las máquinas virtuales del clúster de HDInsight. Si la subred no existe, el script de PowerShell la creará. Si existe, debe formar parte de la red virtual que se proporciona anteriormente.
   * **$AddressSubnetAddressSpace**: intervalo de direcciones de red para la subred virtual de Resource Manager. Las direcciones IP de las máquinas virtuales del clúster de HDInsight pertenecerán a este intervalo de direcciones de subred. Por ejemplo, “10.1.0.0/24”.
   * **$ActiveDirectoryDomainName**: nombre de dominio de Azure AD al que desea unir las máquinas virtuales del clúster de HDInsight. Por ejemplo, “contoso.onmicrosoft.com”.
   * **$ClusterUsersGroups**: nombre común de los grupos de seguridad de AD que desea sincronizar con el clúster de HDInsight. Los usuarios de este grupo de seguridad podrán iniciar sesión en el panel del clúster con sus credenciales de dominio de Active Directory. Estos grupos de seguridad deben existir en Active Directory. Por ejemplo, "hiveusers" o "clusteroperatorusers".
   * **$OrganizationalUnitName**: unidad organizativa en el dominio en el que desea colocar las máquinas virtuales del clúster de HDInsight y las entidades de servicio utilizadas por el clúster. Si no existe, el script de PowerShell creará esta unidad organizativa. Por ejemplo, "HDInsightOU".
3. Guarde los cambios.

**Para ejecutar el script**

1. Ejecute **Windows PowerShell** como administrador.
2. Vaya a la carpeta de run.ps1. 
3. Para ejecutar el script, escriba el nombre de archivo y presione **ENTRAR**.  Se muestran tres cuadros de diálogo de inicio de sesión:
   
   1. **Iniciar sesión en el portal de Azure clásico**: escriba las credenciales que usa para iniciar sesión en el portal de Azure clásico. Debe haber creado las instancias de Azure AD y Azure AD DS con estas credenciales.
   2. **Iniciar sesión en el portal de Azure Resource Manager**: escriba las credenciales que usa para iniciar sesión en el portal de Azure Resource Manager.
   3. **Nombre de usuario de dominio**: escriba las credenciales del nombre de usuario de dominio que quiere que sea administrador en el clúster de HDInsight. Si creó una instancia de Azure AD de cero, debe haber creado este usuario usando esta documentación. 
      
      > [!IMPORTANT]
      > Escriba el nombre de usuario con este formato: 
      > 
      > NombreDominio\nombreUsuario (por ejemplo, contoso.onmicrosoft.com\clusteradmin)
      > 
      > 
      
      Este usuario debe tener privilegios de 3: para unir máquinas al dominio de Active Directory proporcionado; para crear entidades de servicio y objetos de máquina dentro de la unidad organizativa proporcionada; y para agregar reglas de proxy DNS inversas.

Al crear las zonas DNS inversas, el script le pedirá que escriba un identificador de red. Este identificador de red debe ser el prefijo de la dirección de la red virtual de Resource Manager. Por ejemplo, si el espacio de direcciones de subred de la red virtual de Resource Manager es 10.2.0.0/24, escriba 10.2.0.0/24 cuando la herramienta le pida el identificador de red. 

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
     * **Versión**: Hadoop 2.7.3 (HDI 3.5). HDInsight unido a un dominio solo se admite en clústeres de HDInsight versión 3.5.
     * **Tipo de clúster**: PREMIUM
       
       Haga clic en **Seleccionar** para guardar los cambios.
   * **Credenciales**: Configure las credenciales para el usuario del clúster y el usuario SSH.
   * **Origen de datos**: Cree una nueva cuenta de Storage o use una cuenta existente cuenta de Storage como la cuenta de Storage para el clúster de HDInsight. La ubicación debe ser la misma que la de las dos redes virtuales.  La ubicación es también la ubicación del clúster de HDInsight.
   * **Precios**: Seleccione el número de nodos de trabajo del clúster.
   * **Configuraciones avanzadas**: 
     
     * **Unión a un dominio y red virtual o subred**: 
       
       * **Configuración de dominio**: 
         
         * **Nombre de dominio**: contoso.onmicrosoft.com
         * **Nombre de usuario de dominio**: Escriba un nombre de usuario de dominio. Este dominio debe tener los siguientes privilegios: unir máquinas al dominio y colocarlas en la unidad organizativa que configuró anteriormente; crear entidades de seguridad de servicio dentro de la unidad de organización que configuró anteriormente; crear entradas de DNS inverso. Este usuario de dominio se convertirá en el administrador de este clúster de HDInsight unido a un dominio.
         * **Contraseña de dominio**: Escriba la contraseña de usuario de dominio.
         * **Unidad organizativa**: escriba el nombre distintivo de la unidad organizativa que configuró anteriormente. Por ejemplo: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
         * **Dirección URL de LDAPS**: ldaps://contoso.onmicrosoft.com:636
         * **Grupo de usuarios de acceso**: Especifique el grupo de seguridad cuyos usuarios desea que realicen la sincronización con el clúster. Por ejemplo, HiveUsers.
           
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
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/hdinsight-domain-joined-configure-use-powershell/deploy-to-azure.png" alt="Deploy to Azure"></a>
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
   * **Id. de red virtual**: /subscriptions/&lt;Id.Suscripción&gt;/resourceGroups/&lt;NombreGrupoRecursos&gt;/providers/Microsoft.Network/virtualNetworks/&lt;NombreRedVirtual&gt;
   * **Subred de la red virtual**: /subscriptions/&lt;Id.Suscripción&gt;/resourceGroups/&lt;NombreGrupoRecursos&gt;/providers/Microsoft.Network/virtualNetworks/&lt;NombreRedVirtual&gt;/subnets/Subnet1
   * **Nombre de dominio**: contoso.onmicrosoft.com
   * **DN de unidad organizativa**: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
   * **DN de grupo de usuarios de clúster**: "\"CN=HiveUsers,OU=AADDC Users,DC=<DomainName>,DC=onmicrosoft,DC=com\""
   * **LDAPUrls**: ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**: (Escriba el nombre de usuario de administrador de dominio)
   * **DomainAdminPassword**: (Escriba la contraseña de usuario de administrador de dominio)
   * **Acepto los términos y condiciones indicados anteriormente** (Comprobar)
   * **Anclar al panel**: (Comprobar)
3. Haga clic en **Comprar**. Verá un icono nuevo llamado **Implementación para la implementación de plantilla**. Tarda aproximadamente 20 minutos en crear un clúster. Una vez creado el clúster, puede hacer clic en la hoja del clúster en el portal para abrirlo.

Después de completar el tutorial, quizá desee eliminar el clúster. Con HDInsight, los datos se almacenan en Almacenamiento de Azure, por lo que puede eliminar un clúster de forma segura cuando no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso. Para ver instrucciones sobre cómo eliminar un clúster, consulte [Administración de clústeres de Hadoop en HDInsight mediante Azure Portal](hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Pasos siguientes

* Para configurar directivas de Hive y ejecución de consultas de Hive, consulte [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md) (Configuración de directivas de los clústeres de HDInsight unidos a dominio).
* Para establecer conexiones mediante SSH a clústeres de HDInsight unidos a un dominio, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

