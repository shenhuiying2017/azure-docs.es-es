---
title: Trabajo con Reliable Collections | Microsoft Docs
description: Aprenda los procedimientos recomendados para trabajar con Reliable Collections.
services: service-fabric
documentationcenter: .net
author: rajak
manager: timlt
editor: 
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2017
ms.author: rajak
ms.openlocfilehash: f53f13e4fb83b1cd370ec673e86e5311cd93055f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="working-with-reliable-collections"></a>Trabajo con Reliable Collections
Service Fabric ofrece un modelo de programación con estado a los desarrolladores de .NET a través de Reliable Collections. En concreto, Service Fabric proporciona un diccionario confiable y clases de cola confiables. Al utilizar estas clases, se crean particiones en el estado (para escalabilidad) y este se replica (para disponibilidad) y se tramita dentro de una partición (para semántica ACID). Veamos un uso típico de un objeto de diccionario confiable y vea lo que está haciendo realmente.

```csharp

///retry:

try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) {
   await Task.Delay(100, cancellationToken); goto retry;
}
```

Todas las operaciones en objetos de diccionario confiables (excepto ClearAsync, que no se puede deshacer), requieren un objeto ITransaction. Este objeto tiene asociados todos los cambios que está intentando realizar en cualquiera diccionario confiable y/u objeto de cola confiable dentro de una sola partición. Un objeto ITransaction se adquiere llamando al método CreateTransaction de StateManager de la partición.

En el código anterior, el objeto ITransaction se pasa al método AddAsync de un diccionario confiable. Internamente, los métodos de diccionario que acepta una clave toman un bloqueo de lector o escritor asociado a dicha clave. Si el método modifica el valor de la clave, dicho método toma un bloqueo de escritura en la clave; si el método solo lee el valor de la clave, se toma un bloqueo de lectura en la clave. Puesto que AddAsync modifica el valor de la clave al nuevo valor pasado, se toma el bloqueo de escritura de la clave. Por tanto, si 2 (o más) subprocesos intentan agregar valores con la misma clave simultáneamente, un subproceso adquirirá el bloqueo de escritura y los otros subprocesos se bloquearán. De forma predeterminada, los métodos se bloquean hasta 4 segundos para adquirir el bloqueo; después de 4 segundos, los métodos inician una excepción TimeoutException. Existen sobrecargas de método que le permiten pasar un valor de tiempo de espera explícito si lo prefiere.

Normalmente, el código se escribe para reaccionar ante una excepción TimeoutException capturándola y reintentando la operación completa (como se muestra en el código anterior). En mi sencillo código, simplemente llamo a Task.Delay pasando 100 milisegundos cada vez. Sin embargo, en realidad, podría ser mejor usar algún tipo de retraso de interrupción exponencial en su lugar.

Una vez que se adquiere el bloqueo, AddAsync agrega las referencias de objeto de clave y valor a un diccionario temporal interno asociado al objeto ITransaction. Esto se hace para proporcionar una semántica de lectura de escrituras propias. Es decir, después de llamar a AddAsync, una llamada posterior a TryGetValueAsync (con el mismo objeto ITransaction) devolverá el valor incluso si todavía no ha confirmado la transacción. A continuación, AddAsync serializa los objetos de clave y valor en matrices de bytes y anexa estas matrices de bytes a un archivo de registro en el nodo local. Finalmente, AddAsync envía las matrices de bytes a todas las réplicas secundarias, por lo que tienen la misma información de clave y valor. Aunque la información de clave y valor se ha escrito en un archivo de registro, la información no se considera parte del diccionario hasta que se ha confirmado la transacción a la que están asociados.

