<properties
   	pageTitle="Instalación de aplicaciones de Hadoop en HDInsight | Microsoft Azure"
   	description="Obtenga información acerca de cómo instalar aplicaciones de HDInsight en aplicaciones de HDInsight."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="jhubbard"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="09/14/2016"
   	ms.author="jgao"/>

# Instalación de aplicaciones de HDInsight

Una aplicación de HDInsight es una aplicación que los usuarios pueden instalar en un clúster de HDInsight basado en Linux. Estas aplicaciones puede desarrollarlas Microsoft, fabricantes de software independientes (ISV) o el propio usuario. En este artículo, aprenderá a instalar una aplicación publicada. Para instalar su propia aplicación, consulte [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).

Actualmente hay una aplicación publicada:

- **Datameer**: [Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft) ofrece a los analistas una forma interactiva de detectar, analizar y visualizar los resultados sobre macrodatos. Reúna más orígenes de datos con facilidad para detectar nuevas relaciones y obtener las respuestas que necesita rápidamente.

>[AZURE.NOTE] Actualmente, Datameer solo es compatible con clústeres de la versión 3.2 de HDInsight de Azure.

Para las instrucciones proporcionadas en este artículo se usa Azure Portal. También puede exportar la plantilla de Azure Resource Manager desde el portal u obtener una copia de dicha plantilla de proveedores y usar Azure PowerShell y CLI de Azure para implementarla. Consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## Requisitos previos

Si desea instalar aplicaciones de HDInsight en un clúster de HDInsight existente, debe tener un clúster de HDInsight. Para crearlo, consulte [Crear clúster](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). También puede instalar aplicaciones de HDInsight al crear un clúster de HDInsight.

## Instalación de aplicaciones en clústeres existentes

En el siguiente procedimiento, se muestra cómo instalar aplicaciones de HDInsight en un clúster de HDInsight existente.

**Para instalar una aplicación de HDInsight**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda. Si no lo ve, haga clic en **Examinar** y en **Clústeres de HDInsight**.
3. Haga clic en un clúster de HDInsight. Si no tiene ninguno, debe crearlo primero. Consulte [Crear clúster](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).
4. En la hoja **Configuración**, haga clic en **Aplicaciones** en la categoría **General**. En la hoja **Aplicaciones instaladas**, aparecen todas las aplicaciones instaladas.

    ![Menú de portal para aplicaciones de HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)

5. Haga clic en **Agregar** en el menú de la hoja.

    ![Aplicaciones instaladas para aplicaciones de HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps.png)

	Verá una lista de las aplicaciones existentes de HDInsight.

	![Aplicaciones disponibles para aplicaciones de HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)

6. Haga clic en una de las aplicaciones, acepte los términos legales y haga clic en **Seleccionar**.

En el portal aparece una notificación donde puede comprobar el estado de la instalación (haga clic en el icono de campana que encontrará en la parte superior del portal). Una vez instalada la aplicación, aparecerá en la hoja Aplicaciones instaladas.

## Instalación de aplicaciones durante la creación del clúster

Tiene la opción de instalar aplicaciones de HDInsight al crear un clúster. Durante el proceso, se instalan aplicaciones de HDInsight una vez que se haya creado el clúster y se encuentre en ejecución. En el siguiente procedimiento, se muestra cómo instalar aplicaciones de HDInsight cuando se crea un clúster.

**Para instalar una aplicación de HDInsight**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **NUEVO**, en **Datos y análisis** y luego en **HDInsight**.
3. Escriba un nombre en **Nombre de clúster**: debe ser único globalmente.
4. Haga clic en **Suscripción** para seleccionar la suscripción de Azure que se usará para el clúster.
5. Haga clic en **Seleccionar tipo de clúster** y luego seleccione:

    - **Tipo de clúster**: si no sabe qué opción elegir, seleccione **Hadoop**. Es el tipo de clúster más popular.
    - **Sistema operativo**: seleccione **Linux**.
    - **Versión**: use la versión predeterminada si no sabe qué opción elegir. Para obtener más información, consulte [Versiones de clústeres de HDInsight](hdinsight-component-versioning.md).
    - **Nivel de clúster**: HDInsight de Azure proporciona las ofertas de macrodatos en la nube en dos categorías: Estándar y Premium. Para más información, consulte [Niveles de clúster](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).
