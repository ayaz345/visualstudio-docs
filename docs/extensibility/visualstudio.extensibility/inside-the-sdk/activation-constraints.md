---
title: Rule-based activation constraints
description: Learn about activation constraints, which extension authors can use to control the conditions under which extensions surface in the IDE.
ms.topic: conceptual
ms.date: 3/31/2023
ms.author: maiak
monikerRange: ">=vs-2022"
author: maiak
manager: jmartens
ms.technology: vs-ide-sdk
---

# Rule-based activation constraints

One of the common concepts in VisualStudio.Extensibility is use of context-based activation rules in code attributes. These are rules that govern the conditions under which an extension is surfaced to the user. An example of a context-based activation rule is the [`VisibleWhen`](https://github.com/microsoft/VSExtensibility/tree/main/docs/new-extensibility-model/api/Microsoft.VisualStudio.Extensibility.Contracts.md#P-Microsoft-VisualStudio-Extensibility-Commands-CommandConfiguration-VisibleWhen) property in a command's configuration that declares when the command is made visible.

Our goal is to provide a common way to create such contexts.

## Constraint attribute arguments

Each constraint is defined as an instance of the [`ActivationConstraint`](https://github.com/microsoft/VSExtensibility/tree/main/docs/new-extensibility-model/api/Microsoft.VisualStudio.Extensibility.Contracts.md#T-Microsoft-VisualStudio-Extensibility-ActivationConstraint) type created with one of the `ActivationConstraint`'s factory methods.

Multiple activation constraints can be combined together using the [`And`](https://github.com/microsoft/VSExtensibility/tree/main/docs/new-extensibility-model/api/Microsoft.VisualStudio.Extensibility.Contracts.md#M-Microsoft-VisualStudio-Extensibility-ActivationConstraint-And-Microsoft-VisualStudio-Extensibility-ActivationConstraint[]-), [`Or`](https://github.com/microsoft/VSExtensibility/tree/main/docs/new-extensibility-model/api/Microsoft.VisualStudio.Extensibility.Contracts.md#M-Microsoft-VisualStudio-Extensibility-ActivationConstraint-Or-Microsoft-VisualStudio-Extensibility-ActivationConstraint[]-), and [`Not`](https://github.com/microsoft/VSExtensibility/tree/main/docs/new-extensibility-model/api/Microsoft.VisualStudio.Extensibility.Contracts.md#M-Microsoft-VisualStudio-Extensibility-ActivationConstraint-Not-Microsoft-VisualStudio-Extensibility-ActivationConstraint-) methods or the `&`, `|`, and `!` operators.

## Example definition

In the following example, the command configuration defines when the command is in the enabled state.

```csharp
public override CommandConfiguration CommandConfiguration => new("My command")
{
    EnabledWhen = ActivationConstraint.ClientContext(ClientContextKey.Shell.ActiveSelectionFileName, @"\.(jpg|jpeg|txt)$"),
};
```

The following example shows how to combine multiple constraints:

```csharp
EnabledWhen = ActivationConstraint.And(
    ActivationConstraint.SolutionState(SolutionState.Exists),
    ActivationConstraint.ClientContext(ClientContextKey.Shell.ActiveEditorFileName, @"\.(jpg|jpeg|txt)$")),
```

or, more succinctly, using the `&` operator:

```csharp
EnabledWhen =
    ActivationConstraint.SolutionState(SolutionState.Exists) &
    ActivationConstraint.ClientContext(ClientContextKey.Shell.ActiveEditorFileName, @"\.(jpg|jpeg|txt)$")),
```

## Available terms

Following is the list of currently supported activation constraints:

| Term | Description |
| -- | -- |
| [`SolutionHasProjectCapability`](https://github.com/microsoft/VSExtensibility/tree/main/docs/new-extensibility-model/api/Microsoft.VisualStudio.Extensibility.Contracts.md#M-Microsoft-VisualStudio-Extensibility-ActivationConstraint-SolutionHasProjectCapability-Microsoft-VisualStudio-Extensibility-ProjectCapability-)(\<expression>=[`ProjectCapability`](https://github.com/microsoft/VSExtensibility/tree/main/docs/new-extensibility-model/api/Microsoft.VisualStudio.Extensibility.Contracts.md#T-Microsoft-VisualStudio-Extensibility-ProjectCapability)) | True whenever solution has a project with  capabilities matching the provided subexpression. An expression can be something like VB \| CSharp. |
| [`SolutionState`](https://github.com/microsoft/VSExtensibility/tree/main/docs/new-extensibility-model/api/Microsoft.VisualStudio.Extensibility.Contracts.md#M-Microsoft-VisualStudio-Extensibility-ActivationConstraint-SolutionState-Microsoft-VisualStudio-Extensibility-SolutionState-)(\<state>=[`SolutionState`](https://github.com/microsoft/VSExtensibility/tree/main/docs/new-extensibility-model/api/Microsoft.VisualStudio.Extensibility.Contracts.md#T-Microsoft-VisualStudio-Extensibility-SolutionState)) | True when solution state matches to provided value, see [solution states](#solution-states) for list of values. |
| [`ProjectAddedItem`](https://github.com/microsoft/VSExtensibility/tree/main/docs/new-extensibility-model/api/Microsoft.VisualStudio.Extensibility.Contracts.md#M-Microsoft-VisualStudio-Extensibility-ActivationConstraint-ProjectAddedItem-System-String-)(\<pattern>=\<regex>) | The term is true when a file matching the "pattern" is added to a project in the solution that is opened. |
| [`ClientContext`](https://github.com/microsoft/VSExtensibility/tree/main/docs/new-extensibility-model/api/Microsoft.VisualStudio.Extensibility.Contracts.md#M-Microsoft-VisualStudio-Extensibility-ActivationConstraint-ClientContext-Microsoft-VisualStudio-Extensibility-ClientContextKey,System-String-)(\<key>=[`ClientContextKey`](https://github.com/microsoft/VSExtensibility/tree/main/docs/new-extensibility-model/api/Microsoft.VisualStudio.Extensibility.Contracts.md#T-Microsoft-VisualStudio-Extensibility-ClientContextKey), \<pattern>=\<regex>) | True when the provided client context key matches to regular expression. See [client contexts](#client-contexts). |

For compatibility reasons, the following legacy activation constraints are also supported:

| Term | Description |
| -- | -- |
| [`SolutionHasProjectBuildProperty`](https://github.com/microsoft/VSExtensibility/tree/main/docs/new-extensibility-model/api/Microsoft.VisualStudio.Extensibility.Contracts.md#M-Microsoft-VisualStudio-Extensibility-ActivationConstraint-SolutionHasProjectBuildProperty-System-String,System-String-)(\<property>=\<regex>) | The term is true when solution has a loaded project with the specified build property and property value matches to regex filter provided. |
| [`SolutionHasProjectFlavor`](https://github.com/microsoft/VSExtensibility/tree/main/docs/new-extensibility-model/api/Microsoft.VisualStudio.Extensibility.Contracts.md#M-Microsoft-VisualStudio-Extensibility-ActivationConstraint-SolutionHasProjectFlavor-System-Guid-)(\<guid>) | True whenever a solution has project that is flavored (aggregated) and has a flavor matching the given project type GUID. |

## Solution states

The following table shows the possible solution states:

| State | Description |
| -- | -- |
| NoSolution | No solution loaded. |
| Exists | A solution is opened but may be in loaded or loading state. |
| FullyLoaded | A solution is opened and fully loaded. |
| Empty | Solution contains no projects but may contain solution items. |
| SingleProject | Solution contains a single project. |
| MultipleProject | Solution contains multiple projects. |
| Building | Solution is building. |

## Client contexts

Activation rules can also utilize the [client context](extension-anatomy.md#client-context) contents as parts of its expression. 

Currently, the client context is limited to a small set of values in IDE state:

| Context key | Definition |
| -- | -- |
| Shell.ActiveSelectionUri | Full URI for the selected item in solution explorer. |
| Shell.ActiveSelectionPath | Full path for the selected item in solution explorer. |
| Shell.ActiveSelectionFileName | File name of the selected item in solution explorer. |
| Shell.ActiveEditorContentType | Content type of the active editor view. |
| Shell.ActiveEditorFileName | File name for the document that belongs to active editor view. |

## Next steps

Learn more about VisualStudio.Extensibility by reading [Components of a VisualStudio.Extensibility extension](./extension-anatomy.md).
