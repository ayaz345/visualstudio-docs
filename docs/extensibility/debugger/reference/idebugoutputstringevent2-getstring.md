---
description: "Gets the displayable message."
title: IDebugOutputStringEvent2::GetString | Microsoft Docs
ms.date: 11/04/2016
ms.topic: reference
f1_keywords:
- IDebugOutputStringEvent2::GetString
helpviewer_keywords:
- IDebugOutputStringEvent2::GetString
ms.assetid: f059f8e0-ad44-49ac-ba90-73576ada5e06
author: maiak
ms.author: maiak
manager: jmartens
ms.technology: vs-ide-debug
ms.workload:
- vssdk
dev_langs:
- CPP
- CSharp
---
# IDebugOutputStringEvent2::GetString

 [!INCLUDE [Visual Studio](~/includes/applies-to-version/vs-windows-only.md)]
Gets the displayable message.

## Syntax

### [C#](#tab/csharp)
```csharp
int GetString( 
   out string pbstrString
);
```
### [C++](#tab/cpp)
```cpp
HRESULT GetString( 
   BSTR* pbstrString
);
```
---

## Parameters
`pbstrString`\
[out] Returns the displayable message.

## Return Value
 If successful, returns `S_OK`; otherwise, returns an error code.

## See also
- [IDebugOutputStringEvent2](../../../extensibility/debugger/reference/idebugoutputstringevent2.md)
