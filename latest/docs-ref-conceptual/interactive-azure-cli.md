---
title: "Интерактивный режим Azure CLI 2.0"
description: "Использование Azure CLI 2.0 в интерактивном режиме."
keywords: "Azure CLI 2.0, интерактивный режим"
author: rloutlaw
ms.author: routlaw
manager: douge
ms.date: 04/06/2017
ms.topic: article
ms.prod: azure
ms.technology: azure
ms.devlang: azurecli
ms.service: multiple
ms.assetid: 
ms.openlocfilehash: de6a366b84efa5475fd6146ff29c32e32dfe4672
ms.sourcegitcommit: f107cf927ea1ef51de181d87fc4bc078e9288e47
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2017
---
# <a name="interactive-azure-cli-20"></a>Интерактивный Azure CLI 2.0

Azure CLI 2.0 можно использовать в интерактивном режиме, выполнив команду `az interactive`.
Она позволяет вызвать интерактивную оболочку, в которой команды завершаются автоматически и предоставляется доступ к описанию команд и их параметров, а также к примерам команд.

![Интерактивный режим](./media/interactive-azure-cli/webapp-create.png)

> [!NOTE]
> Здесь мы не используем стиль по умолчанию, который неудобно читать, как и черный фон.

Если вы еще не вошли со своей учетной записью, выполните команду `login`, чтобы сделать это.

## <a name="configure"></a>Настройка

Интерактивный режим при необходимости позволяет отобразить описание команд, описание параметров и примеры команд.
Отображение описаний и примеров можно включить или отключить с помощью `F1`.

![Описания и примеры](./media/interactive-azure-cli/descriptions-and-examples.png)

Можно включить или отключить отображение значений параметров по умолчанию с помощью `F2`.

![Значения по умолчанию](./media/interactive-azure-cli/defaults.png)

`F3` переключает отображение некоторых ключевых жестов.

![Жесты](./media/interactive-azure-cli/gestures.png)

## <a name="scope"></a>Область

Вы можете задать область для интерактивного режима, указав определенную группу команд, например `vm` или `vm image`.
При этом будут интерпретироваться все команды в этой области.
Это заметно упрощает работу, если для нее вам требуется определенная группа команд.

Например, требуется ввести следующие команды.

```azurecli
az>> vm create -n myVM -g myRG --image UbuntuLTS
az>> vm list -o table
```

Вместо этого можно задать область для группы команд vm и ввести приведенные ниже команды.

```azurecli
az>> %%vm
az vm>> create -n myVM -g myRG --image UbuntuLTS
az vm>>list -o table
```

Кроме того, в качестве области можно задать группы команд более низкого уровня.
Для `vm image` можно задать область `%%vm image`.
В этом случае, так как мы уже задали `vm` в качестве области, можно было бы использовать `%%image`.

```azurecli
az vm>> %%image
az vm image>>
```

На этом этапе можно указать область `vm`, введя `%%..`, или указать корневой каталог, введя `%%`.

```azurecli
az vm image>> %%
az>>
```

## <a name="query"></a>Запрос

Можно выполнить запрос JMESPath к результатам последней выполненной команды.
Например, после создания виртуальной машины можно убедиться, что она полностью подготовлена.

```azurecli
az>> vm create --name myVM --resource-group myRG --image UbuntuLTS --no-wait
az>> ? [*].provisioningState
```

```
[
  "Creating"
]
```

Чтобы узнать больше о выполнении запросов результатов команд, ознакомьтесь с разделом [Использование запросов JMESPath в Azure CLI 2.0](query-azure-cli.md).

## <a name="bash-commands"></a>Команды Bash

Можно выполнять команды оболочки, не выходя из интерактивного режима, с помощью `#[cmd]`.

```azurecli
az>> #dir
```

## <a name="examples"></a>Примеры

Некоторые команды имеют множество примеров.
Вы можете прокрутить экран до следующей странице примеров с помощью `CTRL-N`, а до предыдущей страницы — с помощью `CTRL-Y`.

![Примеры](./media/interactive-azure-cli/examples.png)

Можно также изучить конкретный пример с помощью `::#`.

```azurecli
az>> vm create ::8
```