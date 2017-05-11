---
title: "Создание субъекта-службы Azure с помощью Azure CLI 2.0"
description: "Узнайте, как создать субъект-службу для приложения или службы с помощью Azure CLI 2.0."
keywords: "Azure CLI 2.0, Azure Active Directory, Azure Active Directory, AD, RBAC"
author: rloutlaw
ms.author: routlaw
manager: douge
ms.date: 02/27/2017
ms.topic: article
ms.prod: azure
ms.technology: azure
ms.devlang: azurecli
ms.service: multiple
ms.assetid: fab89cb8-dac1-4e21-9d34-5eadd5213c05
ms.openlocfilehash: 10a168ae0c33207905d58b7b57ac9ad76d8d9bf4
ms.sourcegitcommit: 73a73c8a17d95b116d33eee3287d938addc5c0ac
ms.translationtype: HT
ms.contentlocale: ru-RU
---
# <a name="create-an-azure-service-principal-with-azure-cli-20"></a>Создание субъекта-службы Azure с помощью Azure CLI 2.0

Если вы планируете управлять приложением или службой с помощью Azure CLI 2.0, вы должны запустить это приложение или службу с помощью субъекта-службы Azure Active Directory (AAD), а не своих учетных данных.
В этой статье объясняется, как создать субъект безопасности с помощью Azure CLI 2.0.

> [!NOTE]
> Можно также создать субъект-службу на портале Azure.
> Дополнительные сведения см. в статье [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](/azure/azure-resource-manager/resource-group-create-service-principal-portal).

## <a name="what-is-a-service-principal"></a>Что такое субъект-служба?

Субъект-служба Azure — это идентификатор безопасности, который используют созданные пользователем приложения, службы и средства автоматизации для доступа к определенным ресурсам Azure. Это что-то вроде удостоверения пользователя (имя для входа и пароль или сертификат) с определенной ролью и строго контролируемыми разрешениями на доступ к ресурсам. Субъект-служба должен выполнять только конкретные задачи, в отличие от удостоверений пользователей. Для повышения безопасности следует предоставить ему только минимально необходимый уровень разрешений для выполнения задач управления. 

Сейчас Azure CLI 2.0 поддерживает только создание учетных данных с проверкой подлинности на основе пароля. В этой статье мы рассмотрим создание субъекта-службы с определенным паролем и дополнительное назначение ему конкретных ролей.

## <a name="verify-your-own-permission-level"></a>Проверка вашего уровня разрешений

Во-первых, у вас должен быть достаточный уровень разрешений в Azure Active Directory и подписке Azure. В частности, вы должны иметь право на создание приложения в Active Directory и назначение роли субъекту-службе. 

Проверить, есть ли у вас соответствующие разрешения, проще всего на портале. Ознакомьтесь с [проверкой наличия необходимых разрешений на портале](/azure/azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions).

## <a name="create-a-service-principal-for-your-application"></a>Создание субъекта-службы для приложения

Необходимо иметь один из следующих параметров для определения приложения, которое нужно создать для субъекта-службы:

  * уникальное имя или URI развернутого приложения (например, MyDemoWebApp в следующих примерах);
  * идентификатор приложения — глобальный уникальный идентификатор, связанный с развернутым приложением, службой или объектом.

Эти значения идентифицируют ваше приложение при создании субъекта-службы.

### <a name="get-information-about-your-application"></a>Получение сведений о приложении

Получить сведения для идентификации приложения можно с помощью команды `az ad app list`.

```azurecli
az ad app list --display-name MyDemoWebApp
```

```json
{
    "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
    "appPermissions": null,
    "availableToOtherTenants": false,
    "displayName": "MyDemoWebApp",
    "homepage": "http://MyDemoWebApp.azurewebsites.net",
    "identifierUris": [
      "http://MyDemoWebApp"
    ],
    "objectId": "bd07205b-629f-4a2e-945e-1ee5dadf610b9",
    "objectType": "Application",
    "replyUrls": []
  }
```

Параметр `--display-name` фильтрует возвращаемый список приложений, чтобы показать те, которые содержат `displayName`, начиная с MyDemoWebApp.

### <a name="create-the-service-principal"></a>Создание субъекта-службы

Создайте субъект-службу с помощью команды [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac). 

```azurecli
az ad sp create-for-rbac --name {appId} --password "{strong password}" 
``` 

```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "MyDemoWebApp",
  "name": "http://MyDemoWebApp",
  "password": {strong password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```

 > [!WARNING] 
 > Не используйте ненадежный пароль.  Следуйте руководству по [правилам и ограничениям для паролей Azure AD](/azure/active-directory/active-directory-passwords-policy).

### <a name="get-information-about-the-service-principal"></a>Получение сведений о субъекте-службе

```azurecli
az ad sp show --id a487e0c1-82af-47d9-9a0b-af184eb87646d
```

