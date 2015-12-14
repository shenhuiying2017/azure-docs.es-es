<properties
   pageTitle="Creación y registro de la cuenta de publicador | Microsoft Azure"
   description="Instrucciones para crear una cuenta de vendedor de Microsoft de manera que, tras su aprobación, se puedan vender varios tipos de productos en Azure Marketplace."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/17/2015"
   ms.author="hascipio"/>

# Creación de una cuenta de vendedor de Microsoft
Este artículo le guía a través del proceso necesario de creación y registro de cuentas para convertirse en un vendedor de Microsoft aprobado para Azure Marketplace.

## 1\. Creación de una cuenta Microsoft
> [AZURE.WARNING]Para finalizar el proceso de publicación, debe crear una cuenta Microsoft. Esta cuenta se usará para registrarse e iniciar sesión tanto en el portal de publicación como en el Panel de vendedores. Solo debe tener una cuenta Microsoft para sus ofertas de Azure Marketplace. Esta no debe ser específica para servicios u ofertas.

La dirección que constituye el nombre de usuario debe estar en su dominio y ser controlada por su equipo de TI (por ejemplo, publishing@example.com)). Los pagos, la información fiscal y los informes se tramitarán con esta cuenta.

  >[AZURE.WARNING]No se admiten palabras como "Azure" y "Microsoft" en el registro de la cuenta Microsoft. Evite el uso de estas palabras para completar el proceso de creación y registro de cuentas.

1. Cree una lista de distribución (DL) o un grupo de seguridad (SG) dentro del dominio de la compañía.
  - Agregue el equipo de incorporación a la lista de distribución.
  - La lista de distribución debe estar activa para recibir un correo electrónico de confirmación.
  - Use marketplace@example.com como dirección de correo electrónico de la lista de distribución.
  - Se debe completar en los sistemas internos.
2. Abra una nueva sesión de exploración de incógnito en Chrome o de InPrivate en Internet Explorer para asegurarse de que no tiene una sesión iniciada en una cuenta existente.
3. Regístrese para obtener una cuenta Microsoft usando el correo electrónico de la lista de distribución.
 - Puede registrarse para obtener una cuenta Microsoft en [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx).
 - Use marketplace@example.com como la dirección de correo electrónico.
 - Su identificador de la cuenta Microsoft es ahora marketplace@example.com.

    <!--[drawing][img-msalive]-->

4. Use un número de teléfono válido para el registro. El sistema le enviará un código de verificación en un mensaje de texto o realizará una llamada automática si es necesario verificar la identidad.
5. Compruebe la dirección de correo electrónico enviada a la lista de distribución.

    <!--[drawing][img-email]-->

6. Ahora está listo para usar la nueva cuenta Microsoft en el Panel de vendedores.

> [AZURE.IMPORTANT]El uso de la lista de distribución permite que varias personas reciban notificaciones de correo electrónico que son importantes para la información sobre los pagos. También garantiza que la propiedad de la cuenta Microsoft se pueda transferir y no esté vinculada a un único usuario.

## 2\. Creación de su cuenta en el Panel de vendedores
El Panel de vendedores de Microsoft se usa para registrar la información de la empresa una vez. El usuario inscrito debe ser un representante válido de la compañía y debe proporcionar su información personal, como una forma de validar su identidad. La persona que se inscribe debe usar una cuenta Microsoft que se comparta para la compañía, y esa misma cuenta debe usarse en el portal de publicación de Azure. Debe asegurarse de que su compañía no tenga ya una cuenta en el Panel de vendedores antes de intentar crear una nueva. Durante el proceso, recopilaremos los datos de su cuenta bancaria, su información fiscal y la dirección de la compañía. Normalmente, estos datos se pueden obtener de contactos financieros o comerciales.

> [AZURE.IMPORTANT]Debe completar los componentes del perfil de vendedor siguientes para avanzar por las distintas fases de la creación e implementación de la oferta.


| Perfil de vendedor | Para iniciar el borrador | Ensayo | Publicación gratis y plantilla de solución | Publicación de ofertas comerciales |
|----|----|----|----|----|
|Registro de la compañía | Obligatorio | Obligatorio | Obligatorio | Obligatorio |
|Identificación de perfil fiscal | Opcional | Opcional | Opcional | Obligatorio |
|Cuenta bancaria | Opcional | Opcional | Opcional | Obligatorio |


