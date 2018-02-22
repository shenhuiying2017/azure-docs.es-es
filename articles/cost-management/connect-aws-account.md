---
title: Conectar una cuenta de Amazon Web Services a Azure Cost Management | Microsoft Docs
description: Conecte una cuenta de Amazon Web Services para ver los datos de uso y el costo en los informes de Cost Management.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/08/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: a82d803b51859f809ca5a39ce177697a1f66a008
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="connect-an-amazon-web-services-account"></a>Conectarse a una cuenta de Amazon Web Services

Tiene dos opciones para conectar su cuenta de Amazon Web Services (AWS) a Azure Cost Management. Puede conectarse con un rol de IAM o con una cuenta de usuario de IAM de solo lectura. Se recomienda usar el rol de IAM porque le permite delegar el acceso con permisos definidos a las entidades de confianza. El rol de IAM no necesita que comparta claves de acceso a largo plazo. Después de conectar una cuenta de AWS a Cost Management, los datos de uso y de costo estarán disponibles en los informes de Cost Management. Este documento le guía a través de ambas opciones.

Para obtener más información acerca de las identidades de AWS IAM, vea [Identidades (usuarios, grupos y roles)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

## <a name="aws-role-based-access"></a>Acceso basado en roles de AWS

En las siguientes secciones se le ayudará a crear un rol de IAM de solo lectura para proporcionar acceso a Cost Management.

### <a name="get-your-cost-management-account-external-id"></a>Obtención del identificador externo de la cuenta de Cost Management

El primer paso es obtener la frase de contraseña de conexión única desde el portal de Azure Cost Management. Se utiliza en AWS como **ID externo**.

1. Abra el portal de Cloudyn desde Azure Portal o vaya a [https://azure.cloudyn.com](https://azure.cloudyn.com) e inicie sesión.
2. Haga clic en **Configuración** (icono de engranaje) y, después, seleccione **Cuentas de Cloud**.
3. En Administración de cuentas, seleccione la pestaña **Cuentas de AWS** y, después, haga clic en **Agregar nuevo +**.
4. En el cuadro de diálogo **Add AWS Account** (Agregar cuenta de AWS), copie el **Id. externo** y guarde este valor para los pasos de creación del rol de AWS de la sección siguiente. En la siguiente imagen, el identificador de ejemplo es _Cloudyn_. Su identificador es diferente.  
    ![Id. externo](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Agregar acceso basado en roles y de solo lectura de AWS

1. Inicie sesión en la consola de AWS en https://console.aws.amazon.com/iam/home y seleccione **Roles**.
2. Haga clic en **Crear rol** y, después, seleccione **Another AWS account** (Otra cuenta de AWS).
3. Pegue la identidad `432263259397` en el campo **Id. de cuenta**. Este identificador de cuenta es la cuenta del recopilador de datos de Cost Management que debe usar.
4. Junto a **Opciones**, seleccione **Require external ID** (Requerir id. externo) y, después, pegue el valor que copió anteriormente en el campo **Id. externo** y haga clic en **Next: Permissions** (Siguiente: Permisos).  
    ![Crear rol](./media/connect-aws-account/create-role01.png)
5. En **Attach permissions policies** (Adjuntar directivas de permisos), en el cuadro de búsqueda de filtros **Tipo de directiva**, escriba `ReadOnlyAccess`, seleccione **ReadOnlyAccess** y, después, haga clic en **Next: Permissions** (Siguiente: Permisos).  
    ![Acceso de solo lectura](./media/connect-aws-account/readonlyaccess.png)
6. En la página Revisar, asegúrese de que las selecciones son correctas y escriba un **Nombre de rol**. Por ejemplo, *Azure-Cost-Mgt*. Escriba una **Descripción de rol**. Por ejemplo, _Role assignment for Azure Cost Management_ (Asignación de roles para Azure Cost Management) y, después, haga clic en **Crear rol**.
7. En la lista **Roles**, haga clic en el rol que haya creado y copie el valor de **ARN de rol** de la página Resumen. Utilice el valor de ARN de rol más adelante al registrar su configuración de Azure Cost Management.  
    ![ARN de rol](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Configuración del rol de AWS IAM en Cost Management

1. Abra el portal de Cloudyn desde Azure Portal o vaya a https://azure.cloudyn.com/ e inicie sesión.
2. Haga clic en **Configuración** (icono de engranaje) y, después, seleccione **Cuentas de Cloud**.
3. En Administración de cuentas, seleccione la pestaña **Cuentas de AWS** y, después, haga clic en **Agregar nuevo +**.
4. En **Nombre de cuenta**, escriba un nombre para la cuenta.
5. Junto a **Tipo de acceso**, seleccione **Rol de IAM**.
6. En el campo **ARN de rol**, pegue el valor que copió anteriormente y, después, haga clic en **Guardar**.  
    ![Estado de la cuenta de AWS](./media/connect-aws-account/aws-account-status01.png)

La cuenta de AWS aparece en la lista de cuentas. El **Id. de propietario** que aparece coincide con el valor ARN del rol. El **Estado de la cuenta** debe tener un símbolo de marca de verificación verde.

Cost Management comienza a recopilar datos y a rellenar informes. Sin embargo, algunos informes de optimización pueden requerir datos de unos días antes para generar recomendaciones precisas.

## <a name="aws-user-based-access"></a>Acceso basado en usuarios de AWS

En las siguientes secciones se le ayudará a crear un usuario de solo lectura para proporcionar acceso a Cost Management.

### <a name="add-aws-read-only-user-based-access"></a>Agregar acceso basado en usuarios y de solo lectura de AWS

1. Inicie sesión en la consola de AWS en https://console.aws.amazon.com/iam/home y seleccione **Usuarios**.
2. Haga clic en **Agregar usuario**.
3. En el campo **Nombre de usuario**, escriba un nombre de usuario.
4. Para **Tipo de acceso**, seleccione **Acceso mediante programación** y haga clic en **Next: Permissions** (Siguiente: Permisos).  
    ![Agregar usuario](./media/connect-aws-account/add-user01.png)
5. Para obtener permisos, seleccione **Attach existing policies directly** (Asociar directivas existentes directamente).
6. En **Attach permissions policies** (Adjuntar directivas de permisos), en el cuadro de búsqueda de filtros **Tipo de directiva**, escriba `ReadOnlyAccess`, seleccione **ReadOnlyAccess** y, después, haga clic en **Next: Permissions** (Siguiente: Permisos).  
    ![Selección de permisos para usuario](./media/connect-aws-account/set-permission-for-user.png)
7. En la página Revisar, asegúrese de que las selecciones son correctas y, después, haga clic en **Crear usuario**.
8. En la página Completar, se muestran el id. de clave de acceso y la clave de acceso secreta. Utilice esta información para configurar el registro en Cost Management.
9. Haga clic en **Descargar .csv** y guarde el archivo credentials.csv en una ubicación segura.  
    ![Descargar credenciales](./media/connect-aws-account/download-csv.png)


### <a name="configure-aws-iam-user-based-access-in-cost-management"></a>Configuración del acceso basado en usuarios de AWS IAM en Cost Management

1. Abra el portal de Cloudyn desde Azure Portal o vaya a https://azure.cloudyn.com/ e inicie sesión.
2. Haga clic en **Configuración** (icono de engranaje) y, después, seleccione **Cuentas de Cloud**.
3. En Administración de cuentas, seleccione la pestaña **Cuentas de AWS** y, después, haga clic en **Agregar nuevo +**.
4. Para **Nombre de cuenta**, escriba un nombre de cuenta.
5. Junto a **Tipo de acceso**, seleccione **Usuario de IAM**.
6. En **Clave de acceso**, pegue el valor de **Id. de clave de acceso** desde el archivo credentials.csv.
7. En **Clave secreta**, pegue el valor de **Clave de acceso secreta** desde el archivo credentials.csv y, después, haga clic en **Guardar**.  
    ![Estado de la cuenta de usuario de AWS](./media/connect-aws-account/aws-user-account-status.png)

La cuenta de AWS aparece en la lista de cuentas. El **Estado de la cuenta** debe tener un símbolo de marca de verificación verde.

Cost Management comienza a recopilar datos y a rellenar informes. Sin embargo, algunos informes de optimización pueden requerir datos de unos días antes para generar recomendaciones precisas.

## <a name="next-steps"></a>pasos siguientes

- Para obtener más información acerca de Azure Cost Management de Cloudyn, consulte el tutorial [Revisión del uso y los costos](tutorial-review-usage.md) de Cost Management.