En el código anterior, la llamada a CommitAsync confirma todas las operaciones de la transacción. Específicamente, anexa información de confirmación al archivo de registro en el nodo local y también envía el registro de confirmación a todas las réplicas secundarias. Una vez que un cuórum (mayoría) de las réplicas ha respondido, todos los cambios de datos se consideran permanentes y se liberan todos los bloqueos asociados a las claves que se manipularon a través del objeto ITransaction de forma que otros subprocesos y transacciones puedan manipular las mismas claves y sus valores.

Si no se llama a CommitAsync (normalmente debido a una excepción iniciada), se elimina el objeto ITransaction. Al desechar un objeto ITransaction sin confirmar, Service Fabric anexa información de anulación al archivo de registro del nodo local y no es necesario enviar nada a ninguna de las réplicas secundarias. Y después, se liberan los bloqueos asociados a las claves que se manipularon a través de la transacción.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Dificultades comunes y cómo evitarlas
Ahora que entiende cómo funcionan internamente las colecciones confiables, echemos un vistazo a algunos usos incorrectos comunes de ellas. Vea el código siguiente:

```csharp
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Cuando se trabaja con un diccionario .NET común, puede agregar una clave y un valor al diccionario y, luego, cambiar el valor de una propiedad (por ejemplo, LastLogin). Sin embargo, este código no funcionará correctamente con un diccionario confiable. Según la explicación anterior, recuerde que la llamada a AddAsync serializa los objetos de clave y valor en matrices de bytes y, luego, guarda las matrices en un archivo local y también las envía a las réplicas secundarias. Si posteriormente cambia una propiedad, esto cambia el valor de la propiedad solo en la memoria; no afecta al archivo local o a los datos que se enviarán a las réplicas. Si el proceso se bloquea, lo que está en memoria se desecha. Cuando se inicia un nuevo proceso u otra réplica se convierte en principal, el valor de propiedad anterior es lo que está disponible.

Es fundamental recalcar lo fácil que es cometer el tipo de error mostrado anteriormente. Y solo aprenderá del error cuando el proceso termine. La manera correcta de escribir el código es simplemente invertir las dos líneas:


```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Este es otro ejemplo que muestra un error común:

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

De nuevo, con los diccionarios .NET convencionales, el código anterior funciona bien y es un patrón común: el desarrollador usa una clave para buscar un valor. Si el valor existe, el desarrollador cambia el valor de una propiedad. Sin embargo, con colecciones confiables, este código tiene el mismo problema que se comentó anteriormente: **no DEBE modificar un objeto una vez que lo haya entregado a una colección confiable.**

La forma correcta de actualizar un valor en una colección confiable es obtener una referencia al valor existente y considerar el objeto al que se refiere esta referencia como inmutable. Después, cree un nuevo objeto que es una copia exacta del objeto original. Ahora, puede modificar el estado de este nuevo objeto y escribir este en la colección para que se serialice en matrices de bytes, se anexe al archivo local y se envíe a las réplicas. Después de confirmar los cambios, los objetos en memoria, el archivo local y todas las réplicas tienen exactamente el mismo estado. ¡Todo es correcto!

El código siguiente muestra la manera adecuada de actualizar un valor en una colección confiable:

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definición de tipos de datos inmutables para evitar errores de programador
Idealmente, nos gustaría que el compilador informara de errores cuando se crea accidentalmente código que transforma el estado de un objeto que se supone que se considera inmutable. Sin embargo, el compilador de C# no tiene la posibilidad de hacer esto. Por lo tanto, para evitar posibles errores de programador, es muy recomendable que defina los tipos que usa con colecciones confiables para que sean tipos inmutables. En concreto, esto significa que se debe ceñir a tipos de valor principales (como números [Int32, UInt64, etc.], DateTime, Guid, TimeSpan y similares). Y, por supuesto, también puede usar String. Es mejor evitar las propiedades de la colección ya que la serialización y deserialización de las mismas puede, con frecuencia, afectar negativamente al rendimiento. Sin embargo, si desea utilizar las propiedades de la colección, es muy recomendable el uso de la biblioteca de colecciones inmutables de .NET ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Esta biblioteca está disponible para descargarse desde http://nuget.org. También se recomienda sellar las clases y establecer los campos como solo lectura siempre que sea posible.

