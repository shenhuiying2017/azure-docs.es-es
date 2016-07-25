<properties 
	pageTitle="Información sobre asociados y Enterprise Integration Pack | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
	description="Sepa cómo usar partners con las Aplicaciones lógicas y Enterprise Integration Pack" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Información sobre partners y Enterprise Integration Pack

## Información general
Antes de crear a un partner, tanto usted como la organización con la que vaya a hacer negocios deben compartir la información que lo ayude a identificar y validar los mensajes que se envían entre sí. Tras estos intercambios y cuando esté preparado para iniciar la relación empresarial, podrá crear un *partner* en la cuenta de integración.

## ¿Qué es un partner?
Los partners son las entidades que participan en las transacciones y los intercambios de mensajes de negocio a negocio (B2B).

## ¿Cómo se usan los partners?
Los partners se utilizan para crear contratos. Los contratos definen los detalles de los mensajes que se intercambiarán entre los partners.

Antes de crear uno, hay que agregar, como mínimo, dos partners a la cuenta de integración. Además, uno de ellos uno debe ser su organización. El partner que la representa se conoce como el "**partner anfitrión**". El segundo partner representaría la otra organización con la que su organización intercambia mensajes. A este se le conoce como "**partner invitado**". El partner invitado puede ser otra compañía o incluso un departamento de su organización.

Cuando haya agregado los partners, los usaría para crear un contrato.

La configuración de recepción y de envío afecta exclusivamente al partner anfitrión. Por ejemplo, la configuración de recepción de un contrato determina cómo el partner anfitrión recibe los mensajes enviados de un partner invitado. Del mismo modo, la configuración de envío del contrato indica cómo el partner anfitrión envía mensajes al partner invitado.

## ¿Cómo se crean partners?
En el Portal de Azure:
1. Seleccione **Examinar**. ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Escriba **integration** en el cuadro de búsqueda del filtro y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Seleccione la **cuenta integración** en la que vaya a agregar los partners. ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Seleccione el icono de **Asociados**. ![](./media/app-service-logic-enterprise-integration-partners/partner-1.png)
5. Haga clic en el botón **Agregar** de la hoja Asociados que se abre. ![](./media/app-service-logic-enterprise-integration-partners/partner-2.png)
6. Escriba un **nombre** para el partner; después, seleccione el **calificador** y, por último, especifique un **valor**. El valor se utiliza para ayudar a identificar los documentos que se incluyen en las aplicaciones. ![](./media/app-service-logic-enterprise-integration-partners/partner-3.png)
7. Seleccione el icono de notificación de *campana* para ver el progreso del proceso de creación de socios comerciales. ![](./media/app-service-logic-enterprise-integration-partners/partner-4.png)
8. Seleccione el icono de **Asociados**. Con esto, se actualiza el icono y debería ver que el número de partners aumenta, lo que significa que se ha agregado correctamente el nuevo partner. ![](./media/app-service-logic-enterprise-integration-partners/partner-5.png)
10. Cuando haya seleccionado el icono de Asociados, también verá que el partner que acaba de agregar aparece en la hoja Asociados. ![](./media/app-service-logic-enterprise-integration-partners/partner-6.png)

## ¿Cómo se editan partners?

Siga estos pasos para editar un partner que ya exista en la cuenta de integración:
1. Seleccione el icono de **Asociados**.
2. Elija el partner que quiera editar cuando se abra la hoja Asociados.
3. En el icono de **Update Partner** (Actualizar partner), realice los cambios necesarios.
4. Si está satisfecho con los cambios, haga clic en el vínculo **Guardar**, o bien en **Descartar** para deshacerlos. ![](./media/app-service-logic-enterprise-integration-partners/edit-1.png)

## ¿Cómo se eliminan partners?
1. Seleccione el icono de **Asociados**.
2. Elija el partner que quiera editar cuando se abra la hoja Asociados.
3. Haga clic en el vínculo **Eliminar**. ![](./media/app-service-logic-enterprise-integration-partners/delete-1.png)

## Pasos siguientes
- [Más información sobre los contratos](./app-service-logic-enterprise-integration-agreements.md "Información sobre los contratos de integración de empresas")

<!---HONumber=AcomDC_0713_2016-->