> [AZURE.NOTE]El sistema "traiga su propia licencia" (BYOL) solo se admite para las máquinas virtuales y se considera una oferta **gratuita**.


### Registro de su cuenta de compañía
1. Abra una nueva sesión de exploración de InPrivate en Internet Explorer o de incógnito en Chrome para asegurarse de que no tiene una sesión iniciada en una cuenta personal.

2. Vaya a [http://sellerdashboard.microsoft.com](http://sellerdashboard.microsoft.com).

    ![dibujo][img-sd-url]

3. Inicie sesión con su cuenta Microsoft de registro de la empresa (por ejemplo, marketplace@example.com))

    ![dibujo][img-signin]

4. Complete el asistente “Ayúdenos a proteger su cuenta”, que comprobará su identidad por teléfono o por correo electrónico.

    ![dibujo][img-verify]

5. Vaya a **Detalles de la cuenta**. En esta pantalla, debe proporcionar su información personal, que solo se usa para verificar su identidad. Se trata de su nombre, dirección de correo electrónico, domicilio y número de teléfono privados. Esta información no se comparte con nadie fuera de Microsoft.

    ![dibujo][img-sd-top]

    <!--[drawing][img-sd-info]-->

6. Regístrese en nombre de su empresa mediante la designación de su tipo de cuenta como **Compañía**, No como cuenta **Individual**. Haga clic en **Siguiente**.

    ![dibujo][img-sd-type]

7. Rellene los datos de la compañía. Debe ser una información precisa: la sede central y las referencias se comprobarán por un equipo de Microsoft.

    ![dibujo][img-sd-mktg1]

8. El nombre de la compañía se utiliza por el portal de publicación, por lo que debe ser preciso.

    <!--[drawing][img-sd-mktg2]-->

9. Use la dirección de la sede central de la compañía.

    ![dibujo][img-sd-addr]

10. Use referencias que sean accesibles y reconocibles, como un representante de la compañía.

    ![dibujo][img-sd-legal]

11. Haga clic en **Enviar para aprobación** y ya habrá finalizado.

    ![dibujo][img-sd-submit]


Si solo piensa publicar ofertas gratuitas, vaya a la sección 3, "Registro de su cuenta en el portal de publicación".

Si piensa publicar ofertas comerciales, debe completar la información fiscal y bancaria de su cuenta de Panel de vendedores.

> [AZURE.IMPORTANT]No podrá probar correctamente las ofertas en el entorno de ensayo ni podrá llevar sus ofertas a producción sin completar la información fiscal y de la cuenta bancaria.

Si prefiere actualizar la información fiscal y de la cuenta bancaria posteriormente, vaya a la sección 3, "Registro de su cuenta en el portal de publicación", y vuelva más tarde mediante los vínculos del portal de publicación.

### Adición de información bancaria y fiscal
 Si desea publicar ofertas comerciales de compra, también necesitará agregar información del pago y fiscal y enviarla para su validación en el Panel de vendedores. Si solo va a publicar ofertas gratis (u ofertas BYOL), no necesita agregar esta información. Puede agregarla más adelante, pero se tarda algún tiempo en validar la información fiscal. Si sabe que ofrecerá ofertas comerciales de compra, se recomienda que las agregue tan pronto como sea posible.

