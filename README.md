# Installing Temporal Cluster

## Prerequisites
- Postgres v12 or higher
- Temporal SQL Tool
- Helm v3
- Kubernetes Cluster
- kubectl

## Preqrequisite: Get the Database Ready for Temporal
1. Create `temporal` and `temporal_visibility` databases in Postgres.

2. Clone the Temporal repository [here](https://github.com/authnull0/temporal)

3. Run `make temporal-sql-tool` in the cloned repository's directory to build the Temporal SQL Tool.

4. Export the following Environment Variables using the commands:
    ```bash 
    export SQL_PLUGIN=postgres
    export SQL_HOST=postgresql_host
    export SQL_PORT=5432
    export SQL_USER=postgresql_user
    export SQL_PASSWORD=postgresql_password
    ```
5. Run the following command to create the schema in the database:
    ```bash
    ./temporal-sql-tool create-database -database temporal
    ```
    ```bash
    SQL_DATABASE=temporal ./temporal-sql-tool setup-schema -v 0.0
    ```
    ```bash
    SQL_DATABASE=temporal ./temporal-sql-tool update -schema-dir schema/postgresql/v96/temporal/versioned
    ```

6. Run the following command to create the visibility schema in the database:
    ```bash
    ./temporal-sql-tool create-database -database temporal_visibility
    ```
    ```bash
    SQL_DATABASE=temporal_visibility ./temporal-sql-tool setup-schema -v 0.0
    ```
    ```bash
    SQL_DATABASE=temporal_visibility ./temporal-sql-tool update -schema-dir schema/postgresql/v96/visibility/versioned
    ```

### Once the above steps are completed, you can start the deployment of the Temporal cluster.

## Deploying Temporal Cluster

1. Switch to the `helm-charts` directory:
    ```bash
    cd helm-charts
    ```
2. Update Helm dependencies:
    ```bash
    helm dependency update
    ```
3. Update the `values.postgresql.yaml` file with database connection details, credentials and other metadata:
    ```bash
    vim values/values.postgresql.yaml
    ```
    Update the following values for the temporal database:
    ```yaml
    host: xxx.xxx.xxx.xxx
    port: 5432
    database: temporal
    user: xxxxx
    password: xxxxxxx
    ```
    Update the following values for the temporal_visibility database:

    ```yaml
    host: xxx.xxx.xxx.xxx
    port: 5432
    database: temporal
    user: xxxxx
    password: xxxxxxx
    ```


4. Deploy Temporal Cluster:
    ```bash
    helm upgrade --install -f values/values.postgresql.yaml --set server.replicaCount=1 --set cassandra.enabled=false --set prometheus.enabled=false --set grafana.enabled=false --set elasticsearch.enabled=false temporal . --timeout 15m
    ```

5. Check the status of the pods:
    ```bash
    kubectl get pods
    ```
    ```bash
    NAME                                                   READY   STATUS    RESTARTS   AGE
    temporal-admintools-5c8f8f9f5f-4q9q8                   1/1     Running   0          2m
    temporal-frontend-5f8f9f5f-4q9q8                       1/1     Running   0          2m
    temporal-history-5f8f9f5f-4q9q8                        1/1     Running   0          2m
    temporal-matching-5f8f9f5f-4q9q8                       1/1     Running   0          2m
    temporal-server-5f8f9f5f-4q9q8                         1/1     Running   0          2m
    temporal-web-5f8f9f5f-4q9q8                            1/1     Running   0          2m
    temporal-worker-5f8f9f5f-4q9q8                         1/1     Running   0          2m
    ```

  Congratulations! You have successfully deployed Temporal Cluster.

  ## Authors
  The KloudOne Team
