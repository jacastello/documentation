---
assets:
  dashboards: {}
  monitors: {}
  service_checks: assets/service_checks.json
categories:
  - collaboration
  - source control
  - issue tracking
  - log collection
creates_events: false
ddtype: check
dependencies:
  - 'https://github.com/DataDog/integrations-core/blob/master/gitlab/README.md'
display_name: Gitlab
git_integration_title: gitlab
guid: 1cab328c-5560-4737-ad06-92ebc54af901
integration_id: gitlab
integration_title: Gitlab
is_public: true
kind: integration
maintainer: help@datadoghq.com
manifest_version: 1.0.0
metric_prefix: gitlab.
metric_to_check: go_gc_duration_seconds
name: gitlab
public_title: Intégration Datadog/Gitlab
short_description: Surveillez toutes vos métriques Gitlab avec Datadog.
support: core
supported_os:
  - linux
  - mac_os
  - windows
---
## Présentation

Grâce à cette intégration, vous pouvez :

* Visualiser et surveiller des métriques recueillies via Gitlab par l'intermédiaire de Prometheus

Consultez la [documentation de Gitlab][1] (en anglais) pour en savoir plus sur Gitlab et sur son intégration à Prometheus.

## Implémentation

Suivez les instructions ci-dessous pour installer et configurer ce check lorsque l'Agent est exécuté sur un host. Consultez la [documentation relative aux modèles d'intégration Autodiscovery][2] pour découvrir comment appliquer ces instructions à un environnement conteneurisé.

### Installation

Le check Gitlab est inclus avec le paquet de l'[Agent Datadog][3] : vous n'avez donc rien d'autre à installer sur vos serveurs Gitlab.

### Configuration

Modifiez le fichier `gitlab.d/conf.yaml` dans le dossier `conf.d/` à la racine du [répertoire de configuration de votre Agent][4] afin de spécifier l'endpoint de métriques Prometheus de Gitlab.
Consultez le [fichier d'exemple gitlab.d/conf.yaml][5] pour découvrir toutes les options de configuration disponibles.

**Remarque** : l'élément `allowed_metrics` de la section `init_config` vous permet d'indiquer les métriques à extraire.


#### Collecte de logs

**Disponible à partir des versions > 6.0 de l'Agent**

1. La collecte de logs est désactivée par défaut dans l'Agent Datadog. Vous devez l'activer dans `datadog.yaml` :

    ```yaml
      logs_enabled: true
    ```

2. Modifiez ensuite `gitlab.d/conf.yaml` en supprimant la mise en commentaire des lignes `logs` en bas du fichier. Mettez à jour la ligne `path` en indiquant le bon chemin vers vos fichiers de log Gitlab.

    ```
      logs:
        - type: file
          path: /var/log/gitlab/gitlab-rails/production_json.log
          service: <SERVICE_NAME>
          source: gitlab
        - type: file
          path: /var/log/gitlab/gitlab-rails/production.log
          service: <SERVICE_NAME>
          source: gitlab
        - type: file
          path: /var/log/gitlab/gitlab-rails/api_json.log
          service: <SERVICE_NAME>
          source: gitlab
    ```

3. [Redémarrez l'Agent][9].

### Validation

[Lancez la sous-commande status de l'Agent][6] et cherchez `gitlab` dans la section Checks.

## Données collectées
### Métriques
{{< get-metrics-from-git "gitlab" >}}


### Événements
Le check Gitlab n'inclut aucun événement.

### Checks de service
Le check Gitlab comprend un check de service relatif à la disponibilité et à l'état d'exécution du service.
En outre, il fournit un check de service pour s'assurer que l'endpoint Prometheus local est disponible.

## Dépannage
Besoin d'aide ? Contactez [l'assistance Datadog][8].

[1]: https://docs.gitlab.com/ee/administration/monitoring/prometheus
[2]: https://docs.datadoghq.com/fr/agent/autodiscovery/integrations
[3]: https://app.datadoghq.com/account/settings#agent
[4]: https://docs.datadoghq.com/fr/agent/guide/agent-configuration-files/?tab=agentv6#agent-configuration-directory
[5]: https://github.com/DataDog/integrations-core/blob/master/gitlab/datadog_checks/gitlab/data/conf.yaml.example
[6]: https://docs.datadoghq.com/fr/agent/guide/agent-commands/?tab=agentv6#agent-status-and-information
[7]: https://github.com/DataDog/integrations-core/blob/master/gitlab/metadata.csv
[8]: https://docs.datadoghq.com/fr/help
[9]: https://docs.datadoghq.com/fr/agent/guide/agent-commands/?tab=agentv6#start-stop-and-restart-the-agent


{{< get-dependencies >}}


## Intégration Gitlab Runner

## Présentation

Grâce à cette intégration, vous pouvez :

* Visualiser et surveiller des métriques collectées via Gitlab Runners par l'intermédiaire de Prometheus
* Confirmer que Gitlab Runner parvient à se connecter à Gitlab

Consultez la [documentation de Gitlab Runner][1] (en anglais) pour
en savoir plus sur Gitlab Runner et sur son intégration à Prometheus.

## Implémentation

Suivez les instructions ci-dessous pour installer et configurer ce check lorsque l'Agent est exécuté sur un host. Consultez la [documentation relative aux modèles d'intégration Autodiscovery][2] pour découvrir comment appliquer ces instructions à un environnement conteneurisé.

### Installation

Le check Gitlab Runner est inclus avec le paquet de l'[Agent Datadog][3] : vous n'avez donc rien d'autre à installer sur vos serveurs Gitlab.

### Configuration

Modifiez le fichier `gitlab_runner.d/conf.yaml` dans le dossier `conf.d/` à la racine du [répertoire de configuration de votre Agent][4] afin de spécifier l'endpoint de métriques Prometheus de Runner ainsi que le master Gitlab à utiliser pour le check de service.
Consultez le [fichier d'exemple gitlab_runner.d/conf.yaml][5] pour découvrir toutes les options de configuration disponibles.

**Remarque** : l'élément `allowed_metrics` de la section `init_config` vous permet d'indiquer les métriques à extraire.

**Attention** : certaines métriques doivent être transmises en tant que `rate` (p. ex., `ci_runner_errors`).

### Validation

[Lancez la sous-commande `status` de l'Agent][6] et cherchez `gitlab_runner` dans la section Checks.

## Données collectées
### Métriques
{{< get-metrics-from-git "gitlab_runner" >}}


### Événements
Le check Gitlab Runner n'inclut aucun événement.

### Checks de service
Le check Gitlab Runner fournit un check de service visant à s'assurer que Runner peut communiquer avec le master Gitlab, ainsi qu'un autre check de service vérifiant la
disponibilité du endpoint Prometheus local.

## Dépannage
Besoin d'aide ? Contactez [l'assistance Datadog][8].

[1]: https://docs.gitlab.com/runner/monitoring/README.html
[2]: https://docs.datadoghq.com/fr/agent/autodiscovery/integrations
[3]: https://app.datadoghq.com/account/settings#agent
[4]: https://docs.datadoghq.com/fr/agent/guide/agent-configuration-files/?tab=agentv6#agent-configuration-directory
[5]: https://github.com/DataDog/integrations-core/blob/master/gitlab_runner/datadog_checks/gitlab_runner/data/conf.yaml.example
[6]: https://docs.datadoghq.com/fr/agent/guide/agent-commands/?tab=agentv6#agent-status-and-information
[7]: https://github.com/DataDog/integrations-core/blob/master/gitlab_runner/metadata.csv
[8]: https://docs.datadoghq.com/fr/help


{{< get-dependencies >}}