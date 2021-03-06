---
title: 'Modèle : Opérateur count dans une définition de stratégie'
description: Ce modèle Azure Policy fournit un exemple d’utilisation de l’opérateur count dans une définition de stratégie.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 88c2d1083a92732ac56ca4d6da7087cc4220d9a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77170377"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Modèle Azure Policy : Opérateur count

L’opérateur [count](../concepts/definition-structure.md#count) évalue les membres d’un alias \[\*\].

## <a name="sample-policy-definition"></a>Exemple de définition de stratégie

Cette définition de stratégie [audite](../concepts/effects.md#audit) des groupes de sécurité réseau configurés pour autoriser le trafic entrant du protocole RDP (Remote Desktop Protocol).

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Explication

Les principaux composants de l’opérateur **count** sont _field_, _where_ et la condition. Chacun est mis en surbrillance dans l’extrait de code ci-dessous.

- _field_ indique à count de quel [alias](../concepts/definition-structure.md#aliases) il faut évaluer les membres. Ici, nous examinons l’alias **securityRules\[\*\]** _array_ du groupe de sécurité réseau.
- _where_ utilise le langage de stratégie pour définir quels membres d’_array_ remplissent les critères. Dans cet exemple, un opérateur logique **allOf** regroupe trois évaluations de condition différentes de propriétés d’alias _array_ : _direction_, _access_ et _destinationPortRange_.
- La condition count dans cet exemple est **greater**. Count prend la valeur true quand un ou plusieurs membres de l’alias _array_ correspondent à la clause _where_.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue les autres [modèles et définitions intégrées](./index.md).
- Consultez la [Structure de définition Azure Policy](../concepts/definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](../concepts/effects.md).