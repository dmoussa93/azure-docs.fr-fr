---
title: Transférer des données de travaux Azure Automation aux journaux d’activité Azure Monitor
description: Cet article montre comment envoyer des données d’état de travaux et des flux de travaux de runbook à des journaux Azure Monitor afin de renforcer la visibilité et la simplicité de gestion.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: 54f77f55a127cd712d43419eb6a85fd5d93a478c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652175"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Transférer l’état d’un travail et des flux de travail d’Automation vers des journaux Azure Monitor

Automation peut envoyer un état de tâche du runbook et des flux de tâches à votre espace de travail Log Analytics. Ce processus n’implique aucune liaison d’espace de travail et est complètement indépendant. Les journaux d’activité de travail et les flux de travail sont visibles dans le portail Azure, ou avec PowerShell, pour des travaux individuels. Cela vous permet d’effectuer des enquêtes simples. Avec les journaux Azure Monitor, vous pouvez désormais :

* Obtenir des informations sur vos travaux Automation.
* Déclencher un e-mail ou une alerte en fonction du statut de votre travail de runbook (par exemple, échec ou état suspendu).
* Écrire des requêtes avancées sur plusieurs flux de travaux.
* Mettre en corrélation des travaux sur différents comptes Automation.
* Visualiser votre historique des travaux dans le temps.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Conditions préalables et considérations relatives au déploiement

Pour commencer à envoyer vos journaux d’activité Automation à des journaux Azure Monitor, vous devez disposer des éléments suivants :