```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "MyDemoWebApp",
  "objectId": "0ceae62e-1a1a-446f-aa56-2300d176659bde",
  "objectType": "ServicePrincipal",
  "servicePrincipalNames": [
    "http://MyDemoWebApp",
    "a487e0c1-82af-47d9-9a0b-af184eb87646d"
  ]
}
```

### <a name="sign-in-using-the-service-principal"></a>Вход с помощью субъекта-службы

Теперь вы можете войти от имени нового субъекта-службы вашего приложения, используя *идентификатор приложения* и *пароль*, указанные в команде `az ad sp show`.  Укажите значение параметра *tenant*, полученное в результате выполнения команды `az ad sp create-for-rbac`.

```azurecli
az login --service-principal -u a487e0c1-82af-47d9-9a0b-af184eb87646d --password {password} --tenant {tenant}
``` 

После успешного входа отобразится следующее:

```json
[
  {
    "cloudName": "AzureCloud",
    "id": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
    "isDefault": true,
    "state": "Enabled",
    "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
    "user": {
      "name": "https://MyDemoWebApp",
      "type": "servicePrincipal"
    }
  }
]
```

Используйте значения `id`, `password` и `tenant` в качестве учетных данных для запуска приложения. 

## <a name="managing-roles"></a>Управление ролями 

> [!NOTE]
> Управление доступом на основе ролей в Azure (RBAC) — это модель для определения ролей пользователя и субъектов-служб и управления этими ролями.
> Ролям назначаются связанные с ними наборы разрешений, определяющие ресурсы, которые доступны субъекту для чтения, доступа, записи или управления.
> Дополнительные сведения о RBAC и ролях см. в статье [Встроенные роли для управления доступом на основе ролей в Azure](/azure/active-directory/role-based-access-built-in-roles).

В Azure CLI 2.0 доступны следующие команды для управления назначением ролей:

* [az role assignment list](/cli/azure/role/assignment#list);
* [az role assignment create](/cli/azure/role/assignment#create);
* [az role assignment delete](/cli/azure/role/assignment#delete).

По умолчанию субъекту-службе назначена роль **участника**. Возможно, это не лучший выбор для взаимодействия приложения со службами Azure, учитывая широкие разрешения у этой роли. Роль **читателя** имеет больше ограничений и отлично подходит для доступа с правами только для чтения. Вы можете просмотреть сведения о разрешениях каждой роли или создать пользовательские разрешения на портале Azure.

В этом примере мы добавим роль **читателя** к предыдущему примеру и удалим роль **участника**:

```azurecli
az role assignment create --assignee a487e0c1-82af-47d9-9a0b-af184eb87646d --role Reader
az role assignment delete --assignee a487e0c1-82af-47d9-9a0b-af184eb87646d --role Contributor
```

Проверьте изменения, отобразив текущие назначенные роли:

```azurecli
az role assignment list --assignee a487e0c1-82af-47d9-9a0b-af184eb87646d
```

```json
{
    "id": "/subscriptions/34345f33-0398-4a99-a42b-f6613d1664ac/providers/Microsoft.Authorization/roleAssignments/c27f78a7-9d3b-404b-ab59-47818f9af9ac",
    "name": "c27f78a7-9d3b-404b-ab59-47818f9af9ac",
    "properties": {
      "principalId": "790525226-46f9-4051-b439-7079e41dfa31",
      "principalName": "http://MyDemoWebApp",
      "roleDefinitionId": "/subscriptions/34345f33-0398-4a99-a42b-f6613d1664ac/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
      "roleDefinitionName": "Reader",
      "scope": "/subscriptions/34345f33-0398-4a99-a42b-f6613d1664ac"
    },
    "type": "Microsoft.Authorization/roleAssignments"
}
```

> [!NOTE] 
> Если у вашей учетной записи нет достаточных разрешений для назначения ролей, вы получите сообщение об ошибке.
> В нем будет указано, что ваша учетная запись не авторизована для выполнения действия "Microsoft.Authorization/roleAssignments/write в области /subscriptions/{guid}".
   
## <a name="change-the-credentials-of-a-security-principal"></a>Изменение учетных данных субъекта безопасности

Из соображений безопасности рекомендуется регулярно просматривать разрешения и обновлять пароли. Можно также изменять учетные данные безопасности и управлять ими по мере изменения приложения.

### <a name="reset-a-service-principal-password"></a>Сброс пароля субъекта-службы

Для сброса текущего пароля субъекта-службы используйте команду `az ad sp reset-credentials`.

```azurecli
az ad sp reset-credentials --name 20bce7de-3cd7-49f4-ab64-bb5b443838c3 --password {new-password}
```

```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "name": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "password": {new-password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```

Интерфейс командной строки создаст надежный пароль, если не указывать параметр `--password`.