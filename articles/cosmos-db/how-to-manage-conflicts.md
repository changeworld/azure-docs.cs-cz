---
title: Správa konfliktů mezi oblastmi v Azure Cosmos DB
description: Zjistěte, jak spravovat konflikty v Azure Cosmos DB vytvořením poslední hozanec-wins nebo vlastní zásady řešení konfliktů
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: mjbrown
ms.openlocfilehash: 6d364f1a9974d6d638bb0f824e88ed3866644c15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247407"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Správa zásad řešení konfliktů v Azure Cosmos DB

Při zápisu s více oblastmi, když více klientů zapisuje do stejné položky, může dojít ke konfliktům. Dojde-li ke konfliktu, můžete konflikt vyřešit pomocí různých zásad řešení konfliktů. Tento článek popisuje, jak spravovat zásady řešení konfliktů.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Vytvoření zásady řešení konfliktů last-writer-wins

Tyto ukázky ukazují, jak nastavit kontejner s poslední writer-wins zásady řešení konfliktů. Výchozí cesta pro poslední zapisovač-wins je `_ts` pole časového razítka nebo vlastnost. Pro rozhraní SQL API to může být také nastavena na uživatelem definovanou cestu s číselným typem. V konfliktu vyhrává nejvyšší hodnota. Pokud cesta není nastavená nebo je neplatná, `_ts`bude výchozí . Konflikty vyřešené touto zásadou se nezobrazují v kanálu konfliktů. Tuto zásadu lze použít pro všechna pravidla API.

### <a name="net-sdk-v2"></a><a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>Sada .NET SDK V2

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/myCustomId",
      },
  });
```

### <a name="net-sdk-v3"></a><a id="create-custom-conflict-resolution-policy-lww-dotnet-v3"></a>Sada .NET SDK V3

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.lwwCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.LastWriterWins,
            ResolutionPath = "/myCustomId",
        }
    });
```

### <a name="java-async-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a name="java-sync-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-java-sync"></a>Java Sync SDK

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/myCustomId"
    }
  }
);
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-python"></a>Python SDK

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'LastWriterWins',
        'conflictResolutionPath': '/myCustomId'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>Vytvoření vlastní zásady řešení konfliktů pomocí uložené procedury

Tyto ukázky předvádějí, jak nastavit kontejner s vlastní zásadou řešení konfliktů s využitím uložené procedury, která konflikt vyřeší. Tyto konflikty se nezobrazují v kanálu konfliktů, pokud není chyba v uložené proceduře. Po vytvoření zásady s kontejnerem je třeba vytvořit uloženou proceduru. Ukázka sady .NET SDK níže ukazuje příklad. Tato zásada je podporována pouze v rozhraní API core (SQL).

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>Ukázka vlastní konfliktřešení uložené procedury

Vlastní konfliktřešení uložené procedury musí být implementovány pomocí podpisu funkce je uvedeno níže. Název funkce nemusí odpovídat názvu použitému při registraci uložené procedury s kontejnerem, ale zjednodušuje pojmenování. Zde je popis parametrů, které musí být implementovány pro tuto uloženou proceduru.

- **incomingItem**: Položka, která je vložena nebo aktualizována v potvrzení, které generuje konflikty. Je null pro operace odstranění.
- **existingItem**: Aktuálně potvrzená položka. Tato hodnota je non-null v aktualizaci a null pro vložení nebo odstraní.
- **isTombstone**: Logická hodnota označující, zda je příchozí položka v konfliktu s dříve odstraněnou položkou. Pokud true, existingItem je také null.
- **conflictingItems**: Pole potvrzené verze všech položek v kontejneru, které jsou v konfliktu s incomingItem na ID nebo jiné jedinečné vlastnosti indexu.

> [!IMPORTANT]
> Stejně jako u jakékoli uložené procedury, vlastní procedury řešení konfliktů můžete přistupovat k všechna data se stejným klíčem oddílu a můžete provést jakékoli vložení, aktualizace nebo odstranění operace k vyřešení konfliktů.

Tato ukázková uložená procedura řeší konflikty `/myCustomId` výběrem nejnižší hodnoty z cesty.

```javascript
function resolver(incomingItem, existingItem, isTombstone, conflictingItems) {
  var collection = getContext().getCollection();

  if (!incomingItem) {
      if (existingItem) {

          collection.deleteDocument(existingItem._self, {}, function (err, responseOptions) {
              if (err) throw err;
          });
      }
  } else if (isTombstone) {
      // delete always wins.
  } else {
      if (existingItem) {
          if (incomingItem.myCustomId > existingItem.myCustomId) {
              return; // existing item wins
          }
      }

      var i;
      for (i = 0; i < conflictingItems.length; i++) {
          if (incomingItem.myCustomId > conflictingItems[i].myCustomId) {
              return; // existing conflict item wins
          }
      }

      // incoming item wins - clear conflicts and replace existing with incoming.
      tryDelete(conflictingItems, incomingItem, existingItem);
  }

  function tryDelete(documents, incoming, existing) {
      if (documents.length > 0) {
          collection.deleteDocument(documents[0]._self, {}, function (err, responseOptions) {
              if (err) throw err;

              documents.shift();
              tryDelete(documents, incoming, existing);
          });
      } else if (existing) {
          collection.replaceDocument(existing._self, incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      } else {
          collection.createDocument(collection.getSelfLink(), incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      }
  }
}
```