El tipo UserInfo siguiente muestra cómo definir un tipo inmutable aprovechando las recomendaciones mencionados anteriormente.

```csharp

[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

El tipo ItemId es también un tipo inmutable, tal y como se muestra aquí:

```csharp

[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Control de versiones de esquema (actualizaciones)
Internamente, Reliable Collections serializa los objetos mediante DataContractSerializer de NET. Los objetos serializados se conservan en el disco local de la réplica principal y también se transmiten a las réplicas secundarias. A medida que se desarrolle el servicio, es probable que desee cambiar el tipo de datos (esquema) que el servicio requiere. Debe abordar el control de versiones de los datos con mucho cuidado. En primer lugar y ante todo, siempre debe ser capaz de deserializar los datos antiguos. En concreto, esto significa que el código de deserialización debe ser compatible con versiones anteriores de forma ilimitada: la versión 333 del código de servicio debe ser capaz de funcionar en los datos colocados en una colección confiable por la versión 1 del código de servicio de hace 5 años.

Además, el código de servicio se actualiza con un dominio de actualización en cada momento. Por lo tanto, durante una actualización, tiene dos versiones diferentes del código de servicio ejecutándose simultáneamente. Debe evitar que la nueva versión del código de servicio utilice el nuevo esquema, ya que las versiones anteriores de dicho código podrían no ser capaces de controlar el nuevo esquema. Cuando sea posible, debería diseñar cada versión del servicio para que sea compatible con versiones posteriores mediante la versión 1. En concreto, esto significa que la versión 1 (V1) del código de servicio simplemente debe ser capaz de omitir cualquier elemento de esquema que no controla explícitamente. Sin embargo, debe ser capaz de guardar todos los datos que no conoce explícitamente y simplemente reescribirlos al actualizar un valor o una clave de diccionario.

> [!WARNING]
> Aunque puede modificar el esquema de una clave, debe asegurarse de que el código hash de la clave y los algoritmos de igualdades son estables. Si cambia la forma en la que cualquiera de estos algoritmos opera, no podrá volver a buscar la clave del diccionario confiable nunca más.
>
>

Como alternativa, puede realizar lo que se conoce normalmente como una actualización de 2 fases. Con una actualización de 2 fases, usted actualiza el servicio de V1 a V2: V2 contiene el código que sabe cómo tratar con el nuevo cambio de esquema, pero este código no se ejecuta. Cuando el código de V2 lee datos de V1, opera en ellos y escribe datos de V1. Luego, después de que la actualización se complete en todos los dominios de actualización, puede indicar de algún modo a las instancias de V2 en ejecución que la actualización se ha completado. (Una forma de indicar esto es lanzar una actualización de la configuración; esta característica es la que convierte a esto en una actualización de 2 fases). Ahora, las instancias de V2 pueden leer datos de V1, convertirlos en datos de V2, operar en ellos y escribirlos como datos de V2. Cuando otras instancias lean datos de V2, no necesitarán convertirlos; simplemente operarán en ellos y escribirán datos de V2.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre la creación de contratos de datos compatibles con versiones posteriores, vea [Contratos de datos compatibles con versiones posteriores](https://msdn.microsoft.com/library/ms731083.aspx).

Para obtener procedimientos recomendados sobre el control de versiones de contratos de datos, consulte [Versiones de contratos de datos](https://msdn.microsoft.com/library/ms731138.aspx).

Para más información sobre cómo implementar contratos de datos tolerantes a versiones, consulte [Devoluciones de llamadas en la serialización tolerante a versiones](https://msdn.microsoft.com/library/ms733734.aspx).

Para más información sobre cómo proporcionar una estructura de datos que pueda interoperar entre varias versiones, consulte [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
