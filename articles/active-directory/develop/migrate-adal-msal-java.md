---
title: ADAL 到 MSAL 的迁移指南（MSAL4j） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何将 Azure Active Directory 身份验证库 (ADAL) Java 应用迁移到 Microsoft 身份验证库 (MSAL)。
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: 7729a30acb1b191378960887164bb4b32e225c36
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82128008"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>适用于 Java 的 ADAL 到 MSAL 迁移指南

本文重点介绍需要做出哪些更改，才能迁移使用 Azure Active Directory 身份验证库 (ADAL) 的应用，使之使用 Microsoft 身份验证库 (MSAL)。

适用于 Java 的 Microsoft 身份验证库 (MSAL4J) 与适用于 Java 的 Azure AD 身份验证库 (ADAL4J) 用于对 Azure AD 实体进行身份验证，以及从 Azure AD 请求令牌。 截止目前，大多数开发人员都是通过 Azure AD 身份验证库 (ADAL) 来请求令牌，使用面向开发人员的 Azure AD 平台 (v1.0) 来对 Azure AD 标识（工作和学校帐户）进行身份验证。

MSAL 提供以下优势：

- 由于它使用较新的 Microsoft 标识平台终结点，因此你可以通过 Azure AD 企业到消费者（B2C）来验证一组更广泛的 Microsoft 标识，如 Azure AD 标识、Microsoft 帐户以及社交和本地帐户。
- 用户将获得最佳单一登录体验。
- 你的应用程序可以启用增量许可，并支持条件性访问。

MSAL for Java 是我们建议用于 Microsoft 标识平台的身份验证库。 我们不会对 ADAL4J 实现任何新的功能。 今后我们的所有努力都重在改进 MSAL。

## <a name="differences"></a>差异