### <a name="net-sdk-v2"></a><a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>Sada .NET SDK V2

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });

//Create the stored procedure
await clients[0].CreateStoredProcedureAsync(
UriFactory.CreateStoredProcedureUri(this.databaseName, this.udpCollectionName, "resolver"), new StoredProcedure
{
    Id = "resolver",
    Body = File.ReadAllText(@"resolver.js")
});
```

### <a name="net-sdk-v3"></a><a id="create-custom-conflict-resolution-policy-stored-proc-dotnet-v3"></a>Sada .NET SDK V3

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.udpCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom,
            ResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver")
        }
    });

await container.Scripts.CreateStoredProcedureAsync(
    new StoredProcedureProperties("resolver", File.ReadAllText(@"resolver.js"))
);
```

### <a name="java-async-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

Po vytvoření kontejneru je nutné `resolver` vytvořit uloženou proceduru.

### <a name="java-sync-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-java-sync"></a>Java Sync SDK

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```

Po vytvoření kontejneru je nutné `resolver` vytvořit uloženou proceduru.

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

Po vytvoření kontejneru je nutné `resolver` vytvořit uloženou proceduru.

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Python SDK

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom',
        'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

Po vytvoření kontejneru je nutné `resolver` vytvořit uloženou proceduru.

## <a name="create-a-custom-conflict-resolution-policy"></a>Vytvoření vlastní zásady řešení konfliktů

Tyto ukázky předvádějí, jak nastavit kontejner s vlastní zásadou řešení konfliktů. Tyto konflikty se zobrazí v kanálu konfliktů.

### <a name="net-sdk-v2"></a><a id="create-custom-conflict-resolution-policy-dotnet"></a>Sada .NET SDK V2

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

### <a name="net-sdk-v3"></a><a id="create-custom-conflict-resolution-policy-dotnet-v3"></a>Sada .NET SDK V3

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.manualCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom
        }
    });
```

### <a name="java-async-sdk"></a><a id="create-custom-conflict-resolution-policy-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a name="java-sync-sdk"></a><a id="create-custom-conflict-resolution-policy-java-sync"></a>Java Sync SDK

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-python"></a>Python SDK

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
    'id': self.manual_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom'
    }
}
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="read-from-conflict-feed"></a>Čtení z informačního kanálu konfliktů

Tyto ukázky předvádějí, jak číst z informačního kanálu konfliktů kontejneru. Konflikty se v kanálu konfliktů zobrazují pouze v případě, že nebyly vyřešeny automaticky nebo pokud používáte vlastní zásady konfliktů.

### <a name="net-sdk-v2"></a><a id="read-from-conflict-feed-dotnet"></a>Sada .NET SDK V2

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

### <a name="net-sdk-v3"></a><a id="read-from-conflict-feed-dotnet-v3"></a>Sada .NET SDK V3

```csharp
FeedIterator<ConflictProperties> conflictFeed = container.Conflicts.GetConflictQueryIterator();
while (conflictFeed.HasMoreResults)
{
    FeedResponse<ConflictProperties> conflicts = await conflictFeed.ReadNextAsync();
    foreach (ConflictProperties conflict in conflicts)
    {
        // Read the conflicted content
        MyClass intendedChanges = container.Conflicts.ReadConflictContent<MyClass>(conflict);
        MyClass currentState = await container.Conflicts.ReadCurrentAsync<MyClass>(conflict, new PartitionKey(intendedChanges.MyPartitionKey));

        // Do manual merge among documents
        await container.ReplaceItemAsync<MyClass>(intendedChanges, intendedChanges.Id, new PartitionKey(intendedChanges.MyPartitionKey));

        // Delete the conflict
        await container.Conflicts.DeleteAsync(conflict, new PartitionKey(intendedChanges.MyPartitionKey));
    }
}
```

### <a name="java-async-sdk"></a><a id="read-from-conflict-feed-java-async"></a>Java Async SDK

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```

### <a name="java-sync-sdk"></a><a id="read-from-conflict-feed-java-sync"></a>Java Sync SDK

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```

### <a name="nodejsjavascripttypescript-sdk"></a><a id="read-from-conflict-feed-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a name="python"></a><a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Další kroky

Seznamte se s následujícími koncepty Azure Cosmos DB:

- [Globální distribuce – pod pokličkou](global-dist-under-the-hood.md)
- [Jak nakonfigurovat multimaster ve vašich aplikacích](how-to-multi-master.md)
- [Konfigurace klientů pro multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
- [Přidání nebo odebrání oblastí z účtu Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
- [Jak nakonfigurovat vícehlavních serverů v aplikacích](how-to-multi-master.md).
- [Dělení a distribuce dat](partition-data.md)
- [Indexování ve službě Azure Cosmos DB](indexing-policies.md)
