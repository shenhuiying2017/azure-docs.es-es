<properties 
	pageTitle="Información general sobre partners y Enterprise Integration Pack | Servicio de aplicaciones de Microsoft Azure" 
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
	ms.date="06/29/2016" 
	ms.author="deonhe"/>


# Información sobre contratos y Enterprise Integration Pack

## Información general
Los contratos son la piedra angular de las comunicaciones de negocio a negocio (B2B) que permiten a las entidades empresariales comunicarse de perfectamente mediante protocolos estándar del sector.

## ¿Qué es un contrato?

Un contrato, a lo que respecta el Enterprise Integration Pack, es una disposición de las comunicaciones entre los socios comerciales B2B. Un contrato se basa en la comunicación que los socios desean lograr y es específico del protocolo o el transporte.

Integración de Enterprise admite tres estándares de transporte/protocolo:

- [AS2](./app-service-logic-enterprise-integration-as2.md)
- [X12](./app-service-logic-enterprise-integration-x12.md)
- [EDIFACT](./app-service-logic-enterprise-integration-edifact.md) (próximamente)

## Por qué usar contratos
Algunas de las ventajas comunes del uso de contratos son las siguientes:
- Permite que distintas organizaciones y empresas puedan intercambiar información en un formato conocido.
- Mejora la eficacia al realizar transacciones B2B
- Fácil de crearlos, administrarlos y utilizarlos al crear aplicaciones de Enterprise Integration

## Creación de contratos
- [Creación de un contrato AS2](./app-service-logic-enterprise-integration-as2.md)
- [Creación de un contrato X12](./app-service-logic-enterprise-integration-x12.md)

## Uso de contratos
Después de crear un contrato, puede usarlo en el Portal de Azure para crear [aplicaciones lógicas](./app-service-logic-what-are-logic-apps.md "Más información sobre Aplicaciones lógicas") con características de B2B.

## Edición de contratos
Puede editar cualquier acuerdo siguiendo estos pasos:
1. Seleccione la cuenta de integración que contiene el contrato que desea modificar.
2. Seleccione el icono **Agreements** (Contratos)
3. Seleccione el contrato que desee modificar en la hoja **Agreements** (Contratos)
4. Seleccione **Editar** desde el menú anterior
5. Realice los cambios en el menú de edición que aparece y, a continuación, seleccione el botón **Aceptar** para guardar los cambios

## Eliminación de una cuenta
Puede eliminar cualquier contrato siguiendo estos pasos desde dentro de la cuenta de integración que contiene el contrato que desea eliminar:
1. Seleccione el icono **Agreements** (Contratos)
2. Seleccione el contrato que desee eliminar en la hoja **Agreements** (Contratos)
3. Seleccione **Eliminar** desde el menú anterior
4. Confirme que realmente desea eliminar el contrato
5. Tenga en cuenta que el contrato ya no aparece en la hoja Agreements (Contratos)
 

## Pasos siguientes
- [Creación de un contrato AS2](./app-service-logic-enterprise-integration-as2.md)

<!---HONumber=AcomDC_0706_2016-->