如果你一直在使用面向开发人员的 Azure AD (v1.0) 终结点（和 ADAL4J），请阅读[ Microsoft 标识平台 (v2.0) 终结点有何不同？](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

## <a name="scopes-not-resources"></a>范围不是资源

ADAL4J 获取资源的令牌，而 MSAL for Java 则是获取范围的令牌。 许多 MSAL for Java 类需要 scopes 参数。 此参数是一个字符串列表，这些字符串声明所需的权限和请求的资源。 请参阅 [Microsoft Graph 的范围](https://docs.microsoft.com/graph/permissions-reference)查看示例范围。

可以将`/.default`范围后缀添加到资源中，以帮助将应用从 v2.0 终结点（ADAL）迁移到 Microsoft 标识平台终结点（MSAL）。 例如，对于的资源值`https://graph.microsoft.com`，等效的作用域值为。 `https://graph.microsoft.com/.default`  如果资源不是 URL 格式，而是窗体`XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX`的资源 ID，则仍可以使用范围值。 `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default`

有关不同类型的作用域的详细信息，请参阅[Microsoft 标识平台中的权限和许可](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)和[接受 V1.0 令牌的 Web API 的范围](https://docs.microsoft.com/azure/active-directory/develop/msal-v1-app-scopes)。

## <a name="core-classes"></a>核心类

在 ADAL4J 中，`AuthenticationContext` 类代表通过颁发机构与安全令牌服务 (STS) 或授权服务器建立的连接。 但是，MSAL for Java 是围绕客户端应用程序设计的。 它提供两个不同的类（`PublicClientApplication` 和 `ConfidentialClientApplication`）来代表客户端应用程序。  后者 (`ConfidentialClientApplication`) 代表的应用程序旨在安全维护机密，例如守护程序应用的应用程序标识符。

下表显示了 ADAL4J 函数到新 MSAL for Java 函数的映射：

| ADAL4J 方法| MSAL4J 方法|
|------|-------|
|acquireToken(String resource, ClientCredential credential, AuthenticationCallback callback) | acquireToken(ClientCredentialParameters)|
|acquireToken(String resource, ClientAssertion assertion, AuthenticationCallback callback)|acquireToken(ClientCredentialParameters)|
|acquireToken(String resource, AsymmetricKeyCredential credential, AuthenticationCallback callback)|acquireToken(ClientCredentialParameters)|
|acquireToken(String resource, String clientId, String username, String password, AuthenticationCallback callback)| acquireToken(UsernamePasswordParameters)|
|acquireToken(String resource, String clientId, String username, String password=null, AuthenticationCallback callback)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken(String resource, UserAssertion userAssertion, ClientCredential credential, AuthenticationCallback callback)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode() and acquireTokenByDeviceCode()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount 而不是 IUser

ADAL4J 操控用户。 尽管用户代表单个个人或软件代理，但用户可以在 Microsoft 标识系统中有一个或多个帐户。 例如，用户可能有多个 Azure AD、Azure AD B2C 或 Microsoft 个人帐户。

MSAL for Java 通过 `IAccount` 接口定义帐户的概念。 这是 ADAL4J 中的一项中断性变更，但这项变更是积极性的，因为它捕获到这样一个事实：同一用户可以有多个帐户，甚至是不同 Azure AD 目录中的帐户。 由于系统会提供主帐户信息，MSAL for Java 可以在来宾方案中提供更有用的信息。

## <a name="cache-persistence"></a>缓存持久性

ADAL4J 不支持令牌缓存。
MSAL for Java 添加了[令牌缓存](msal-acquire-cache-tokens.md)，在可能的情况下，它会自动刷新已过期的令牌，并防止不必要地提示用户提供凭据，以此简化令牌生存期的管理。

## <a name="common-authority"></a>通用颁发机构

在 v1.0 中，如果你使用 `https://login.microsoftonline.com/common` 颁发机构，则用户可以使用任何 Azure Active Directory (AAD) 帐户（适用于任何组织）登录。

如果你使用的`https://login.microsoftonline.com/common`是 v2.0 中的权限，则用户可以使用任何 AAD 组织，甚至 Microsoft 个人帐户（MSA）登录。 在 MSAL for Java 中，若要限制登录到任何 AAD 帐户，则需要使用 `https://login.microsoftonline.com/organizations` 颁发机构（与 ADAL4J 中的行为相同）。 若要指定颁发机构，请在创建 `PublicClientApplication` 类时，在 [PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) 方法中设置 `authority` 参数。

## <a name="v10-and-v20-tokens"></a>v1.0 和 v2.0 令牌

v1.0 终结点（由 ADAL 使用）只发出 v1.0 令牌。

v2.0 终结点（由 MSAL 使用）可以发出 v1.0 和 v2.0 令牌。 使用 web API 的应用程序清单的属性，开发人员可以选择接受的令牌版本。 请`accessTokenAcceptedVersion`参阅[应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)参考文档中的。

有关1.0 和 v2.0 令牌的详细信息，请参阅[Azure Active Directory 访问令牌](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)。

## <a name="adal-to-msal-migration"></a>ADAL 到 MSAL 的迁移

在 ADAL4J 中会公开刷新令牌 -- 允许开发人员缓存这些令牌。 然后，开发人员可使用 `AcquireTokenByRefreshToken()` 来实现各种解决方案，例如，实现长时间运行的服务，以便在用户不再保持连接时，代表用户刷新仪表板。

出于安全原因，MSAL for Java 不公开刷新令牌。 而是由 MSAL 代你处理令牌刷新。

使用 MSAL for Java 提供的 [acquireToken(RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-) API，可将通过 ADAL4j 获取的刷新令牌迁移到 ClientApplication 中。 使用此方法可以结合所需的任何范围（资源）提供以前用过的刷新令牌。 该刷新令牌将交换一个新令牌，并缓存供应用程序使用。

以下代码片段演示了机密客户端应用程序中的一些迁移代码：

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

`IAuthenticationResult` 返回访问令牌和 ID 令牌，新的刷新令牌将存储在缓存中。
应用程序现在还包含一个 IAccount：

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

若要使用现已进入缓存中的令牌，请调用：

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
