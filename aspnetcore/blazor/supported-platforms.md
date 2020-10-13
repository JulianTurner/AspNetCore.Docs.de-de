---
title: Unterstützte Plattformen für ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 1ffe98636ed200adbf00e89c2c3499eb69792d3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754540"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>Unterstützte Plattformen für ASP.NET Core Blazor

Von [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly und Blazor Server werden in den Browsern unterstützt, die in der folgenden Tabelle aufgelistet werden.

| Browser                          | Version         |
| -------------------------------- | --------------- |
| Apple Safari, einschließlich iOS      | Aktuell&dagger; |
| Google Chrome, einschließlich Android | Aktuell&dagger; |
| Microsoft Edge                   | Aktuell&dagger; |
| Mozilla Firefox                  | Aktuell&dagger; |  

&dagger;*Aktuell* bezieht sich auf die neueste Version des Browsers.  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| Browser                          | Version               |
| -------------------------------- | --------------------- |
| Apple Safari, einschließlich iOS      | Aktuell&dagger;       |
| Google Chrome, einschließlich Android | Aktuell&dagger;       |
| Microsoft Edge                   | Aktuell&dagger;       |
| Microsoft Internet Explorer      | Nicht unterstützt&Dagger; |
| Mozilla Firefox                  | Aktuell&dagger;       |  

&dagger;*Aktuell* bezieht sich auf die neueste Version des Browsers.  
&Dagger;Microsoft Internet Explorer unterstützt [WebAssembly](https://webassembly.org) nicht.

## Blazor Server

| Browser                          | Version         |
| -------------------------------- | --------------- |
| Apple Safari, einschließlich iOS      | Aktuell&dagger; |
| Google Chrome, einschließlich Android | Aktuell&dagger; |
| Microsoft Edge                   | Aktuell&dagger; |
| Microsoft Internet Explorer      | 11&Dagger;      |
| Mozilla Firefox                  | Aktuell&dagger; |

&dagger;*Aktuell* bezieht sich auf die neueste Version des Browsers.  
&Dagger;Zusätzliche Polyfills sind erforderlich. Beispielsweise können Zusagen über ein [`Polyfill.io`](https://polyfill.io/v3/)-Bundle hinzugefügt werden.

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
