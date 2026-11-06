# CST8915 Lab 6: Deploying the Algonquin Pet Store on AKS

- **Student Name**: Hye Ran Yoo
- **Student ID**: 041145212
- **Course**: CST8915 Full-stack Cloud-native Development
- **Semester**: Fall 2026

---

## Demo Video

🎥 [Watch Demo Video](https://youtu.be/Ku_-t3lwuAM)

---

## Environment

- Resource group: `AlgonquinPetStoreRG`, region Canada Central
- AKS cluster: `AlgonquinPetStoreCluster`, preset Dev/Test, pricing tier Free, Kubernetes 1.36.5
- Node pools:
  - `masterpool`: System mode, Standard_D2as_v4, Manual, 1 node
  - `workerspool`: User mode, Standard_D2as_v4, Manual, 1 node
- Commands were run in Azure Cloud Shell (Bash). It already has `az` and `kubectl`.

---

## Steps

1. Created the AKS cluster in the Azure portal with the two node pools above.
2. Connected `kubectl` to the cluster:
   ```bash
   az aks get-credentials --resource-group AlgonquinPetStoreRG --name AlgonquinPetStoreCluster
   kubectl get nodes
   ```
3. Deployed the app with the all-in-one YAML file:
   ```bash
   kubectl apply -f algonquin-pet-store-all-in-one.yaml
   kubectl get pods
   kubectl get services
   ```
   This created 4 deployments and 4 services. All 4 pods were Running after about 1 minute.
4. Opened the store-front external IP in the browser with `http://`, placed an order, and checked `order_queue` in the RabbitMQ management page.
5. Scaled store-front to 3 pods, deleted one pod, and saw Kubernetes create a new one. Then scaled back to 1.

---

## Notes

- Only `store-front` is a LoadBalancer service with a public IP. `order-service`, `product-service`, and `rabbitmq` are ClusterIP, so they are only inside the cluster.
- One public IP serves all paths because nginx inside store-front is a reverse proxy. It sends `/products` to `product-service` and `/orders` to `order-service` by the service name.
