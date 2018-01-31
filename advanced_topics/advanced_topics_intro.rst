Advanced Topics Introduction
============================
On top of the standard Kubernetes architecture, in a KubeNow cluster there are four main node entities: master, service, storage and egde. By default, each node shares the same private network that allows incoming traffic only on port 80 and 443 (enabling HTTP and HTTPS).

Apart from the typical KubeNow's setting, some other configurations can be used. **Excluding the master node**, each node entity is optional and can be set to any replication factor. For instance, when IP addresses are particularly scarce, it is possible to not deploy any edge node, and to use the master node as reverse proxy instead. The same stands for the storage nodes, that can be removed when an external filesystem is available.