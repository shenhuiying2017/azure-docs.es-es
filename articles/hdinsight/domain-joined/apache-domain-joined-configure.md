---
title: "Configuración de clústeres de HDInsight unidos a un dominio (versión preliminar): Azure | Microsoft Docs"
description: "Aprenda a configurar y definir clústeres de HDInsight unidos a un dominio."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: cgronlun
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/15/2018
ms.author: saurinsh
ms.openlocfilehash: b4d71eeb0aab75e67e851f867f194ed7578d0d1c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="configure-domain-joined-hdinsight-sandbox-environment"></a>Configuración de un entorno de espacio aislado de HDInsight unido a un dominio

Aprenda a configurar un clúster de Azure HDInsight con Active Directory independiente y [Apache Ranger](http://hortonworks.com/apache/ranger/) para aprovechar la autenticación sólida y las directivas de control de acceso basado en rol (RBAC) enriquecidas. Para más información, consulte [Introduce Domain-joined HDInsight clusters](apache-domain-joined-introduction.md) (Introducción a los clústeres de HDInsight unidos a dominio (versión preliminar)) 

> [!IMPORTANT]
> De forma predeterminada, esta configuración solo puede utilizarse con cuentas de Azure Storage. Para utilizarla con Azure Data Lake Store, sincronice Active Directory con una nueva instancia de Azure Active Directory.

Sin un clúster de HDInsight unido a un dominio, cada clúster solo puede tener una cuenta de usuarios HTTP de Hadoop y una cuenta de usuario SSH.  La autenticación de varios usuario puede lograrse mediante:

-   Una instancia de Active Directory independiente que se ejecute en Azure IaaS.
-   Azure Active Directory.

El uso de un Active Directory independiente que se ejecute en Azure IaaS se trata en este artículo. Es la arquitectura más sencilla de que un cliente puede usar para poder utilizar varios usuario en HDInsight. En este artículo se explican dos enfoques para esta configuración:

- Opción 1: Usar una plantilla de administración de recursos de Azure para crear la instancia de Active Directory independiente y el clúster de HDInsight.
- Opción 2: Todo el proceso se divide en los siguientes pasos:
    - Creación de una instancia de Active Directory mediante una plantilla.
    - Instalación de LDAPS.
    - Creación de usuarios y grupos de AD
    - Creación de un clúster de HDInsight

> [!IMPORTANT]
> 
> Oozie no está habilitado en HDInsight unido a un dominio.

## <a name="prerequisite"></a>Requisito previo
* Suscripción de Azure

## <a name="option-1-one-step-approach"></a>Opción 1: Enfoque de un paso
En esta sección se abre una plantilla de administración de recursos de Azure desde Azure Portal. La plantilla se utiliza para crear una instancia de Active Directory independiente y un clúster de HDInsight. Actualmente se puede crear un clúster de Hadoop unidos a un dominio, un clúster Spark y un clúster de Interactive Query.

1. Haga clic en la imagen siguiente para abrir la plantilla en el Portal de Azure. La plantilla se encuentra en las [plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/).
   
    Para crear un clúster de Spark:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fspark%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Para crear un clúster de Interactive Query:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Finteractivequery%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Para crear un clúster de Hadoop:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fhadoop%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Escriba los valores, seleccione **Acepto los términos y condiciones indicados anteriormente**, seleccione **Anclar al panel** y haga clic en **Comprar**. Mantenga el cursor del mouse sobre el signo de explicación que hay al lado de los campos para ver las descripciones. La mayoría de los valores se han rellenado. Puede usar los valores predeterminados o sus propios valores.

    - **Grupo de recursos**: escriba el nombre de un grupo de recursos de Azure.
    - **Ubicación**: elija una ubicación cercana.
    - **Nuevo nombre de cuenta de almacenamiento**: escriba un nombre de cuenta de Azure Storage. Esta nueva cuenta de almacenamiento la utilizan el controlador de dominio principal, el BDC y el clúster de HDInsight como cuenta de almacenamiento predeterminada.
    - **Nombre de usuario de administrador**: escriba el nombre de usuario del administrador del dominio.
    - **Contraseña de administrador**: escriba la contraseña del administrador del dominio.
    - **Nombre de dominio**: el nombre predeterminado es *contoso.com*.  Si cambia el nombre de dominio, también debe actualizar los campos **Certificado de LDAP seguro** y **DN de unidad organizativa**.
    - **Prefijo de DNS**: escriba el prefijo de DNS para la dirección IP pública que usa Load Balancer.
    - **Nombre de clúster**: escriba el nombre del clúster de HDInsight.
    - **Tipo de clúster**: no cambie este valor. Si desea cambiar el tipo de clúster, use la plantilla específica en el último paso.
    - **Proteger la contraseña del certificado LDAP**: use el valor predeterminado a menos que cambie el campo del certificado de LDAP seguro.
    Algunos de los valores están codificados de forma rígida en la plantilla, por ejemplo, el número de instancias del nodo de trabajo es dos.  Para cambiar los valores codificados de forma rígida, haga clic en **Editar plantilla**.

    ![Plantilla de edición de clústeres unidos a un dominio de HDInsight](./media/apache-domain-joined-configure/hdinsight-domain-joined-edit-template.png)

Cuando la plantilla se completa correctamente, hay 23 recursos creados en el grupo de recursos.

## <a name="option-2-multi-step-approach"></a>Opción 2: método de varios pasos

En esta sección hay cuatro pasos:

1. Creación de una instancia de Active Directory mediante una plantilla.
2. Instalación de LDAPS.
3. Creación de usuarios y grupos de AD
4. Creación de un clúster de HDInsight

### <a name="create-an-active-directory"></a>Creación de una instancia de Active Directory

La plantilla de Azure Resource Manager facilita la creación de recursos de Azure. En esta sección se utiliza una [plantilla de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/active-directory-new-domain-ha-2-dc/) para crear un bosque y un dominio nuevos con dos máquinas virtuales. Las dos máquinas virtuales actúan como controlador de dominio principal y controlador de dominio de copia de seguridad.

**Para crear un dominio con dos controladores de dominio**

1. Haga clic en la imagen siguiente para abrir la plantilla en el Portal de Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Así es la plantilla:

    ![Clúster unido a dominio de HDInsight: crear máquinas virtuales en un dominio de bosque](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-arm-template.png)

2. Escriba los siguientes valores:

    - **Suscripción**: seleccione una suscripción de Azure.
    - **Nombre del grupo de recursos**: escriba el nombre de un grupo de recursos.  Un grupo de recursos se utiliza para administrar los recursos de Azure relacionados con un proyecto.
    - **Ubicación**: seleccione una ubicación de Azure cercana a usted.
    - **Nombre de usuario de administrador**: es el nombre de usuario del administrador del dominio. Este usuario no es la cuenta de usuario HTTP de su clúster de HDInsight. Se trata de la cuenta que usa en el tutorial.
    - **Contraseña de administrador**: escriba la contraseña del administrador del dominio.
    - **Nombre de dominio**: el nombre de dominio debe tener de dos partes. Por ejemplo: contoso.com, contoso.local o hdinsight.test.
    - **Prefijo de DNS**: escriba un prefijo DNS
    - **Puerto RDP de PDC**: (use el valor predeterminado en este tutorial)
    - **Puerto RDP de BDC**: (use el valor predeterminado en este tutorial)
    - **Puerto RDP de BDC** (Ubicación de artefactos): (use el valor predeterminado en este tutorial)
    - **artifacts Location SAS token** (Token SAS de ubicación de artefactos): (déjelo vacío en este tutorial.)

Los recursos tardan aproximadamente 20 minutos en crearse.

### <a name="setup-ldaps"></a>Instalación de LDAPS

El protocolo ligero de acceso a directorios (LDAP) se usa para leer de AD y escribir en él.

**Para conectarse al controlador de dominio principal través de Escritorio remoto**

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Abra el grupo de recursos y, después, abra la máquina virtual del controlador de dominio principal (PDC). El nombre de controlador de dominio principal predeterminado es adPDC. 
3. Haga clic en **Conectar** para conectarse al controlador de dominio principal través de Escritorio remoto.

    ![Escritorio remoto de PDC de conexión unido al dominio de HDInsight](./media/apache-domain-joined-configure/hdinsight-domain-joined-remote-desktop-pdc.png)


**Para agregar Servicios de certificados de Active Directory**

4. Abra el **Administrador del servidor**, en caso de que no esté abierto.
5. Haga clic en **Administrar** y luego en **Agregar roles y características**.

    ![Clúster unido a dominio de HDInsight: agregar roles y características](./media/apache-domain-joined-configure/hdinsight-domain-joined-add-roles.png)
5. En "Antes de comenzar", haga clic en **Siguiente**.
6. Seleccione **Instalación basada en características o en roles** y haga clic en **Siguiente**.
7. Seleccione el controlador de dominio principal y haga clic en **Siguiente**.  El nombre de controlador de dominio principal predeterminado es adPDC.
8. Seleccione **Servicios de certificados de Active Directory**.
9. Haga clic en **Agregar características** en el cuadro de diálogo emergente.
10. Siga el asistente, use la configuración predeterminada para el resto del procedimiento.
11. Haga clic en **Cerrar** para cerrar el asistente.

**Para configurar un certificado de AD**

1. En el Administrador del servidor, haga clic en el icono de notificación amarillo y, después, haga clic en **Configurar Servicios de certificados de Active Directory**.

    ![Clúster unido a dominio de HDInsight: configurar un certificado de AD](./media/apache-domain-joined-configure/hdinsight-domain-joined-configure-ad-certificate.png)

2. Haga clic en **Servicios de rol** a la izquierda, seleccione **Entidad de certificación**y, luego, haga clic en **Siguiente**.
3. Siga el asistente y use la configuración predeterminada para el resto del procedimiento (haga clic en **Configurar** en el último paso).
4. Haga clic en **Cerrar** para cerrar el asistente.

### <a name="optional-create-ad-users-and-groups"></a>(Opcional) Creación de usuarios y grupos de AD

**Para crear usuarios y grupos en AD**
1. Conéctese al controlador de dominio principal mediante Escritorio remoto
1. Abra **Usuarios y equipos de Active Directory**.
2. Seleccione el nombre de dominio en el panel izquierdo.
3. Haga clic en el icono **Crear un nuevo usuario en el contenedor actual** del menú superior.

    ![Clúster unido a dominio de HDInsight: crear usuarios](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-ad-user.png)
4. Siga las instrucciones para crear varios usuarios. Por ejemplo, hiveuser1 y hiveuser2.
5. Haga clic en el icono **Crear un nuevo grupo en el contenedor actual.** del menú superior.
6. Siga las instrucciones para crear un grupo denominado **HDInsightUsers**.  Este grupo se utilizará más adelante en el tutorial, cuando se cree clúster de HDInsight.

> [!IMPORTANT]
> Antes de crear un clúster de HDInsight unido a un dominio es preciso reiniciar la máquina virtual del controlador de dominio principal.

### <a name="create-an-hdinsight-cluster-in-the-vnet"></a>Creación de un clúster de HDInsight en la red virtual

En esta sección, usará Azure Portal para agregar un clúster de HDInsight a la red virtual que creó mediante la plantilla de Resource Manager anteriormente en el tutorial. Este artículo solo abarca la información específica de la configuración de clústeres unidos a un dominio.  Para obtener una información general, consulte [Creación de clústeres basados en Linux en HDInsight con Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).  

**Para crear un clúster de HDInsight unido a un dominio**

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Abra el grupo de recursos que creó mediante la plantilla de Resource Manager anteriormente en el tutorial.
3. Agregue un clúster de HDInsight al grupo de recursos.
4. Seleccione la opción **Personalizar**:

    ![Clúster unido a dominio de HDInsight: opción de creación personalizada](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-custom-configuration-option.png)

    La opción de configuración personalizada cuenta con seis secciones: Datos básicos, Almacenamiento, Aplicación, Tamaño de clúster, Configuración avanzada y Resumen.
5. En la sección **Datos básicos**:

    - Tipo de clúster: seleccione **Paquete de seguridad de la empresa**. Actualmente, el paquete de seguridad de la empresa solo se puede habilitar para los siguientes tipos de clúster: Hadoop, Interactive Query y Spark.

        ![Clúster unido a dominio de HDInsight: paquete de seguridad de la empresa](./media/apache-domain-joined-configure/hdinsight-creation-enterprise-security-package.png)
    - Nombre de usuario de inicio de sesión del clúster: es el nombre de usuario HTTP de Hadoop. Esta cuenta no es la misma que la cuenta de administrador de dominio.
    - Grupo de recursos: seleccione el grupo de recursos que creó anteriormente mediante la plantilla de Resource Manager.
    - Ubicación: la ubicación debe ser la misma que utilizó al crear la red virtual y los controladores de dominio mediante la plantilla de Resource Manager.

6. En la sección **Configuración avanzada**:

    - Configuración de dominio:

        ![Configuración avanzada de dominio unido al dominio de HDInsight](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-advanced-domain-settings.png)
        
        - Nombre de dominio: escriba el nombre de dominio que se usó en [Creación de una instancia de Active Directory](#create-an-active-directory).
        - Nombre de usuario de dominio: escriba el nombre de usuario de administrador de AD que se usó en [Creación de una instancia de Active Directory](#create-an-active-directory).
        - Unidad organizativa: en la captura de pantalla se puede ver un ejemplo.
        - URL de LDAPS: en la captura de pantalla se puede ver un ejemplo
        - Grupo de usuarios de acceso: especifique el nombre de grupo de usuarios que creó en [Creación de usuarios y grupos de AD](#optionally-createad-users-and-groups)
    - Red virtual: seleccione la red virtual que creó en [Creación de una instancia de Active Directory](#create-an-active-directory). El nombre predeterminado que se utiliza en la plantilla es **adVNET**.
    - Subred: el nombre predeterminado que se utiliza en la plantilla es **adSubnet**.



Después de completar el tutorial, quizá desee eliminar el clúster. Con HDInsight, los datos se almacenan en Azure Storage, por lo que puede eliminar un clúster de forma segura cuando no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso. Para ver instrucciones sobre cómo eliminar un clúster, consulte [Administración de clústeres de Hadoop en HDInsight mediante Azure Portal](../hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>pasos siguientes
* Para configurar directivas de Hive y ejecución de consultas de Hive, consulte [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md) (Configuración de directivas de los clústeres de HDInsight unidos a dominio).
* Para utilizar SSH para conectarse a clústeres de HDInsight unidos a dominio, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

