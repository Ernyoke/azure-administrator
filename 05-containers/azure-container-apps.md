# Azure Container Apps

- Azure Container Apps: managed platform for containerized applications without administering a Kubernetes cluster
- Supports Linux container images, managed ingress, revisions and automatic horizontal scaling
- Environment: shared boundary for container apps with common networking and logging configuration
- Replica: running instance of an application revision

## Provision an Application

- In the Azure portal, create or select an environment and configure the container image, CPU, memory and environment variables
- For a private registry, configure registry authentication and give the workload identity permission to pull the image
- Ingress settings:
    - External: accepts traffic from outside the Container Apps environment, subject to environment networking
    - Internal: accepts traffic only from inside the environment
    - Target port: port on which the container application listens
- Store sensitive values as secrets and reference them from environment variables
- Container-local storage is ephemeral; use supported persistent storage such as Azure Files when data must survive replicas

## Revisions and Traffic

- Revision: immutable snapshot of the application's revision-scoped configuration
- Changes to the image, container resources or scale rules create a new revision
- Single revision mode: shifts traffic to the new revision after it becomes ready
- Multiple revision mode: allows several active revisions with weighted HTTP traffic splitting
- Use traffic weights to introduce a release gradually or return traffic to a previous revision
- Changing a secret does not create a revision; restart affected revisions when they must pick up updated values

## Sizing and Scaling

- Set CPU and memory per replica using a combination supported by the selected workload profile
- Consumption profile: provides serverless execution with usage-based billing
- Dedicated workload profiles: provide reserved compute capacity for workloads with specific resource requirements
- KEDA (Kubernetes Event-driven Autoscaling): drives automatic scaling from supported triggers
- Scale rules include HTTP concurrency, TCP connections and events such as queue message counts
- Set minimum and maximum replicas to control availability and scale limits
- Scale to zero requires a minimum of zero and a trigger that can activate an idle application
- CPU and memory rules alone cannot activate an application from zero replicas
- Without ingress, configure an event-driven scale rule or keep at least one replica running
- Scaling adds or removes replicas; changing CPU or memory is a separate configuration change
- Use console logs, system logs and revision status to investigate startup, image pull and scaling failures

## Service Selection

- ACI: isolated containers or short-lived tasks with explicit resource allocation
- Container Apps: applications needing revisions, managed ingress and event-driven scaling
- Container Registry: image storage used by either execution service