* La version la plus récente d’[Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Un espace de travail Log Analytics. Pour plus d’informations, consultez [Prise en main des journaux Azure Monitor](../log-analytics/log-analytics-get-started.md).
* L’ID de ressource de votre compte Azure Automation.

Utilisez la commande suivante pour rechercher l’ID de ressource de votre compte Azure Automation :

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Pour rechercher l’ID de ressource de votre espace de travail Log Analytics, exécutez la commande PowerShell suivante :

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Si vous disposez de plusieurs comptes Automation ou plusieurs espaces de travail, recherchez le nom à configurer dans la sortie des commandes précédentes et copiez la valeur de l'ID de ressource.

1. Sélectionnez votre compte Automation à partir du panneau **Compte Automation** du portail Azure, puis **Tous les paramètres**. 
2. Dans le panneau **Tous les paramètres**, sous **Paramètres du compte**, sélectionnez **Propriétés**.  
3. Dans le panneau **Propriétés**, notez ces valeurs.<br> ![Propriétés du compte Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)


## <a name="azure-monitor-log-records"></a>Enregistrements de journaux Azure Monitor

Les diagnostics d’Azure Automation créent deux types d’enregistrements dans les journaux Azure Monitor, marqué comme `AzureDiagnostics`. Les tableaux des sections suivantes sont des exemples d’enregistrements générés par Azure Automation et de types de données s'affichant dans les résultats de recherche dans les journaux.

### <a name="job-logs"></a>Journaux d’activité de tâches

| Propriété | Description |
| --- | --- |
| TimeGenerated |Date et heure d’exécution du travail du runbook. |
| RunbookName_s |Nom du runbook. |
| Caller_s |Appelant ayant initié l’opération. Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés. |
| Tenant_g | GUID identifiant le locataire pour l’appelant. |
| JobId_g |GUID identifiant le travail du runbook. |
| ResultType |L’état du travail du runbook. Les valeurs possibles sont les suivantes :<br>- Nouveau<br>Créé<br>Démarré<br>Arrêté<br>Interrompu<br>Échec<br>- Terminé |
| Category | Classification du type de données. Pour Automation, la valeur est JobLogs. |
| NomOpération | Type d’opération exécutée dans Azure. Pour Automation, la valeur est Job. |
| Ressource | Nom du compte Automation |
| SourceSystem | Système utilisé par les journaux d'activité Azure Monitor pour collecter les données. La valeur est toujours Azure pour les diagnostics Azure. |
| resultDescription |État résultant du travail du runbook. Les valeurs possibles sont les suivantes :<br>- Job is started<br>- Job Failed<br>- Job Completed |
| CorrelationId |ID de corrélation du travail du runbook. |
| ResourceId |ID de ressource de compte Azure Automation du runbook. |
| SubscriptionId | GUID d’abonnement Azure pour le compte Automation. |
| ResourceGroup | Nom du groupe de ressources du compte Automation. |
| ResourceProvider | Fournisseur de ressources. La valeur est MICROSOFT.AUTOMATION. |
| ResourceType | Type de ressource. La valeur est AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Flux de tâches
| Propriété | Description |
| --- | --- |
| TimeGenerated |Date et heure d’exécution du travail du runbook. |
| RunbookName_s |Nom du runbook. |
| Caller_s |Appelant ayant initié l’opération. Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés. |
| StreamType_s |Type de flux de travail. Les valeurs possibles sont les suivantes :<br>progress<br>Sortie<br>Avertissement<br>error<br>DEBUG<br>- Verbose |
| Tenant_g | GUID identifiant le locataire pour l’appelant. |
| JobId_g |GUID identifiant le travail du runbook. |
| ResultType |L’état du travail du runbook. Les valeurs possibles sont les suivantes :<br>- In Progress |
| Category | Classification du type de données. Pour Automation, la valeur est JobStreams. |
| NomOpération | Type d’opération exécutée dans Azure. Pour Automation, la valeur est Job. |
| Ressource | Nom du compte Automation |
| SourceSystem | Système utilisé par les journaux d'activité Azure Monitor pour collecter les données. La valeur est toujours Azure pour les diagnostics Azure. |
| resultDescription |Description incluant le flux de sortie du runbook. |
| CorrelationId |ID de corrélation du travail du runbook. |
| ResourceId |ID de ressource de compte Azure Automation du runbook. |
| SubscriptionId | GUID d’abonnement Azure pour le compte Automation. |
| ResourceGroup | Nom du groupe de ressources du compte Automation. |
| ResourceProvider | Fournisseur de ressources. La valeur est MICROSOFT.AUTOMATION. |
| ResourceType | Type de ressource. La valeur est AUTOMATIONACCOUNTS. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Configuration de l’intégration aux journaux Azure Monitor

1. Sur votre ordinateur, démarrez Windows PowerShell à partir de l’écran **Démarrer**.
2. Exécutez les commandes PowerShell suivantes, et remplacez la valeur de `[your resource ID]` et de `[resource ID of the log analytics workspace]` par celles de la section précédente.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Une fois ce script exécuté, il peut s’écouler une heure avant que les enregistrements soient visibles dans les journaux Azure Monitor suivant l’écriture des nouveaux `JobLogs` ou `JobStreams`.

Pour afficher les journaux d’activité, exécutez la requête suivante dans la recherche de journal d’activité de Log Analytics : `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Vérifier la configuration

Pour vous assurer que votre compte Automation envoie des journaux d’activité à votre espace de travail Log Analytics, vérifiez que les diagnostics sont correctement configurés sur le compte Automation à l’aide de la commande PowerShell suivante.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Dans la sortie, assurez-vous que :

* Sous `Logs`, la valeur de `Enabled` est True.
* `WorkspaceId` est défini sur la valeur `ResourceId` pour votre espace de travail Log Analytics.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Affichage des journaux Automation dans les journaux Azure Monitor

Après avoir commencé à envoyer des journaux d’activité de travaux Automation dans des journaux Azure Monitor, nous allons voir comment les utiliser dans les journaux Azure Monitor.

Pour afficher les journaux d’activité, exécutez la requête suivante :`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Envoyer un e-mail en cas d’échec ou de suspension d’un travail de runbook

Nos clients demandent souvent à pouvoir envoyer un e-mail ou un SMS lorsqu’une erreur survient sur un travail de runbook.

Pour créer une règle d’alerte, commencez par créer une recherche de journal pour les enregistrements de travaux de runbook qui doivent appeler l’alerte. Cliquez sur le bouton **Alerte** pour créer et configurer la règle d’alerte.

1. Dans la page de présentation de l’espace de travail Log Analytics, cliquez sur **Afficher les journaux**.
2. Créez une requête de recherche dans les journaux pour votre alerte en tapant la recherche suivante dans le champ de requête : `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Vous pouvez également effectuer un regroupement par nom de runbook en utilisant : `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Si vous avez configuré des journaux d’activité dans votre espace de travail sur plusieurs abonnements ou comptes Automation, vous pouvez également regrouper vos alertes par abonnement ou par compte Automation. Le nom du compte Automation se trouve dans le champ `Resource` de la recherche de `JobLogs`.
3. Pour ouvrir l’écran **Créer une règle**, cliquez sur **+ Nouvelle règle d’alerte** en haut de la page. Pour plus d’informations sur les options de configuration de l’alerte, voir [Alertes de journaux dans Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Rechercher tous les travaux qui ont rencontré des erreurs

En plus des alertes concernant les échecs, vous pouvez déterminer lorsqu’une tâche de runbook comporte une erreur sans fin d’exécution. Dans ce cas, PowerShell génère un flux d’erreur, mais les erreurs sans fin d’exécution ne provoquent la suspension ou l’échec du travail.

1. Dans votre espace de travail Log Analytics, cliquez sur **Journaux**.
2. Dans le champ de requête de recherche, entrez `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`.
3. Cliquez sur le bouton **Rechercher**.

### <a name="view-job-streams-for-a-job"></a>Afficher les flux de travail pour un travail

Lorsque vous déboguez un travail, vous pouvez également examiner les flux de travaux. La requête ci-dessous montre tous les flux pour un seul travail avec le GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 :

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Afficher l’état de travail historique

Vous pouvez enfin souhaiter visualiser l’historique de vos travaux dans le temps. Vous pouvez utiliser cette requête pour rechercher l’état de vos travaux au fil du temps.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Graphique de l’état de la tâche historique de Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Suppression des paramètres de diagnostic

Pour supprimer les paramètres de diagnostic du compte Automation, exécutez la commande suivante :

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Résumé

L’envoi de vos données de diffusion en continu et d’état des travaux Automation aux journaux Azure Monitor permet d’obtenir plus de détails sur l’état de vos travaux Automation en :
+ Configurant des alertes pour vous avertir en cas de problème.
+ Utilisant des vues et des requêtes de recherche personnalisées pour visualiser les résultats de votre runbook, l’état du travail de runbook et d’autres indicateurs ou mesures clés associées.

Les journaux Azure Monitor offrent une plus grande visibilité opérationnelle sur vos travaux Automation et peuvent permettre de traiter les incidents plus rapidement.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir de l’aide sur la résolution des problèmes Log Analytics, consultez [Dépannage si Log Analytics ne collecte plus de données](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Pour savoir comment construire différentes requêtes de recherche et consulter les journaux d’activité de travaux Automation avec les journaux Azure Monitor, consultez la page [Recherches dans les journaux d’activité dans les journaux Azure Monitor](../log-analytics/log-analytics-log-searches.md).
* Pour savoir comment créer et récupérer la sortie et les messages d’erreur de runbooks, consultez la page [Sortie et messages de runbooks](automation-runbook-output-and-messages.md).
* Pour plus d’informations sur l’exécution d’un runbook, la manière de surveiller des tâches de runbook et autres détails techniques, voir [Suivi d’une tâche de runbook](automation-runbook-execution.md).
* Pour plus d’informations sur les journaux Azure Monitor et sur les sources de collecte de données, consultez la page [Collecter des données de stockage Azure dans les journaux Azure Monitor – Vue d’ensemble](../azure-monitor/platform/collect-azure-metrics-logs.md).

