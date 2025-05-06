# 🧠 Kubernetes Essential Commands Cheat Sheet

A concise guide to essential Kubernetes (`kubectl`) commands for managing clusters, resources, and debugging. Use this cheat sheet to streamline your workflow and troubleshoot effectively.

> **Note**: Replace `<ns>` with the namespace name, `<pod>`, `<file>`, `<name>`, or other placeholders as needed. Commands assume `kubectl` is configured to interact with your cluster.

---

## 📑 Table of Contents

- [Listing Resources](#-listing-resources)
- [Inspecting and Debugging](#-inspecting-and-debugging)
- [Namespace and Context Management](#-namespace-and-context-management)
- [Resource Management](#-resource-management)
- [Tips for Effective Usage](#-tips-for-effective-usage)

---

## 📦 Listing Resources

Commands to view resources like pods, services, and more across namespaces.

| **Command**                            | **Description**                                |
| -------------------------------------- | ---------------------------------------------- |
| `kubectl get all`                      | List all resources in the default namespace    |
| `kubectl get all -n <namespace>`       | List all resources in a specific namespace     |
| `kubectl get pods -A`                  | List all pods across all namespaces            |
| `kubectl get svc -A`                   | List all services across all namespaces        |
| `kubectl get events -A`                | Show all cluster events (useful for debugging) |
| `kubectl get pods --watch`             | Watch pod status updates in real-time          |
| `kubectl get endpoints -n <namespace>` | Show endpoints for services in a namespace     |
| `kubectl get nodes`                    | List all cluster nodes                         |

> **Tip**: Use `-o wide` (e.g., `kubectl get pods -o wide`) for additional details like IP addresses or node names.

---

## 🔎 Inspecting and Debugging

Commands to dive deeper into resource details and troubleshoot issues.

| **Command**                                                             | **Description**                                 |
| ----------------------------------------------------------------------- | ----------------------------------------------- |
| `kubectl describe pod <pod> -n <ns>`                                    | Show detailed info for a specific pod           |
| `kubectl logs <pod> -n <ns>`                                            | View logs of a pod's primary container          |
| `kubectl logs <pod> -c <container> -n <ns>`                             | View logs of a specific container in a pod      |
| `kubectl exec -it <pod> -n <ns> -- /bin/sh`                             | Open an interactive shell in a pod              |
| `kubectl top pod -n <ns>`                                               | View CPU/memory usage (requires metrics-server) |
| `kubectl top node`                                                      | View resource usage for nodes                   |
| `kubectl get events --field-selector involvedObject.name=<pod> -n <ns>` | Filter events for a specific pod                |

> **Tip**: If `/bin/sh` fails, try `/bin/bash` or `sh` for shell access. Use `--previous` with `kubectl logs` to view logs from a crashed pod.

---

## 🌐 Namespace and Context Management

Commands to manage namespaces and `kubectl` contexts for efficient cluster navigation.

| **Command**                                             | **Description**                               |
| ------------------------------------------------------- | --------------------------------------------- | ------------------------------------ |
| `kubectl get ns`                                        | List all namespaces in the cluster            |
| `kubectl config view --minify                           | grep namespace`                               | Show the current context's namespace |
| `kubectl config set-context --current --namespace=<ns>` | Set default namespace for the current context |
| `kubectl create ns <ns>`                                | Create a new namespace                        |
| `kubectl delete ns <ns>`                                | Delete a namespace and its resources          |

> **Tip**: Use `kubens` (from `kubectx` tool) for faster namespace switching.

---

## 🛠 Resource Management

Commands to create, update, scale, and delete resources.

| **Command**                                              | **Description**                                  |
| -------------------------------------------------------- | ------------------------------------------------ |
| `kubectl apply -f <file>.yaml`                           | Apply a configuration from a YAML/JSON file      |
| `kubectl delete -f <file>.yaml`                          | Delete resources defined in a YAML/JSON file     |
| `kubectl edit <resource>/<name> -n <ns>`                 | Edit a resource (e.g., `deployment/myapp`)       |
| `kubectl scale deployment <name> --replicas=<n> -n <ns>` | Scale a deployment to `<n>` replicas             |
| `kubectl rollout restart deployment <name> -n <ns>`      | Restart a deployment by triggering a new rollout |
| `kubectl rollout status deployment <name> -n <ns>`       | Check the status of a deployment rollout         |
| `kubectl get componentstatuses`                          | Check the health of core Kubernetes components   |
| `kubectl label <resource> <name> <key>=<value> -n <ns>`  | Add a label to a resource                        |

> **Tip**: Use `kubectl apply -k` for Kustomize-based configurations.

---

## 💡 Tips for Effective Usage

- **Aliases**: Set up a shell alias like `alias k='kubectl'` to save time.
- **Dry Run**: Test commands with `--dry-run=client` (e.g., `kubectl apply -f file.yaml --dry-run=client`) to preview changes.
- **Output Formats**: Use `-o yaml` or `-o json` to export resource configs (e.g., `kubectl get pod <pod> -o yaml`).
- **Autocomplete**: Enable `kubectl` autocomplete in your shell for faster command entry.
- **Plugins**: Explore tools like `k9s`, `kubectx`, or `stern` for enhanced Kubernetes management.
- **Error Handling**: If a command fails, check cluster connectivity (`kubectl cluster-info`) and permissions (`kubectl auth can-i`).

---

> **Disclaimer**: Ensure you have appropriate permissions before running commands, especially destructive ones like `delete`. Always verify namespace and resource names to avoid unintended changes.
