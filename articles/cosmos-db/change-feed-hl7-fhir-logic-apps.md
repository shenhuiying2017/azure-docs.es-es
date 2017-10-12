---
title: 'Fuente de cambios para recursos de HL7 FHIR: Azure Cosmos DB | Microsoft Docs'
description: Aprenda a configurar notificaciones de cambio para los registros de asistencia sanitaria de paciente de HL7 FHIR con Azure Logic Apps, Azure Cosmos DB y Service Bus.
keywords: hl7 fhir
services: cosmos-db
author: hedidin
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 0d25c11f-9197-419a-aa19-4614c6ab2d06
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: b-hoedid
ms.openlocfilehash: d2b50c0b6864af41fb9cfa051721c432772b228d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Notificación a los pacientes de cambios en los registros de asistencia sanitaria de HL7 FHIR con Logic Apps y Azure Cosmos DB

Recientemente un organización que proporciona asistencia sanitaria se puso en contacto con Azure MVP Howard Edidin, porque deseaba agregar una nueva funcionalidad a su portal de pacientes. Querían enviar notificaciones a los pacientes cuando había una actualización en sus registros sanitarios y que los pacientes pudieran suscribirse a estas actualizaciones. 

Este artículo le guía a través de la solución de notificación de fuente de cambios creada para esta organización de asistencia sanitaria mediante Azure Cosmos DB, Logic Apps y Service Bus. 

## <a name="project-requirements"></a>Requisitos de proyecto
- Los proveedores envían documentos de arquitectura de documento clínico consolidado (C-CDA) HL7 en formato XML. Los documentos C-CDA comprenden prácticamente todos los tipos de documentos clínicos, incluidos entre otros historiales de familia y registros de inmunización, así como documentos administrativos, de flujo de trabajo y financieros. 
- Los documentos C-CDA se convierten en [Recursos HL7 FHIR](http://hl7.org/fhir/2017Jan/resourcelist.html) en formato JSON.
- Los documentos de recursos FHIR modificados se envían por correo electrónico en formato JSON.

## <a name="solution-workflow"></a>Flujo de trabajo de la solución 

En un nivel alto, el proyecto necesita los siguientes pasos de flujo de trabajo: 
1. Convertir documentos de C-CDA a recursos FHIR.
2. Realizar sondeos periódicos con desencadenadores para localizar recursos FHIR modificados. 
2. Llamar a una aplicación personalizada, FhirNotificationApi, para conectarse a Azure Cosmos DB y consultar si hay documentos nuevos o modificados.
3. Guardar la respuesta en la cola de Service Bus.
4. Sondear para ver si hay nuevos mensajes en la cola de Service Bus.
5. Enviar notificaciones por correo electrónico a los pacientes.

## <a name="solution-architecture"></a>Arquitectura de la solución
Esta solución requiere que tres instancias de Logic Apps cumplan los requisitos anteriores y completen el flujo de trabajo de la solución. Las tres aplicaciones lógicas son:
1. **Aplicación HL7-FHIR-Mapping**: recibe el documento HL7 C-CDA, lo transforma en recurso FHIR y luego lo guarda en Azure Cosmos DB.
2. **Aplicación EHR**: consulta el repositorio FHIR de Azure Cosmos DB y guarda la respuesta en una cola de Service Bus. Esta aplicación lógica utiliza una [aplicación de API](#api-app) para recuperar los documentos nuevos y cambiados.
3. **Aplicación de notificación de proceso**: envía una notificación por correo electrónico con los documentos de recursos FHIR en el cuerpo del mensaje.

![Las tres instancias de Logic Apps utilizadas en esta solución de servicios de asistencia sanitaria de HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>Servicios de Azure utilizados en la solución

#### <a name="azure-cosmos-db-documentdb-api"></a>API de DocumentDB de Azure Cosmos DB
Azure Cosmos DB es el repositorio para los recursos FHIR tal y como se muestra en la ilustración siguiente.

![Cuenta de Azure Cosmos DB que se usa en este tutorial de asistencia sanitaria de FHIR HL7](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Aplicaciones lógicas
Logic Apps controlan el proceso de flujo de trabajo. Las capturas de pantalla siguientes muestran las aplicaciones lógicas creadas para esta solución. 


1. **Aplicación HL7-FHIR-Mapping**: recibe el documento HL7 C-CDA y lo transforma en recurso FHIR usando Enterprise Integration Pack para Logic Apps. Enterprise Integration Pack controla la asignación de la C-CDA a los recursos FHIR.

    ![La aplicación lógica que se usa para recibir los registros de los servicios de asistencia sanitaria FHIR HL7](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **Aplicación EHR**: consulta el repositorio FHIR de Azure Cosmos DB y guarda la respuesta en una cola de Service Bus. El código de la aplicación GetNewOrModifiedFHIRDocuments está a continuación.

    ![Logic App que se usa para consultar Azure Cosmos DB](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Aplicación de notificación de proceso**: envía una notificación por correo electrónico con los documentos de recursos FHIR en el cuerpo del mensaje.

    ![La aplicación lógica que envía correos electrónicos al paciente con el recurso de HL7 FHIR en el cuerpo del mensaje](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
La figura siguiente muestra la cola de pacientes. El valor de propiedad de etiqueta se utiliza para el asunto del correo electrónico.

![La cola de Service Bus usada en este tutorial FHIR HL7](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>Aplicación de API
Una aplicación de API se conecta a Azure Cosmos DB y consulta si hay documentos FHIR nuevos o modificados por tipo de recurso. Esta aplicación tiene un controlador, **FhirNotificationApi** con una sola operación **GetNewOrModifiedFhirDocuments**, consulte [Origen de la aplicación de API](#api-app-source).

Usamos la clase [ `CreateDocumentChangeFeedQuery` ](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) a partir de la API de .NET de DocumentDB de Azure Cosmos DB. Para obtener más información, consulte el artículo [Compatibilidad con la fuente de cambios en Azure DocumentDB](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>Operación GetNewOrModifiedFhirDocuments

**Entradas**
- DatabaseId
- CollectionId
- Nombre de tipo de recurso de HL7 FHIR
- Un valor booleano: Iniciar desde el principio
- Int: Número de documentos procesados

**Outputs**
- Correcto: Código de estado: 200, Respuesta: lista de documentos (matriz JSON)
- Error: Código de estado: 404, Respuesta: "No se han encontrado documentos del tipo de recurso '*nombre de recurso '*"

<a id="api-app-source"></a>

**Origen de la aplicación de API**

```C#

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///     or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modifed FHIR Documents By Resource Type " +
                "from Last Run Date or Begining of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modifed Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();  
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>Prueba de FhirNotificationApi 

La imagen siguiente muestra cómo se usa Swagger para probar [FhirNotificationApi](#api-app-source).

![El archivo Swagger que se usa para probar la aplicación de API](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Panel de Portal de Azure

La siguiente imagen muestra todos los servicios de Azure para esta solución ejecutándose en Azure Portal.

![Azure Portal mostrando todos los servicios que se usan en este tutorial FHIR HL7](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Resumen

- Ha aprendido que Azure Cosmos DB tiene soporte técnico nativo para las notificaciones para documentos nuevos o modificados y ha visto lo fácil que es de usar. 
- Ha visto como aprovechando Logic Apps puede crear flujos de trabajo sin escribir ningún código.
- Ha visto que usando las colas de Azure Service Bus puede controlar la distribución de los documentos de HL7 FHIR.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre Azure Cosmos DB, consulte la [página principal de Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Para más información acerca de Logic Apps, consulte [Logic Apps](https://azure.microsoft.com/services/logic-apps/).


