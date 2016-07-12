<properties
   pageTitle="Notificaciones de servicio de Reliable Services | Microsoft Azure"
   description="Documentación conceptual sobre las notificaciones de servicio de Reliable Services de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/24/2016"
   ms.author="mcoskun"/>

# Notificaciones de servicio de Reliable Services

Las notificaciones permiten que los clientes sigan los cambios que se están realizando en el objeto que les interesa. Existen dos tipos de objeto que admiten notificaciones: **Reliable State Manager** y **Reliable Dictionary**.

Algunas causas habituales del uso de notificaciones son

- Creación de vistas materializadas como índices secundarios o vistas filtradas agregadas del estado de la réplica. Por ejemplo, un índice ordenado de todas las claves en un diccionario Reliable Dictionary.
- Envío de datos de supervisión, como el número de usuarios agregados en la última hora.

Las notificaciones se desencadenan como parte de la aplicación de operaciones. Dado que forman parte de la aplicación de la operación, es importante tener en cuenta que se debería completar el control de las notificaciones con la mayor rapidez y no se debería llevar a cabo ninguna operación costosa en eventos sincrónicos.

## Notificaciones de Reliable State Manager

Reliable State Manager proporciona notificaciones para los siguientes eventos:

- Transacción
    - Confirmación
- Administrador de estado
    - Recompilación
    - Adición de un estado Reliable State
    - Eliminación de un estado Reliable State

Reliable State Manager realiza un seguimiento de las transacciones en proceso actuales. El único cambio de estado de transacción que hará que se desencadene una notificación es la confirmación de una transacción.

Reliable State Manager mantiene una colección de estados Reliable State, como Reliable Dictionary y Reliable Queue. Reliable State Manager desencadena notificaciones cuando cambia esta colección: se agrega un estado Reliable State, se quita o se recompila la colección completa. La colección de Reliable State Manager se recompila en tres situaciones:

- Recuperación: cuando se inicia una réplica, recuperará su estado anterior del disco. Al final de la recuperación, desencadena un evento con **NotifyStateManagerChangedEventArgs** que contiene el conjunto de interfaces **IReliableState** recuperadas.
- Copia completa: para que una réplica pueda unirse al conjunto de configuración, primero tiene que compilarse. En ocasiones, se podría requerir que se aplique una copia completa del estado de Reliable State Manager en la réplica principal a la réplica secundaria inactiva. En la réplica secundaria, Reliable State Manager desencadenará un evento con **NotifyStateManagerChangedEventArgs** que contiene el conjunto de interfaces **IReliableState** que adquirió de la principal.
- Restauración: en escenarios de recuperación ante desastres, se puede restaurar el estado de la réplica desde una copia de seguridad con **RestoreAsync**. En estos casos, en la réplica principal, Reliable State Manager desencadenará un evento con **NotifyStateManagerChangedEventArgs** que contiene el conjunto de interfaces **IReliableState** que restauró de la copia de seguridad.

### Uso de las notificaciones de Reliable State Manager
Para registrarse para las notificaciones de transacciones o notificaciones del administrador de estados, el usuario se debe registrar con los eventos **TransactionChanged** o **StateManagerChanged** en Reliable State Manager, respectivamente. Un lugar habitual donde registrarse con estos controladores de eventos es el constructor de su clase StatefulService. Esto se debe a que, al registrarse en el constructor, el cliente garantiza que no se perderá ninguna notificación causada por un cambio durante la vigencia de **IReliableStateManager**.

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

El controlador de eventos **TransactionChanged** usa **NotifyTransactionChangedEventArgs** para proporcionar detalles sobre el evento. Contiene la propiedad Action (por ejemplo, **NotifyTransactionChangedAction.Commit**), que especifica el tipo de cambio, y la propiedad Transaction, que proporciona una referencia a la transacción que ha cambiado.

>[AZURE.NOTE] Actualmente, solo se generan eventos TransactionChanged si la transacción se confirma; por tanto, Action equivale a NotifyTransactionChangedAction.Commit. Sin embargo, es posible que se añadan otras acciones en futuras actualizaciones. Por lo tanto, se recomienda comprobar la propiedad Action y solo procesar el evento si es el que espera.

A continuación, se ofrece un ejemplo de controlador de eventos **TransactionChanged**.

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

El controlador de eventos **StateManagerChanged** usa **NotifyStateManagerChangedEventArgs** para proporcionar detalles sobre el evento. **NotifyStateManagerChangedEventArgs** tiene dos subclases: NotifyStateManagerRebuildEventArgs y NotifyStateManagerSingleEntityChangedEventArgs. Se debe usar la propiedad Action en **NotifyStateManagerChangedEventArgs** para convertir la subclase **NotifyStateManagerChangedEventArgs** en la correcta.

- NotifyStateManagerChangedAction.Rebuild: NotifyStateManagerRebuildEventArgs
- NotifyStateManagerChangedAction.Add y Remove: NotifyStateManagerSingleEntityChangedEventArgs

A continuación, se ofrece un ejemplo de controlador de notificación **StateManagerChanged**.

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## Notificaciones de Reliable Dictionary

