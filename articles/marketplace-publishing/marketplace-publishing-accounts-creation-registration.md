---
title: "Creación y registro de la cuenta de publicador | Microsoft Docs"
description: "Instrucciones para crear una cuenta de desarrollador de Microsoft de manera que, tras su aprobación, se puedan vender varios tipos de productos en Azure Marketplace."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5a2fe68d-2967-463f-8af6-42bed07e3eaa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: hascipio
ms.openlocfilehash: 642e4a2d11ef5a92f5ab46bc4872414966b04c0d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-microsoft-developer-account"></a>Crear una cuenta de desarrollador de Microsoft
Este artículo le guía a través del proceso necesario de creación y registro de cuentas para convertirse en un desarrollador de Microsoft aprobado para Azure Marketplace.

## <a name="1-create-a-microsoft-account"></a>1. Creación de una cuenta Microsoft
Para finalizar el proceso de publicación, deberá crear una cuenta Microsoft. Esta cuenta se utilizará para registrarse en el **Centro para desarrolladores de Microsoft** y el **Portal de publicación de Azure**. Solo debe tener una cuenta Microsoft para sus ofertas de Azure Marketplace. Esta no debe ser específica para servicios u ofertas.

La dirección que constituye el nombre de usuario debe estar en su dominio y estar controlada por su equipo de TI. Todas las actividades relacionadas con la publicación deben realizarse a través de esta cuenta.

> [!WARNING]
> No se admiten palabras como "**Azure**" y "**Microsoft**" en el registro de la cuenta Microsoft. Evite el uso de estas palabras para completar el proceso de creación y registro de cuentas.
>
>

### <a name="guidelines-for-company-accounts"></a>Instrucciones para las cuentas de empresa
Cuando cree una cuenta de empresa, siga estas instrucciones si más de una persona necesitará acceso a la cuenta con el inicio de sesión con la cuenta de Microsoft que abrió la cuenta.