1. Inicie sesión en el [Panel de vendedores](http://sellerdashboard.microsoft.com) con su cuenta Microsoft.

2. Haga clic en la pestaña **CUENTA** y, después, haga clic en **Pago e impuestos**.

3. Haga clic en **AGREGAR INFORMACIÓN DE PAGO E IMPUESTOS**.

4. En la página **Elija un método de pago**, en **Nuevo método de pago**, haga clic en **Cuenta bancaria** o **PayPal**.

> [AZURE.IMPORTANT]Si tiene ofertas comerciales que compran los clientes en Marketplace, esta es la cuenta donde recibirá el pago para esas compras.

5. Especifique los datos de la cuenta bancaria o de una cuenta PayPal.

6. Haga clic en **SIGUIENTE**.

7. En la página **Información sobre impuestos**, seleccione el país o región donde tenga la residencia permanente y después seleccione el país o región de donde ostenta la nacionalidad principal. Haga clic en **SIGUIENTE**.

8. Escriba la información fiscal y, después, haga clic en **SIGUIENTE**.

9. Haga clic en **Enviar**. Si todavía no está preparado para enviar la información fiscal para su validación, haga clic en **Guardar** o en **Guardar y salir**. Se tarda algún tiempo en validar la información fiscal, por lo que recomendamos que la envíe para su validación lo antes posible.

> [AZURE.WARNING]No podrá llevar a producción las ofertas comerciales sin completar la información fiscal y de cuenta bancaria en su cuenta de Panel de vendedores.

## 3\. Registro de su cuenta en el portal de publicación
El portal de publicación de Azure se usa para publicar y administrar sus ofertas. Encontrará información útil en el portal de publicación que le guiará por todo el proceso de creación de ofertas.

> [AZURE.WARNING]Debe usar aquí la misma cuenta de Microsoft de compañía que se usó para el registro en el Panel de vendedores. Se pueden agregar usuarios adicionales para ayudar una vez creada la cuenta del anunciante principal.

1.	Abra una nueva sesión de exploración de incógnito en Chrome o de InPrivate en Internet Explorer para asegurarse de que no tiene una sesión iniciada en una cuenta personal.
2.	Vaya a [http://publish.windowsazure.com](http://publish.windowsazure.com).
3.	 Inicie sesión con su cuenta Microsoft de registro de la compañía (es decir, marketplace@example.com)) y, si es necesario, agregue coadministradores.
4.	Lea y acepte los términos del contrato de publicador (la primera vez que inicia sesión en el portal de publicación) y ya habrá terminado aquí.

  >[AZURE.TIP]Las directivas de participación se describen en el [sitio web de Azure](http://azure.microsoft.com/support/legal/marketplace/participation-policies/).

  > Si tiene problemas para completar el registro como vendedor, abra una incidencia de soporte técnico tal como se indica a continuación: 1. Póngase en contacto con [Soporte técnico](http://go.microsoft.com/fwlink?LinkId=272975). 2. Elija **Registro del Panel de vendedores y su cuenta**. 3. Elija **Registrarse para obtener una cuenta de desarrollador**. 4. Elija un método de contacto.

### Países de "Venta-de"

> [AZURE.WARNING]Para vender sus servicios en Azure Marketplace, es necesario que la entidad registrada proceda de uno de los países desde donde está permitida la venta. Esta restricción se debe a motivos de pago e impuestos. Trabajamos activamente para ampliar esta lista de países en un futuro próximo, así pues, manténgase atento. Para obtener la lista completa, vea la sección 1b de la página [Directivas de participación de Microsoft Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).




## Pasos siguientes
Ahora que ya se creó y se registró la cuenta, haga clic en el tipo de artefacto (máquina virtual, servicio de programador, servicio de datos, plantilla de soluciones) que desea publicar en Azure Marketplace. Visite uno de los artículos siguientes para obtener más información sobre cómo publicar su oferta respectiva:

|| Imagen de máquina virtual | Servicio para desarrolladores | Servicio de datos | Plantilla de solución | |----|-----|-----|-----|-----| |**Paso 2: Creación de la oferta** | [Requisitos previos generales no técnicos](marketplace-publishing-pre-requisites.md)| [Requisitos previos generales no técnicos](marketplace-publishing-pre-requisites.md) | [Requisitos previos generales no técnicos](marketplace-publishing-pre-requisites.md) | [Requisitos previos generales no técnicos](marketplace-publishing-pre-requisites.md) | || [Requisitos previos técnicos de imagen de la máquina virtual][link-single-vm-prereq] | Requisitos previos técnicos de servicio de desarrolladores | [Requisitos previos técnicos de servicio de datos](marketplace-publishing-data-service-creation-prerequisites.md) |[ Requisitos previos técnicos de plantilla de solución](marketplace-publishing-solution-template-creation-prerequisites.md) | || [Guía de publicación de imágenes de máquina virtual][link-single-vm] | Guía de publicación de servicios de desarrolladores | [Guía de publicación de servicios de datos](marketplace-publishing-data-service-creation.md) | [Guía de publicación de plantillas de solución](marketplace-publishing-solution-template-creation.md) | || [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] | [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] | [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] | [Guía de contenido de marketing de Azure Marketplace][link-pushstaging] |

## Consulte también
- [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/es-ES/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md

<!---HONumber=AcomDC_1203_2015-->