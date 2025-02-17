---
categories:
  - cloud
  - processing
  - messaging
  - google cloud
  - log collection
ddtype: crawler
dependencies: []
description: Surveillez des métriques clés de Google Cloud PubSub.
doc_link: 'https://docs.datadoghq.com/integrations/google_cloud_pubsub/'
git_integration_title: google_cloud_pubsub
has_logo: true
integration_title: Google Pub/Sub
is_public: true
kind: integration
manifest_version: '1.0'
name: google_cloud_pubsub
public_title: Intégration Datadog/Google Pub/Sub
short_description: Surveillez des métriques clés de Google Cloud PubSub.
version: '1.0'
---
## Présentation
Google Cloud Pub/Sub apporte au cloud la flexibilité et la fiabilité des middlewares orientés message d'entreprise. 

Recueillez des métriques de Google Pub/Sub pour :

* Visualiser les performances de vos sujets et abonnements Pub/Sub
* Corréler les performances de vos sujets et abonnements Pub/Sub avec vos applications

## Implémentation
### Collecte de métriques
#### Installation

Si vous ne l'avez pas déjà fait, configurez d'abord [l'intégration Google Cloud Platform][1]. Aucune autre procédure d'installation n'est requise.

### Collecte de logs

Les logs de Google Cloud Pub/Sub sont recueillis via Stackdriver et envoyés à un Cloud Pub/Sub grâce à un redirecteur Push HTTP. Si vous ne l'avez pas déjà fait, configurez le [Cloud Pub/Sub grâce à un redirecteur Push HTTP][4].

Une fois cette opération effectuée, exportez vos logs de Google Cloud Pub/Sub depuis Stackdriver vers le Pub/sub :

1. Accédez à [la page Stackdriver][5] et filtrez les logs de Google Cloud Pub/Sub.
2. Cliquez sur **Create Export** et nommez le récepteur.
3. Choisissez Cloud Pub/Sub comme destination et sélectionnez le Pub/Sub créé à cette fin. **Remarque** : le Pub/Sub peut se situer dans un autre projet.

{{< img src="integrations/google_cloud_pubsub/export_pubsub_subscription.png" alt="Exportation des logs Google Cloud Pub/Sub vers Pub Sub" responsive="true">}}

4. Cliquez sur **Create** et attendez que le message de confirmation s'affiche.

## Données collectées
### Métriques
{{< get-metrics-from-git "google_cloud_pubsub" >}}


### Événements
L'intégration Google Cloud Pub/Sub n'inclut aucun événement.

### Checks de service
L'intégration Google Cloud Pub/Sub n'inclut aucun check de service.

## Dépannage
Besoin d'aide ? Contactez [l'assistance Datadog][3].

[1]: https://docs.datadoghq.com/fr/integrations/google_cloud_platform
[2]: https://github.com/DataDog/dogweb/blob/prod/integration/google_cloud_pubsub/google_cloud_pubsub_metadata.csv
[3]: https://docs.datadoghq.com/fr/help
[4]: https://docs.datadoghq.com/fr/integrations/google_cloud_platform/#log-collection
[5]: https://console.cloud.google.com/logs/viewer


{{< get-dependencies >}}