---
title: Управление подписками Azure с помощью Azure CLI
description: Управление подписками Azure с помощью Azure CLI.
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/09/2018
ms.topic: conceptual
ms.product: azure
ms.technology: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 4c196b8376b0044e315cfee2b3958f2f4b476df7
ms.sourcegitcommit: 7f79860c799e78fd8a591d7a5550464080e07aa9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56158673"
---
# <a name="use-multiple-azure-subscriptions"></a>Использование нескольких подписок Azure

Большинство пользователей Azure обычно используют только одну подписку. Но если вы работаете в нескольких организациях или если доступ к определенным ресурсам в вашей организации разделен по группам, у вас может быть несколько подписок Azure. CLI поддерживает выбор подписки как глобально, так и для отдельных команд.

## <a name="tenants-users-and-subscriptions"></a>Клиенты, пользователи и подписки

При определении различий между клиентами, пользователями и подписками в Azure может возникнуть путаница. _Клиент_ — это сущность Azure Active Directory, которая условно представляет всю организацию. Клиент предполагает наличие минимум одной _подписки_ и одного _пользователя_. Пользователь — это человек, который связан только с одним клиентом — организацией, в которой он работает. Пользователи — это учетные записи для входа в Azure, которые используются, чтобы создавать, использовать и администрировать ресурсы.
Пользователь может иметь доступ к нескольким _подпискам_. Подписки — это соглашения с корпорацией Майкрософт на использование облачных служб, в том числе Azure. Каждый ресурс связан с подпиской.

Дополнительные сведения о различиях между клиентами, пользователями и подписками см. в [словаре терминов, связанных с облачной платформой Azure](/azure/azure-glossary-cloud-terminology).  Чтобы узнать, как добавить новую подписку в клиент Azure Active Directory, см. статью [Как добавить подписку Azure в Azure Active Directory](/azure/active-directory/active-directory-how-subscriptions-associated-directory).
Сведения о том, как выполнить вход от имени определенного клиента см. в статье [Вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="change-the-active-subscription"></a>Изменение активной подписки

Для доступа к ресурсам определенной подписки измените активную подписку или используйте аргумент `--subscription`. Изменение подписки для всех команд выполняется с помощью команды [az account set](/cli/azure/account#az-account-set).

Чтобы изменить активную подписку:

1. Получите список своих подписок с помощью команды [az account list](/cli/azure/account#az-account-list):

    ```azurecli-interactive
    az account list --output table
    ```
2. Выполните команду `az account set`, указав идентификатор подписки или имя, на которое нужно переключиться.

    ```azurecli-interactive
    az account set --subscription "My Demos"
    ```

Для выполнения только одной команды с другой подпиской, используйте аргумент `--subscription`. Этот аргумент принимает идентификатор или имя подписки:

```azurecli-interactive
az vm create --subscription "My Demos" --resource-group MyGroup --name NewVM --image Ubuntu
```