6. Haga clic en **Aplicaciones**, en una de las aplicaciones publicadas y en **Seleccionar**.
6. Haga clic en **Credenciales** y escriba una contraseña para el usuario administrador. También debe especificar un **Nombre de usuario de SSH** y una **CONTRASEÑA** o una **CLAVE PÚBLICA**, que se usarán para autenticar al usuario de SSH. Es recomendable usar una clave pública. Haga clic en **Seleccionar** en la parte inferior para guardar la configuración de las credenciales.
8. Haga clic en **Origen de datos**, seleccione una cuenta de almacenamiento existente o cree una para usarla como cuenta de almacenamiento predeterminada del clúster.
9. Haga clic en **Grupo de recursos** para seleccionar un grupo de recursos existente o en **Nuevo** para crear uno.

10. En la hoja **Nuevo clúster de HDInsight**, asegúrese de que la opción **Anclar a Panel de inicio** está seleccionada y haga clic en **Crear**.

## Lista de las aplicaciones de HDInsight instaladas y sus propiedades

El portal muestra una lista de las aplicaciones de HDInsight instaladas para un clúster y las propiedades de cada una.

**Para ver una lista de las aplicaciones de HDInsight y mostrar sus propiedades**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda. Si no lo ve, haga clic en **Examinar** y en **Clústeres de HDInsight**.
3. Haga clic en un clúster de HDInsight.
4. En la hoja **Configuración**, haga clic en **Aplicaciones** en la categoría **General**. En la hoja Aplicaciones instaladas, aparecen todas las aplicaciones instaladas.

	![Aplicaciones instaladas para aplicaciones de HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)

5. Haga clic en una de las aplicaciones instaladas para mostrar la propiedad. En la hoja de la propiedad, aparecen los siguientes elementos:

    - Nombre de la aplicación: el nombre de la aplicación.
    - Estado: el estado de la aplicación.
    - Página web: la dirección URL de la aplicación web que ha implementado en el nodo perimetral si existe. La credencial coincide con las credenciales de usuario HTTP que ha configurado para el clúster.
    - Punto de conexión HTTP: la credencial coincide con las credenciales de usuario HTTP que ha configurado para el clúster.
    - Punto de conexión SSH: puede usar [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) para conectar con el nodo perimetral. Las credenciales de SSH coinciden con las credenciales de usuario SSH que ha configurado para el clúster.

6. Para eliminar una aplicación, haga clic con el botón derecho en ella y haga clic en **Eliminar** en el menú contextual.

## Conexión con el nodo perimetral

Puede conectar con el nodo perimetral mediante HTTP y SSH. Encontrará la información de punto de conexión en el [portal](#list-installed-hdinsight-apps-and-properties). Para más información sobre el uso de SSH, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

Las credenciales del punto de conexión HTTP son las credenciales de usuario HTTP que ha configurado para el clúster de HDInsight; las del punto de conexión SSH son las credenciales SSH que ha configurado para el clúster de HDInsight.

## Solución de problemas

Consulte [Solución de problemas de instalación](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## Pasos siguientes

- [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a implementar en HDInsight una aplicación de HDInsight no publicada.
- [Publicación de aplicaciones de HDInsight en Azure Marketplace](hdinsight-apps-publish-applications.md): aprenda a publicar aplicaciones de HDInsight personalizadas en Azure Marketplace.
- [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: instalación de una aplicación de HDInsight): aprenda a definir aplicaciones de HDInsight.
- [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md): aprenda a utilizar acciones de script para instalar otras aplicaciones.
- [Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): aprenda a llamar a plantillas de Resource Manager para crear clústeres de HDInsight.
- [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Utilización de nodos perimetrales vacíos en HDInsight): aprenda a usar un nodo perimetral vacío para acceder a los clústeres de HDInsight, probar aplicaciones de este y hospedarlas.

<!---HONumber=AcomDC_0914_2016-->