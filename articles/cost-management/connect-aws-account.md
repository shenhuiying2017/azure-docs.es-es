---
title: Conectar una cuenta de Amazon Web Services a Azure Cost Management | Microsoft Docs
description: Conecte una cuenta de Amazon Web Services para ver los datos de uso y el costo en los informes de Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 0c18fc065ae4d9a9401a8d603f051e9d6236c538
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="connect-an-amazon-web-services-account"></a>Conectarse a una cuenta de Amazon Web Services

Tiene dos opciones para conectar su cuenta de Amazon Web Services (AWS) a Azure Cost Management. Puede conectarse con un rol de IAM o con una cuenta de usuario de IAM de solo lectura. Se recomienda usar el rol de IAM porque le permite delegar el acceso con permisos definidos a las entidades de confianza. El rol de IAM no necesita que comparta claves de acceso a largo plazo. Después de conectar una cuenta de AWS a Cost Management, los datos de uso y de costo estarán disponibles en los informes de Cost Management. Este documento le guía a través de ambas opciones.

Para obtener más información acerca de las identidades de AWS IAM, vea [Identidades (usuarios, grupos y roles)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Además, habilitará los informes de facturación detallados de AWS y almacenará la información en un cubo de Simple Storage Service (S3). Los informes de facturación detallados incluyen cargos de facturación con etiqueta e información de recursos cada hora. El almacenamiento de los informes permite que Cost Management los recupere del cubo y muestre la información en sus informes.


## <a name="aws-role-based-access"></a>Acceso basado en roles de AWS

En las siguientes secciones se le ayudará a crear un rol de IAM de solo lectura para proporcionar acceso a Cost Management.

### <a name="get-your-cost-management-account-external-id"></a>Obtención del identificador externo de la cuenta de Cost Management

El primer paso es obtener la frase de contraseña de conexión única desde el portal de Azure Cost Management. Se utiliza en AWS como **ID externo**.

1. Abra el portal de Cloudyn desde Azure Portal o vaya a [https://azure.cloudyn.com](https://azure.cloudyn.com) e inicie sesión.
2. Haga clic en el símbolo de engranaje y, después, seleccione **Cuentas de Cloud**.
3. En Administración de cuentas, seleccione la pestaña **Cuentas de AWS** y, después, haga clic en **Agregar nuevo +**.
4. En el cuadro de diálogo **Add AWS Account** (Añadir cuenta de AWS), copie el **identificador externo** y guarde el valor para los pasos de creación del rol de AWS de la sección siguiente. El identificador externo es exclusivo de su cuenta. En la siguiente imagen, el identificador externo de ejemplo es _Contoso_ seguido de un número. Su identificador es diferente.  
    ![Id. externo](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Agregar acceso basado en roles y de solo lectura de AWS

1. Inicie sesión en la consola de AWS en https://console.aws.amazon.com/iam/home y seleccione **Roles**.
2. Haga clic en **Crear rol** y, después, seleccione **Another AWS account** (Otra cuenta de AWS).
3. En el cuadro **Id. de cuenta**, pegue `432263259397`. Este identificador de cuenta es la cuenta del recopilador de datos de Cost Management asignada por AWS al servicio de Cloudyn. Utilice el mismo identificador de cuenta que se muestra.
4. Junto a **Opciones**, seleccione **Require external ID** (Requerir id. externo). Pegue el valor exclusivo que copió anteriormente del campo **Id. externo** en Cost Management. A continuación, haga clic en **Next: Permissions** (Siguiente: permisos).  
    ![Crear rol](./media/connect-aws-account/create-role01.png)
5. En **Attach permissions policies** (Adjuntar directivas de permisos), en el cuadro de búsqueda de filtros **Tipo de directiva**, escriba `ReadOnlyAccess`, seleccione **ReadOnlyAccess** y, después, haga clic en **Next: Permissions** (Siguiente: Permisos).  
    ![Acceso de solo lectura](./media/connect-aws-account/readonlyaccess.png)
6. En la página Revisar, asegúrese de que las selecciones son correctas y escriba un **Nombre de rol**. Por ejemplo, *Azure-Cost-Mgt*. Escriba una **Descripción de rol**. Por ejemplo, _Role assignment for Azure Cost Management_ (Asignación de roles para Azure Cost Management) y, después, haga clic en **Crear rol**.
7. En la lista **Roles**, haga clic en el rol que haya creado y copie el valor de **ARN de rol** de la página Resumen. Utilice el valor de ARN (nombre de recursos de Amazon) de rol más adelante al registrar su configuración en Azure Cost Management.  
    ![ARN de rol](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Configuración del rol de AWS IAM en Cost Management

1. Abra el portal de Cloudyn desde Azure Portal o vaya a https://azure.cloudyn.com/ e inicie sesión.
2. Haga clic en el símbolo de engranaje y, después, seleccione **Cuentas de Cloud**.
3. En Administración de cuentas, seleccione la pestaña **Cuentas de AWS** y, después, haga clic en **Agregar nuevo +**.
4. En **Nombre de cuenta**, escriba un nombre para la cuenta.
5. Junto a **Tipo de acceso**, seleccione **Rol de IAM**.
6. En el campo **ARN de rol**, pegue el valor que copió anteriormente y, después, haga clic en **Guardar**.  
    ![Cuadro Add AWS Account (Añadir cuenta de AWS)](./media/connect-aws-account/add-aws-account-box.png)


La cuenta de AWS aparece en la lista de cuentas. El **Id. de propietario** que aparece coincide con el valor ARN del rol. Su **estado de cuenta** debe tener una marca de verificación verde que indica que Cost Management puede acceder a su cuenta de AWS. Hasta que habilite la facturación de AWS detallada, el estado de consolidación será **independiente**.

![Estado de la cuenta de AWS](./media/connect-aws-account/aws-account-status01.png)

Cost Management comienza a recopilar datos y a rellenar informes. A continuación, [habilite la facturación de AWS detallada](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Acceso basado en usuarios de AWS

En las siguientes secciones se le ayudará a crear un usuario de solo lectura para proporcionar acceso a Cost Management.

### <a name="add-aws-read-only-user-based-access"></a>Agregar acceso basado en usuarios y de solo lectura de AWS

1. Inicie sesión en la consola de AWS en https://console.aws.amazon.com/iam/home y seleccione **Users** (Usuarios).
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

1. Abra el portal de Cloudyn desde Azure Portal o navegue a https://azure.cloudyn.com/ e inicie sesión.
2. Haga clic en el símbolo de engranaje y, después, seleccione **Cuentas de Cloud**.
3. En Administración de cuentas, seleccione la pestaña **Cuentas de AWS** y, después, haga clic en **Agregar nuevo +**.
4. Para **Nombre de cuenta**, escriba un nombre de cuenta.
5. Junto a **Tipo de acceso**, seleccione **Usuario de IAM**.
6. En **Clave de acceso**, pegue el valor de **Id. de clave de acceso** desde el archivo credentials.csv.
7. En **Clave secreta**, pegue el valor de **Clave de acceso secreta** desde el archivo credentials.csv y, después, haga clic en **Guardar**.  

La cuenta de AWS aparece en la lista de cuentas. El **Estado de la cuenta** debe tener un símbolo de marca de verificación verde.

Cost Management comienza a recopilar datos y a rellenar informes. A continuación, [habilite la facturación de AWS detallada](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Habilitación de la facturación de AWS detallada

Siga los pasos siguientes para obtener su ARN de rol de AWS. El ARN de rol se usa para conceder permisos de lectura a un cubo de facturación.

1. Inicie sesión en la consola de AWS en https://console.aws.amazon.com y seleccione **Services** (Servicios).
2. En el cuadro de búsqueda del servicio, escriba *IAM* y seleccione esa opción.
3. Seleccione **Roles** en el menú izquierdo.
4. En la lista de roles, seleccione el rol que ha creado para el acceso de Cloudyn.
5. En la página de resumen de roles, haga clic para copiar el **ARN de rol**. Conserve el ARN de rol para los pasos posteriores.

### <a name="create-an-s3-bucket"></a>Creación de un cubo de S3

Un cubo de S3 se crea para almacenar información de facturación detallada.

1. Inicie sesión en la consola de AWS en https://console.aws.amazon.com y seleccione **Services** (Servicios).
2. En el cuadro de búsqueda del servicio, escriba *S3* y seleccione **S3**.
3. En la página de Amazon S3, haga clic en **Create bucket** (Crear cubo).
4. En el asistente de creación de cubos, elija un nombre y una región para el cubo y, a continuación, haga clic en **Next** (Siguiente).  
    ![Creación de cubo](./media/connect-aws-account/create-bucket.png)
5. En la página **Set properties** (Establecer propiedades), mantenga los valores predeterminados y, a continuación, haga clic en **Next** (Siguiente).
6. En la página Review (Revisar), haga clic en **Create bucket** (Crear cubo). Se mostrará la lista de cubos.
7. Haga clic en el cubo que ha creado y seleccione la pestaña **Permissions** (Permisos) y, a continuación, seleccione **Bucket Policy** (Directiva de cubos). Se abrirá el editor de directivas de cubos.
8. Copie el siguiente JSON de ejemplo y péguelo en el editor de directivas de cubos.
  - Reemplace `<BillingBucketName>` por el nombre del cubo S3.
  - Reemplace `<ReadOnlyUserOrRole>` por el ARN de rol o usuario que ha copiado anteriormente.

  ```
  {
    "Version": "2012-10-17",
    "Id": "Policy1426774604000",
    "Statement": [
        {
            "Sid": "Stmt1426774604000",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>"
        },
        {
            "Sid": "Stmt1426774604001",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        },
        {
            "Sid": "Stmt1426774604002",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<ReadOnlyUserOrRole>"
            },
            "Action": [
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        }
    ]
  }
  ```

9. Haga clic en **Save**(Guardar).  
    ![Editor de directivas de cubos](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Habilitación de informes de facturación de AWS

Después de crear y configurar el cubo de S3, vaya a [Billing Preferences](https://console.aws.amazon.com/billing/home?#/preference) (Preferencias de facturación) en la consola de AWS.

1. En la página de preferencias, seleccione **Receive Billing Reports** (Recibir informes de facturación).
2. En **Receive Billing Reports** (Recibir informes de facturación), escriba el nombre del cubo que ha creado y, a continuación, haga clic en **Verify** (Comprobar).  
3. Seleccione las cuatro opciones de granularidad de informe y, a continuación, haga clic en **Save preferences** (Guardar preferencias).  
    ![Habilitación de informes](./media/connect-aws-account/enable-reports.png)

Cost Management recupera información de facturación detallada del cubo de S3 y rellena informes después de que se habilite la facturación detallada. Pueden pasar hasta 24 horas hasta que aparezcan datos de facturación detallada en la consola de Cloudyn. Cuando hay datos de facturación detallada disponibles, el estado de consolidación de su cuenta se muestra como **Consolidado**. El estado de la cuenta se muestra como **Completado**.

![Estado de cuenta consolidado](./media/connect-aws-account/consolidated-status.png)

Algunos de los informes de optimización pueden requerir datos de varios días para obtener un tamaño de muestra de datos adecuado para ofrecer recomendaciones precisas.

## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca de Azure Cost Management, consulte el tutorial [Revisión del uso y los costos](tutorial-review-usage.md) de Cost Management.
