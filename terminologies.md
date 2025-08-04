Let’s say you have a backend application with five microservices and one UI component.

Each microservice and the UI can run inside its own container.

A pod in Kubernetes might hold one container for each microservice. That means you’ll have five pods for the backend microservices, and another pod for the UI.

All these pods run on nodes, which are the machines in the cluster.

So, in this example, you have a cluster (the entire setup), nodes (the machines running the pods), pods (each holding a microservice container), and containers (where your actual application code runs).


The cluster is the top-level structure. Inside the cluster, you have nodes. On each node, there can be multiple pods. Each pod holds one or more containers, and inside those containers is where your application code runs.