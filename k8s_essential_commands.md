# 🧠 Kubernetes Essential Commands Cheat Sheet

A concise guide to essential Kubernetes (`kubectl`) commands for managing clusters, resources, and debugging. Use this cheat sheet to streamline your workflow and troubleshoot effectively.

> **Note**: Replace `<ns>` with the namespace name, `<pod>`, `<file>`, `<name>`, or other placeholders as needed. Commands assume `kubectl` is configured to interact with your cluster.
> **Copy Commands**: Click the code block or select the text to copy each command easily. Use a terminal to paste and execute.

---

## 📑 Table of Contents

- [Listing Resources](#-listing-resources)
- [Inspecting and Debugging](#-inspecting-and-debugging)
- [Namespace and Context Management](#-namespace-and-context-management)
- [Resource Management](#-resource-management)
- [Tips for Effective Usage](#-tips-for-effective-usage)

---

## 📦 Listing Resources

Commands to view resources like pods, services, and more across namespaces. Copy the command by selecting the code block.

| **Command**                                        | **Description**                                |
| -------------------------------------------------- | ---------------------------------------------- |
| `bash<br>kubectl get all<br>`                      | List all resources in the default namespace    |
| `bash<br>kubectl get all -n <namespace><br>`       | List all resources in a specific namespace     |
| `bash<br>kubectl get pods -A<br>`                  | List all pods across all namespaces            |
| `bash<br>kubectl get svc -A<br>`                   | List all services across all namespaces        |
| `bash<br>kubectl get events -A<br>`                | Show all cluster events (useful for debugging) |
| `bash<br>kubectl get pods --watch<br>`             | Watch pod status updates in real-time          |
| `bash<br>kubectl get endpoints -n <namespace><br>` | Show endpoints for services in a namespace     |
| `bash<br>kubectl get nodes<br>`                    | List all cluster nodes                         |

> **Tip**: Use `-o wide` (e.g., `kubectl get pods -o wide`) for additional details like IP addresses or node names.

---

## 🔎 Inspecting and Debugging

Commands to dive deeper into resource details and troubleshoot issues. Copy commands from the code blocks below.

| **Command**                                                                         | **Description**                                 |
| ----------------------------------------------------------------------------------- | ----------------------------------------------- |
| `bash<br>kubectl describe pod <pod> -n <ns><br>`                                    | Show detailed info for a specific pod           |
| `bash<br>kubectl logs <pod> -n <ns><br>`                                            | View logs of a pod's primary container          |
| `bash<br>kubectl logs <pod> -c <container> -n <ns><br>`                             | View logs of a specific container in a pod      |
| `bash<br>kubectl exec -it <pod> -n <ns> -- /bin/sh<br>`                             | Open an interactive shell in a pod              |
| `bash<br>kubectl top pod -n <ns><br>`                                               | View CPU/memory usage (requires metrics-server) |
| `bash<br>kubectl top node<br>`                                                      | View resource usage for nodes                   |
| `bash<br>kubectl get events --field-selector involvedObject.name=<pod> -n <ns><br>` | Filter events for a specific pod                |

> **Tip**: If `/bin/sh` fails, try `/bin/bash` or `sh` for shell access. Use `--previous` with `kubectl logs` to view logs from a crashed pod.

---

## 🌐 Namespace and Context Management

Commands to manage namespaces and `kubectl` contexts for efficient cluster navigation. Select and copy commands as needed.

| **Command**                                                         | **Description**                               |
| ------------------------------------------------------------------- | --------------------------------------------- | ------------------------------------ |
| `bash<br>kubectl get ns<br>`                                        | List all namespaces in the cluster            |
| ```bash<br>kubectl config view --minify                             | grep namespace<br>```                         | Show the current context's namespace |
| `bash<br>kubectl config set-context --current --namespace=<ns><br>` | Set default namespace for the current context |
| `bash<br>kubectl create ns <ns><br>`                                | Create a new namespace                        |
| `bash<br>kubectl delete ns <ns><br>`                                | Delete a namespace and its resources          |

> **Tip**: Use `kubens` (from `kubectx` tool) for faster namespace switching.

---

## 🛠 Resource Management

Commands to create, update, scale, and delete resources. Copy commands from the code blocks for quick execution.

| **Command**                                                          | **Description**                                  |
| -------------------------------------------------------------------- | ------------------------------------------------ |
| `bash<br>kubectl apply -f <file>.yaml<br>`                           | Apply a configuration from a YAML/JSON file      |
| `bash<br>kubectl delete -f <file>.yaml<br>`                          | Delete resources defined in a YAML/JSON file     |
| `bash<br>kubectl edit <resource>/<name> -n <ns><br>`                 | Edit a resource (e.g., `deployment/myapp`)       |
| `bash<br>kubectl scale deployment <name> --replicas=<n> -n <ns><br>` | Scale a deployment to `<n>` replicas             |
| `bash<br>kubectl rollout restart deployment <name> -n <ns><br>`      | Restart a deployment by triggering a new rollout |
| `bash<br>kubectl rollout status deployment <name> -n <ns><br>`       | Check the status of a deployment rollout         |
| `bash<br>kubectl get componentstatuses<br>`                          | Check the health of core Kubernetes components   |
| `bash<br>kubectl label <resource> <name> <key>=<value> -n <ns><br>`  | Add a label to a resource                        |

> **Tip**: Use `kubectl apply -k` for Kustomize-based configurations.

---

## 💡 Tips for Effective Usage

- **Aliases**: Set up a shell alias like `alias k='kubectl'` to save time.
- **Dry Run**: Test commands with `--dry-run=client` (e.g., `kubectl apply -f file.yaml --dry-run=client`) to preview changes.
- **Output Formats**: Use `-o yaml` or `-o json` to export resource configs (e.g., `kubectl get pod <pod> -o yaml`).
- **Autocomplete**: Enable `kubectl` autocomplete in your shell for faster command entry.
- **Plugins**: Explore tools like `k9s`, `kubectx`, or `stern` for enhanced Kubernetes management.
- **Error Handling**: If a command fails, check cluster connectivity (`kubectl cluster-info`) and permissions (`kubectl auth can-i`).
- **Copy Efficiency**: Triple-click a code block to select the entire command, then copy and paste into your terminal.

---

> **Disclaimer**: Ensure you have appropriate permissions before running commands, especially destructive ones like `delete`. Always verify namespace and resource names to avoid unintended changes.
