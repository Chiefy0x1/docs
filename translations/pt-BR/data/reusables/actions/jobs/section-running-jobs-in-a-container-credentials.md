---
ms.openlocfilehash: 55bc7e2bd09fe50c60377a90473bfc36697d0ca5
ms.sourcegitcommit: fcf3546b7cc208155fb8acdf68b81be28afc3d2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2022
ms.locfileid: "145065422"
---
{% data reusables.actions.registry-credentials %}

#### Exemplo: Definindo credenciais para o registro de um contêiner 

{% raw %}
```yaml
container:
  image: ghcr.io/owner/image
  credentials:
     username: ${{ github.actor }}
     password: ${{ secrets.github_token }}
```
{% endraw %}