> [!Important]
> Importante Para permitir que varios usuarios tengan acceso a la cuenta del Centro de desarrollo, se recomienda usar Azure Active Directory para asignar roles a usuarios individuales, que pueden obtener acceso a la cuenta si inician sesión con sus credenciales individuales de Azure AD. Para más información, consulte [Administrar usuarios de cuentas](https://msdn.microsoft.com/en-us/windows/uwp/publish/manage-account-users).

* Cree su cuenta de Microsoft con una dirección de correo electrónico que pertenezca al dominio de la empresa, pero no a un usuario individual; por ejemplo, windowsapps@fabrikam.com.
* Limite el acceso a esta cuenta de Microsoft a la menor cantidad de desarrolladores posible.
* Configure una lista de distribución de correo electrónico corporativo que incluya a todos los usuarios que necesiten tener acceso a la cuenta de desarrollador y agregue esta dirección de correo electrónico a la información de seguridad. Esto permite que todos los empleados de la lista reciban códigos de seguridad cuando sea necesario y administren la información de seguridad de la cuenta de Microsoft. Si no es posible configurar una lista de distribución, el propietario de la cuenta de correo electrónico individual deberá permitir el acceso y el uso compartido del código de seguridad cuando se le solicite (por ejemplo, cuando se agregue información de seguridad nueva a la cuenta o cuando se deba obtener acceso a ella desde un dispositivo nuevo).
* Agregue un número de teléfono de la empresa que no necesite extensión y al que puedan tener acceso miembros clave del equipo.
* En general, los desarrolladores deben usar dispositivos de confianza para iniciar sesión en la cuenta de desarrollador de la empresa. Todos los miembros clave del equipo deben tener acceso a estos dispositivos de confianza. Esto disminuirá la necesidad de enviar códigos de seguridad cuando se obtiene acceso a la cuenta.
* Si desea permitir el acceso a la cuenta desde un equipo que no es de confianza, limite el acceso a un máximo de cinco desarrolladores. Idealmente, estos desarrolladores deben obtener acceso a la cuenta desde máquinas que compartan la misma ubicación geográfica y de la red.
* Revise con frecuencia la información de seguridad de su empresa en [https://account.live.com/proofs/Manage](https://account.live.com/proofs/Manage) para asegurarse de que está actualizada.

Se debe tener acceso a la cuenta de desarrollador principalmente desde equipos de confianza. Esto resulta fundamental porque la cantidad de códigos generados por cuenta a la semana es limitada. También permite lograr la experiencia de inicio de sesión más óptima.

Para más información sobre seguridad e instrucciones adicionales para la cuenta de desarrollador, haga clic [aquí](https://msdn.microsoft.com/en-us/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="instructions"></a>Instrucciones
1. Abra una nueva sesión de exploración de incógnito en Chrome o de InPrivate en Internet Explorer para asegurarse de que no tiene una sesión iniciada en una cuenta existente.
2. Registre el correo electrónico (según las instrucciones anteriores, por ejemplo windowsapp@fabrikam.com) como una cuenta de Microsoft mediante el vínculo [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx). Siga las instrucciones que se describen a continuación.

   1. Durante el registro de la cuenta como cuenta de Microsoft, debe proporcionar un número de teléfono válido para que el sistema pueda enviarle un código de comprobación de la cuenta a través de un mensaje de texto o una llamada automatizada.
   2. También, debe proporcionar un identificador de correo electrónico válido para recibir un correo electrónico automatizado de comprobación de la cuenta.
3. Compruebe la dirección de correo electrónico enviada a la lista de distribución.
4. Ahora está listo para usar la nueva cuenta Microsoft en el Centro para desarrolladores de Microsoft.

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2. Registre la cuenta en el Centro para desarrolladores de Microsoft
El Centro para desarrolladores de Microsoft se usa para registrar la información de la empresa una sola vez. El usuario inscrito debe ser un representante válido de la compañía y debe proporcionar su información personal, como una forma de validar su identidad. La persona que se inscriba debe usar una cuenta Microsoft que se comparta en la compañía, **y esa misma cuenta debe usarse en el Portal de publicación de Azure.** Debe asegurarse de que su compañía no tenga ya una cuenta del Centro para desarrolladores de Microsoft antes de intentar crear una nueva. Durante el proceso, recopilaremos la información de dirección, los datos de la cuenta bancaria y la información fiscal de la compañía. Normalmente, estos datos se pueden obtener de contactos financieros o comerciales.

> [!IMPORTANT]
> Debe completar los siguientes componentes del perfil de desarrollador para avanzar por las distintas fases de la creación e implementación de ofertas.
>
>

| Perfil del desarrollador | Para iniciar el borrador | Ensayo | Publicación gratis y plantilla de solución | Publicación de ofertas comerciales |
| --- | --- | --- | --- | --- |
| Registro de la compañía |Obligatorio |Obligatorio |Obligatorio |Obligatorio |
| Identificación de perfil fiscal |Opcional |Opcional |Opcional |Obligatorio |
| Cuenta bancaria |Opcional |Opcional |Opcional |Obligatorio |

> [!NOTE]
> El sistema "traiga su propia licencia" (BYOL) solo se admite para las máquinas virtuales y se considera una oferta **gratuita** .
>
>

### <a name="register-your-company-account"></a>Registro de su cuenta de compañía
1. Abra una nueva sesión de exploración de InPrivate en Internet Explorer o de incógnito en Chrome para asegurarse de que no tiene una sesión iniciada en una cuenta personal.
2. Vaya a [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure) para registrarse como vendedor en el Centro de desarrollo. Lea la siguiente nota importante antes de continuar.

   > [!IMPORTANT]
   > Asegúrese de que el identificador de correo electrónico o la DL (se recomienda usar una lista de distribución para no tener que depender de otras personas) que va a utilizar para registrarse en el Centro de desarrollo es una cuenta Microsoft. En caso contrario, regístrela haciendo clic en este [vínculo](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). Además, **no se puede utilizar cualquier identificador de correo electrónico en el dominio de empresa de Microsoft**, es decir, @microsoft.com para registrarse en el Centro de desarrollo.
   >
   >

    ![dibujo][img-signin]
3. Complete el asistente “Ayúdenos a proteger su cuenta”, que comprobará su identidad por teléfono o por correo electrónico.

    ![dibujo][img-verify]
4. En la sección de registro e información de la cuenta, seleccione su **país o región** en el menú desplegable.

    ![dibujo](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **Países de "origen de venta":** para vender sus servicios en Azure Marketplace, la entidad registrada debe proceder de uno de los países de "origen de venta" aprobados que se mencionan arriba. Esta restricción se debe a motivos de pago e impuestos. Trabajamos activamente para ampliar esta lista de países en un futuro próximo, así pues, manténgase atento. Para obtener más información, consulte las [directivas de participación de Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).
   >
   >
5. Seleccione **Compañía** como tipo de cuenta y, después, haga clic en el botón **Siguiente**.

   > [!IMPORTANT]
   > Para comprender mejor los tipos de cuenta y cuál es la mejor para sus necesidades, consulte la página [Tipos de cuenta, ubicaciones y precios](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

    ![dibujo](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)
6. Escriba el **nombre para mostrar del publicador**, que suele ser el nombre de su compañía.

   > [!TIP]
   > El nombre para mostrar del publicador especificado en el Centro de desarrollo no se muestra en Azure Marketplace cuando la oferta esté activa. Sin embargo, se debe rellenar para completar el proceso de registro.
   >
   >
7. Escriba la **información de contacto** para que se compruebe la cuenta.

   > [!IMPORTANT]
   > La información proporcionada debe ser precisa ya que se usará en nuestro proceso de comprobación y aprobación de la compañía en el Centro para desarrolladores.
   >
   >
8. Escriba la información de contacto del **aprobador de la compañía**. El aprobador de la compañía es la persona que puede comprobar que está autorizado para crear una cuenta en el Centro de desarrollo en nombre de su organización. Cuando haya terminado, haga clic en **Siguiente** para pasar a la **sección de pago**.

    ![dibujo](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)
9. Escriba la información de pago para pagar su cuenta. Si tiene un código de promoción que cubre el costo de registro, puede indicarlo aquí. De lo contrario, proporcione su información de tarjeta de crédito (o PayPal en mercados admitidos). Cuando haya terminado, haga clic en **Siguiente** para pasar a la **"Pantalla de revisión"**.

    ![dibujo](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)
10. Revise la información de la cuenta y confirme que todo es correcto. A continuación, lea y acepte los términos y condiciones del [Contrato del publicador de Microsoft Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560). Active la casilla para indicar que ha leído y aceptado estos términos.
11. Haga clic en **Finalizar** para confirmar el registro. Le enviaremos un mensaje de confirmación a su dirección de correo electrónico.
12. Si solo tiene pensado publicar ofertas gratuitas, haga clic en **Ir al Portal de publicación de Azure Marketplace** y vaya directamente a la sección 3 de este documento, [Registro de la cuenta en el portal de publicación](#3-register-your-account-in-the-publishing-portal).

Si piensa publicar ofertas comerciales (por ejemplo, ofertas de máquinas virtuales con el modelo de facturación por hora), haga clic en **Actualizar la información de la cuenta** , donde debe completar la información fiscal y bancaria de su cuenta del Centro para desarrolladores.

Si prefiere actualizar la información fiscal y bancaria en otro momento, puede ir a la sección 3 de este documento, [Registro de la cuenta en el portal de publicación](#3-register-your-account-in-the-publishing-portal), y volver más tarde mediante los vínculos del Portal de publicación de Azure.

> [!IMPORTANT]
> En el caso de las ofertas comerciales, no podrá llevarlas a producción sin completar la información fiscal y bancaria.
>
>

Si prefiere actualizar la información fiscal y bancaria en otro momento, puede ir a la sección 3, [Registro de la cuenta en el portal de publicación](#3-register-your-account-in-the-publishing-portal), y volver más tarde mediante los vínculos del Portal de publicación de Azure.

### <a name="add-tax-and-banking-information"></a>Adición de información bancaria y fiscal
 Si desea publicar ofertas comerciales de compra, también necesitará agregar información de pago y fiscal y enviarla para su validación en el Centro para desarrolladores. Si solo va a publicar ofertas gratis (u ofertas BYOL), no necesita agregar esta información. Puede agregarla más adelante, pero se tarda algún tiempo en validar la información fiscal. Si sabe que ofrecerá ofertas comerciales de compra, se recomienda que las agregue tan pronto como sea posible.

**Información bancaria**

1. Inicie sesión en el [Cntro para desarrolladores de Microsoft](http://dev.windows.com/registration?accountprogram=azure) con su cuenta Microsoft.
2. Haga clic en **Cuenta de pago** en el menú de la izquierda y, en **Elegir método de pago**, haga clic en **Cuenta bancaria** o **PayPal**.

   > [!IMPORTANT]
   > Si tiene ofertas comerciales que compran los clientes en Marketplace, esta es la cuenta donde recibirá el pago para esas compras.
   >
   >
3. Escriba la información de pago y, cuando esté satisfecho, haga clic en **Guardar** .

   > [!IMPORTANT]
   > Si necesita actualizar o cambiar la cuenta de pago, siga estos mismos pasos, pero sustituya la información actual por la nueva. El hecho de cambiar la cuenta de pago puede dar lugar a retrasos en los pagos de hasta un ciclo completo. Este retraso se produce porque es necesario comprobar el cambio de la cuenta, al igual que hicimos cuando configuró por primera vez la cuenta de pago. Aunque se le seguirá pagando el importe completo después de que se haya comprobado la cuenta, los pagos pendientes del ciclo actual se agregarán al siguiente.
   >
   >
4. Haga clic en **Siguiente**.

**Información fiscal**

1. Inicie sesión en el [Centro para desarrolladores de Microsoft](http://dev.windows.com/registration?accountprogram=azure) con su cuenta Microsoft (si es necesario).
2. Haga clic en **Perfil fiscal** en el menú izquierdo.
3. En la página **Configure su formulario fiscal** , seleccione el país o la región donde tenga la residencia permanente y después seleccione el país o la región donde tiene la nacionalidad principal. Haga clic en **Siguiente**.
4. Escriba la información fiscal y, después, haga clic en **Siguiente**.

> [!WARNING]
> No podrá llevar a producción las ofertas comerciales sin completar la información fiscal y bancaria de su cuenta del Centro para desarrolladores de Microsoft.
>
>

Si tiene problemas para registrarse en el Centro para desarrolladores, abra una incidencia de soporte técnico tal y como se indica a continuación.

1. Vaya al vínculo de soporte técnico [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. En la sección **Ponte en contacto con nosotros**, haga clic en el botón **Enviar un incidente** (tal y como se muestra en la siguiente captura de pantalla).

    ![dibujo](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)
3. Elija Help with Dev Center (Ayuda con el Centro de desarrollo) como **tipo de problema**, y Publish and manage apps (Publicar y administrar aplicaciones) como **categoría**. Después, haga clic en el botón Start email (Iniciar correo electrónico).

    ![dibujo](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)
4. Se le redireccionará a una página de inicio de sesión. Utilice cualquier cuenta Microsoft para iniciar sesión. Si no tiene una, puede crearla si hace clic en este [vínculo](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Complete los detalles del problema y envíe la incidencia haciendo clic en el **Enviar** botón.

    ![dibujo](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3. Registro de su cuenta en el portal de publicación
El [Portal de publicación](http://publish.windowsazure.com) se usa para publicar y administrar ofertas.

1. Abra una nueva sesión de exploración de incógnito en Chrome o de InPrivate en Internet Explorer para asegurarse de que no tiene una sesión iniciada en una cuenta personal.
2. Vaya a [http://publish.windowsazure.com](http://publish.windowsazure.com).
3. Si es un nuevo usuario y va a iniciar sesión por primera vez en el Portal de publicación, tendrá que hacerlo con el mismo identificador de correo electrónico con el que registró la cuenta del Centro de desarrollo. De esta manera, se vincularán entre sí la cuenta del Centro de desarrollo y la del Portal de publicación. Más adelante, podrá agregar como coadministradores en el Portal de publicación al resto de los miembros de la compañía que estén utilizando la aplicación siguiendo estos pasos.

Si a usted se le agregó como coadministrador en el Portal de publicación, podrá iniciar sesión con su cuenta de coadministrador.

> [!TIP]
> Las directivas de participación se describen en el [sitio web de Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
>
>

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4. Pasos para agregar coadministradores en el Portal de publicación
**Si damos por hecho que usted es el administrador** , siga estos pasos para agregar un coadministrador.

> [!NOTE]
> **Para los nuevos usuarios,** antes de agregar un coadministrador en el Portal de publicación, asegúrese de que ha creado, al menos, una aplicación en el Portal de publicación. Esto es necesario porque la pestaña **PUBLICADORES** solo se muestra después de crear, como mínimo, una aplicación en el Portal de publicación.
>
>

1. Asegúrese de que el identificador de correo electrónico de coadministrador sea una cuenta Microsoft (MSA). Si no es así, regístrela como MSA haciendo clic en este [vínculo](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Asegúrese de que hay, al menos, una aplicación en la cuenta de administrador antes de intentar agregar un coadministrador.
3. Cuando realice los pasos anteriores, inicie sesión en el Portal de publicación con el identificador de correo electrónico de coadministrador y, luego, cierre de sesión.
4. Ahora, inicie sesión en el Portal de publicación con el identificador de correo electrónico de administrador.
5. Vaya a Publicadores -> seleccione la cuenta -> Administradores -> Agregar nuevo coadministrador (consulte la captura de pantalla siguiente).

   ![dibujo](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5. Pasos para eliminar a un coadministrador en el portal de publicación
**Dando por hecho que usted es el administrador** , siga estos pasos para eliminar a un coadministrador.

1. Inicie sesión en el portal de publicación con el identificador de correo electrónico de administrador.
2. Vaya a **Publicadores** -> seleccione su cuenta -> **Administradores** -> **Co-Admins** (Coadministradores).
3. Haga clic en el botón **X** situado junto al coadministrador que desee eliminar (consulte la captura de pantalla siguiente).

    ![dibujo](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ya se creó y se registró la cuenta, consulte los [requisitos previos no técnicos](marketplace-publishing-pre-requisites.md)para asegurarse de cumplir o satisfacer todos los requisitos previos no técnicos para publicar su oferta.

## <a name="see-also"></a>Consulte también
* [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md
