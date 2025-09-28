# ArgoCD GitOps Project

This GitOps repository is designed to manage the deployment of applications and services across multiple environments and clusters using ArgoCD and Helm charts. The repository is structured to ensure scalability, flexibility, and ease of management.

## Repository Structure

- **argocd/applicationsets/**
  - **cluster-addons/**: Contains ApplicationSet manifests for deploying cluster-wide add-ons, such as Prometheus Operator. These add-ons are deployed to all clusters.
  - **microservices-appset.yaml**: A dynamic ApplicationSet manifest that generates ArgoCD applications for microservices based on environment and service configurations.

- **environments/**
  - Contains environment-specific configurations. Each environment directory includes:
    - **config.yaml**: Specifies the cluster name and namespace for the environment.

- **services/**
  - Contains service-specific configurations. Each service directory includes:
    - **chart-config.yaml**: Defines the Helm chart repository, target revision, and chart path for the service.
    - **envs/**: Contains environment-specific values files for the service. For example, `envs/staging/values.yaml` defines the Helm values for deploying the service in the `staging` environment.

## Usage

### Adding a New Service

1. **Create the Service Directory**
   Under `services/`, create a new directory named after the service (e.g., `services/<service-name>/`).

2. **Define the Helm Chart Configuration**
   In the new service directory, create a `chart-config.yaml` file to specify the Helm chart details. For example:
   ```yaml
   chart:
     repoURL: <helm-repo-url>
     targetRevision: <chart-version>
     path: <chart-path>
   ```

3. **Add Environment-Specific Values Files**
   Under `services/<service-name>/envs/`, create a directory for each environment (e.g., `staging`, `production`) and add a `values.yaml` file with the Helm values for that environment.

4. **Update the ApplicationSet Manifest**
   The `microservices-appset.yaml` file dynamically generates applications for all services and environments. Ensure the new service and its values files follow the existing structure.

### Adding a New Environment

1. **Create the Environment Directory**
   Under `environments/`, create a new directory named after the environment (e.g., `environments/<env-name>/`).

2. **Define the Cluster Configuration**
   In the new environment directory, create a `config.yaml` file specifying the cluster name and namespace. For example:
   ```yaml
   cluster:
     name: <cluster-name>
     namespace: <namespace>
   ```

3. **Add Values Files for Services**
   For each service, create a `values.yaml` file under `services/<service-name>/envs/<env-name>/` to define the Helm values for that environment.

Once these steps are completed, the `microservices-appset.yaml` manifest will automatically generate applications for the new environment and services.

## ApplicationSet Structure Explanation

The `microservices-appset.yaml` manifest uses a **matrix generator** to dynamically create ArgoCD applications based on the following:

1. **Environment Discovery**
   - Scans the `environments/` directory for `config.yaml` files to identify available environments.

2. **Service Discovery**
   - Scans the `services/` directory for `chart-config.yaml` files to identify available services.

3. **Values File Matching**
   - Matches services with environment-specific `values.yaml` files under `services/<service-name>/envs/<env-name>/`.

### Key Requirements

- Each environment must have a `config.yaml` file specifying the cluster name and namespace.
- Each service must have a `chart-config.yaml` file defining the Helm chart details.
- Each service-environment combination must have a `values.yaml` file to define the deployment configuration.

### Summary of Behavior

- The ApplicationSet dynamically generates applications for all valid service-environment combinations.
- Applications are only created for environments and services that meet the required configuration criteria.

This setup ensures that deployments are consistent, automated, and easy to manage across multiple clusters and environments.