Reliable Dictionary proporciona notificaciones para los siguientes eventos.

- Rebuild: se llama cuando **ReliableDictionary** ha recuperado su estado del disco local, de una copia de la réplica principal o de una copia de seguridad.
- Clear: se llama cuando se ha borrado el estado de **ReliableDictionary** mediante el método **ClearAsync**.
- Add: se llama cuando se ha agregado un elemento a **ReliableDictionary**.
- Update: se llama cuando se ha actualizado un elemento de **IReliableDictionary**.
- Remove: se llama cuando se ha eliminado un elemento de **IReliableDictionary**.

### Uso de las notificaciones de Reliable Dictionary
Para registrarse para las notificaciones de Reliable Dictionary, el usuario se debe registrar con el controlador de eventos **DictionaryChanged** en la interfaz **IReliableDictionary**. Un lugar habitual donde registrarse con estos controladores de eventos es en la notificación de adición de **ReliableStateManager.StateManagerChanged**. Esto es porque, al registrarse en el momento de agregar **IReliableDictionary** a **IReliableStateManager**, se garantiza que no se perderá ninguna notificación.

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

>[AZURE.NOTE] ProcessStateManagerSingleEntityNotification es el método de ejemplo llamado por OnStateManagerChangedHandler en el ejemplo anterior.

Tenga en cuenta que el código anterior establece IReliableNotificationAsyncCallback, además de **DictionaryChanged**. Puesto que **NotifyDictionaryRebuildEventArgs** contiene una interfaz **IAsyncEnumerable** que debe enumerarse de forma asincrónica, las notificaciones de recompilación se desencadenan mediante **RebuildNotificationAsyncCallback** en lugar de **OnDictionaryChangedHandler**.

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

>[AZURE.NOTE] En el código anterior, como parte del procesamiento de la notificación de recompilación, primero se borra el estado agregado mantenido. Esto se debe a que, como se está recompilando la colección Reliable Collection con el nuevo estado, todas las notificaciones anteriores son irrelevantes.

El controlador de eventos **DictionaryChanged** usa **NotifyDictionaryChangedEventArgs** para proporcionar detalles sobre el evento. **NotifyDictionaryChangedEventArgs** tiene cinco subclases. Se debe usar la propiedad Action en **NotifyDictionaryChangedEventArgs** para convertir la subclase **NotifyDictionaryChangedEventArgs** en la correcta.

- NotifyDictionaryChangedAction.Rebuild: NotifyDictionaryRebuildEventArgs
- NotifyDictionaryChangedAction.Clear: NotifyDictionaryClearEventArgs
- NotifyDictionaryChangedAction.Add y Remove: NotifyDictionaryItemAddedEventArgs
- NotifyDictionaryChangedAction.Update: NotifyDictionaryItemUpdatedEventArgs
- NotifyDictionaryChangedAction.Remove: NotifyDictionaryItemRemovedEventArgs

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## Recomendaciones

- Complete los eventos de notificaciones tan rápido como sea posible.
- No ejecute ninguna operación costosa (por ejemplo, operaciones de E/S) como parte de eventos sincrónicos.
- Compruebe el tipo de acción antes de procesar el evento. Se pueden agregar tipos de acción en el futuro.

Algunos aspectos que debe tener en cuenta:
- Las notificaciones se desencadenan como parte de la ejecución de una operación. Por ejemplo, se desencadenará una notificación de restauración como el último paso de la restauración y esta no se completará hasta que se procese el evento de notificación.
- Ya que las notificaciones se desencadenan como parte de la aplicación de operaciones, los clientes solo verán notificaciones para las operaciones confirmadas localmente. Tenga en cuenta que, como solo se garantiza que las operaciones se confirmen localmente (es decir, se registren), es posible que en el futuro se deshagan o no.
- En la ruta de acceso de puesta al día, se desencadenará una única notificación para cada operación aplicada. Esto significa que, si una transacción T1 incluye Create(X), Delete(X), Create(X), recibirá una notificación para la creación de X, una para la eliminación y otra para la creación de nuevo, en ese orden.
- Para las transacciones que contienen varias operaciones, se aplicarán las operaciones en el orden en que se recibieron en la réplica principal del usuario.
- Como parte del procesamiento de progreso falso, es posible que algunas operaciones se deshagan. Se generarán notificaciones para estas operaciones de deshacer y se revertirá el estado de la réplica a un punto estable. Una diferencia importante de las notificaciones de deshacer es que se agregarán los eventos con claves duplicadas. Por ejemplo, si se está deshaciendo la transacción T1 de antes, el usuario verá una única notificación para Delete(X).

## Pasos siguientes

- [Introducción a Reliable Services de Service Fabric de Microsoft Azure](service-fabric-reliable-services-quick-start.md)
- [Copia de seguridad y restauración de Reliable Services (recuperación ante desastres)](service-fabric-reliable-services-backup-restore.md)
- [Referencia para desarrolladores de colecciones confiables](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

<!---HONumber=AcomDC_0629_2016-->