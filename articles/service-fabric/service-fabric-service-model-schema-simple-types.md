---
title: Jednoduché typy schématu XML modelu služby Azure Service Fabric
description: Popisuje jednoduché typy ve schématu XML modelu služby Service Fabric.
ms.topic: reference
ms.date: 12/10/2018
ms.openlocfilehash: 3ed028bc255fa9561316a655da6edfacceff1c55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466214"
---
<!-- This article was generated by the Python script found in the service-fabric-service-model-schema.md file -->

# <a name="service-model-xml-schema-simple-types"></a>Jednoduché typy schématu XML modelu služby

## <a name="fabricuri-simpletype"></a>FabricUri simpleType
Identifikátor URI, který se používá jako stabilní identifikátor služeb systémem microsoft azure service fabric pojmenování. 

### <a name="xml-source"></a>Zdroj XML
```xml
<xs:simpleType xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="FabricUri">
    <xs:annotation>
      <xs:documentation>A URI that is used as a stable identifier of services by Microsoft Azure Service Fabric Naming system. </xs:documentation>
    </xs:annotation>
    <xs:restriction base="xs:anyURI"/>
  </xs:simpleType>
  
```
