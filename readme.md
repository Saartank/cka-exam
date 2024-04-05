# CKA Exam Quick Pointers

## Table of Contents

1. [Kubernetes Important Components](#kubernetes-important-components)
2. [Important Kubernetes Directories](#important-kubernetes-directories)
3. [Kubernetes Important Commands](#kubernetes-important-commands)
4. [Kubectl Output Customization](#kubectl-output-customization)
5. [Important Vim Commands](#important-vim-commands)
6. [Important Grep Options](#important-grep-options)
7. [Important Systemd Commands](#important-systemd-commands)

## Kubernetes Important Components:

- **API Server**

  - **Port**: 6443
  - **Description**: Serves the Kubernetes API and is the central management entity for the cluster's operations and communications.

- **etcd**

  - **Ports**: 2379 (client requests), 2380 (peer communication)
  - **Description**: Key-value store for all cluster data, acting as Kubernetes' backing store for all cluster state and data.

- **Scheduler**

  - **Description**: Watches for newly created Pods with no assigned node and selects a node for them to run on.

- **Controller Manager**

  - **Port**: 10252
  - **Description**: Runs controller processes such as node controller, endpoints controller, and namespace controller.

- **Kubelet**

  - **Port**: 10250
  - **Description**: Agent running on each node, ensuring containers are running in a Pod as intended.

  **Kube-proxy**

  - **Description**: Manages network rules on nodes to allow network communication to Pods.

## Important Kubernetes Directories

### `~/.kube/config`

- **Path**: `~/.kube/config`
- **Description**: This is the default location for a user's kubeconfig file, which stores the cluster connection and access information. It allows `kubectl` and other Kubernetes client tools to communicate with the Kubernetes cluster. The file contains details such as cluster definitions, user credentials, and context for switching between clusters.
- **Management**:
  - Use `kubectl config` commands to view, modify, and manage your kubeconfig settings.
  - Merge multiple kubeconfig files by setting the `KUBECONFIG` environment variable to a list of file paths to aggregate configuration from those files.

### `/etc/kubernetes/`

- **Description**: The main configuration directory for Kubernetes components.
- **Key Subdirectories**:
  - `/etc/kubernetes/manifests/`: Contains static pod manifests for starting control plane components as static pods.
  - `/etc/kubernetes/pki/`: Stores the Certificate Authority (CA) and other TLS certificates for securing cluster communication.

### `/var/lib/kubelet/`

- **Description**: The primary directory used by the kubelet.
- **Key Subdirectories**:
  - `/var/lib/kubelet/pods/`: Data about pods managed by the kubelet, including volumes and configuration.
  - `/var/lib/kubelet/pki/`: Contains the kubelet's private key and certificate.

### `/var/lib/etcd/`

- **Description**: Default directory for etcd data storage.
- **Key Points**: Contains the etcd database with all Kubernetes cluster state.

### `/var/log/`

- **Description**: Contains log files for Kubernetes components and the container runtime.
- **Key Subdirectories**:
  - `/var/log/kube-apiserver.log`: Logs from the Kubernetes API server.
  - `/var/log/kube-scheduler.log`: Logs from the Kubernetes scheduler.
  - `/var/log/kube-controller-manager.log`: Logs from the Kubernetes controller manager.
  - `/var/log/kubelet.log`: Logs from the kubelet service.
  - `/var/log/containers/`: Logs for containers managed by Kubernetes.

### `/etc/cni/net.d/`

- **Description**: Where CNI plugin configurations are stored.

## Kubernetes Important Commands

### Resource Management

| Command   | Description                                             | Example Usage                                                  |
| --------- | ------------------------------------------------------- | -------------------------------------------------------------- |
| `create`  | Create a resource from a file or from stdin.            | `kubectl create -f <config-file>`                              |
| `delete`  | Delete resources by names, file, or types.              | `kubectl delete pod <pod-name>`                                |
| `apply`   | Apply a configuration change to a resource from a file. | `kubectl apply -f <config-file>`                               |
| `replace` | Replace a resource by filename or stdin.                | `kubectl replace -f <config-file>`                             |
| `patch`   | Update field(s) of a resource.                          | `kubectl patch deployment <name> -p '{"spec":{"replicas":5}}'` |
| `edit`    | Edit the specified resource with the default editor.    | `kubectl edit deployment/<deployment-name>`                    |

### Resource Inspection and Debugging

| Command    | Description                                          | Example Usage                                      |
| ---------- | ---------------------------------------------------- | -------------------------------------------------- |
| `get`      | List resources.                                      | `kubectl get <resource-name>`                      |
| `describe` | Show detailed information about a specific resource. | `kubectl describe <resource-type>/<resource-name>` |
| `logs`     | Print the logs for a container in a pod.             | `kubectl logs <pod-name>`                          |
| `exec`     | Execute a command inside a container in a pod.       | `kubectl exec -it <pod-name> -- /bin/bash`         |
| `top`      | Display resource (CPU/Memory/Storage) usage.         | `kubectl top nodes`                                |

### Configuration and Security

| Command                  | Description                                                | Example Usage                                                  |
| ------------------------ | ---------------------------------------------------------- | -------------------------------------------------------------- |
| `config set-context`     | Change the current context's namespace or user.            | `kubectl config set-context --current --namespace=<namespace>` |
| `config get-contexts`    | Display list of contexts.                                  | `kubectl config get-contexts`                                  |
| `config use-context`     | Switch to use a different context.                         | `kubectl config use-context <context-name>`                    |
| `config view`            | Show merged kubeconfig settings.                           | `kubectl config view`                                          |
| `config set-credentials` | Set a user entry in kubeconfig.                            | `kubectl config set-credentials <user-name> --token=<token>`   |
| `auth can-i`             | Check whether the current user can perform a given action. | `kubectl auth can-i create deployments`                        |

### Deployment Management

| Command           | Description                                                       | Example Usage                                                      |
| ----------------- | ----------------------------------------------------------------- | ------------------------------------------------------------------ |
| `scale`           | Scale a deployment to a specified number of replicas.             | `kubectl scale deployment/<name> --replicas=<num>`                 |
| `expose`          | Expose a pod, deployment, or service as a new Kubernetes service. | `kubectl expose deployment/<name> --type=LoadBalancer --port=8080` |
| `rollout status`  | Show the rollout status of a deployment.                          | `kubectl rollout status deployment/<name>`                         |
| `rollout restart` | Restart a deployment.                                             | `kubectl rollout restart deployment/<name>`                        |
| `set image`       | Update the image of a container of a pod or selected set of pods. | `kubectl set image deployment/<name> <container>=<image>`          |

### Node Management

| Command    | Description                                               | Example Usage                                          |
| ---------- | --------------------------------------------------------- | ------------------------------------------------------ |
| `cordon`   | Mark a node as unschedulable.                             | `kubectl cordon <node-name>`                           |
| `uncordon` | Mark a node as schedulable.                               | `kubectl uncordon <node-name>`                         |
| `drain`    | Prepare a node for maintenance, evicting pods gracefully. | `kubectl drain <node-name> --ignore-daemonsets`        |
| `taint`    | Update the taints on a node.                              | `kubectl taint nodes <node-name> key=value:NoSchedule` |

## Kubectl Output Customization

| Output Type    | Specific Example                                                                                                    |
| -------------- | ------------------------------------------------------------------------------------------------------------------- |
| Custom Columns | `kubectl get pods -o=custom-columns=NAME:.metadata.name,STATUS:.status.phase --sort-by=.metadata.creationTimestamp` |
| JSON Path      | `kubectl config view --kubeconfig=my-kube-config -o jsonpath="{.contexts[?(@.context.user=='aws-user')].name}"`     |

## Important Vim Commands

| Command         | Description                                                                                                        |
| --------------- | ------------------------------------------------------------------------------------------------------------------ |
| `i`             | Enter insert mode before the current cursor position.                                                              |
| `a`             | Enter insert mode after the current cursor position.                                                               |
| `v`             | Enter visual mode (character selection).                                                                           |
| `V`             | Enter visual line mode (line selection).                                                                           |
| `Ctrl + v`      | Enter visual block mode (block selection).                                                                         |
| `:w`            | Save the file but don't exit Vim.                                                                                  |
| `:wq` or `:x`   | Save the file and exit Vim.                                                                                        |
| `:q`            | Quit Vim (fails if there are unsaved changes).                                                                     |
| `:q!`           | Quit Vim and discard any changes.                                                                                  |
| `dd`            | Delete the current line (cut).                                                                                     |
| `yy`            | Yank (copy) the current line.                                                                                      |
| `p`             | Paste the clipboard content after the cursor.                                                                      |
| `P`             | Paste the clipboard content before the cursor.                                                                     |
| `u`             | Undo the last operation.                                                                                           |
| `Ctrl + r`      | Redo the last undone operation.                                                                                    |
| `/`             | Search forward for a pattern.                                                                                      |
| `?`             | Search backward for a pattern.                                                                                     |
| `n`             | Repeat the last search in the same direction.                                                                      |
| `N`             | Repeat the last search in the opposite direction.                                                                  |
| `:%s/old/new/g` | Replace all occurrences of `old` with `new` throughout the file. Any delimiter can be used, e.g., `:%s#old#new#g`. |
| `:noh`          | Remove search highlighting.                                                                                        |
| `gg`            | Go to the first line of the document.                                                                              |
| `G`             | Go to the last line of the document.                                                                               |
| `:set nu`       | Turn on line numbering.                                                                                            |
| `:set nonu`     | Turn off line numbering.                                                                                           |

## Important Grep Options

| Option         | Description                                                                         |
| -------------- | ----------------------------------------------------------------------------------- |
| `-i`           | Ignore case distinctions in both the pattern and input files.                       |
| `-v`           | Invert the sense of matching, to select non-matching lines.                         |
| `-r`           | Recursively search subdirectories listed.                                           |
| `-n`           | Print the line number of each matching line.                                        |
| `--color=auto` | Highlight matching strings. Automatically shows colors when outputting to terminal. |
| `-A <number>`  | Print `<number>` lines of trailing context after matching lines.                    |
| `-B <number>`  | Print `<number>` lines of leading context before matching lines.                    |

## Important Systemd Commands

| Command                                               | Description                                                    |
| ----------------------------------------------------- | -------------------------------------------------------------- |
| `systemctl daemon-reload`                             | Reloads the systemd manager configuration to read any changes. |
| `systemctl start <service>`                           | Starts a specific service.                                     |
| `systemctl stop <service>`                            | Stops a specific service.                                      |
| `systemctl restart <service>`                         | Restarts a specific service.                                   |
| `systemctl status <service>`                          | Displays the status of a specific service.                     |
| `systemctl enable <service>`                          | Enables a service to start at boot.                            |
| `systemctl disable <service>`                         | Disables a service from starting at boot.                      |
| `systemctl is-enabled <service>`                      | Checks if a service is enabled to start on boot.               |
| `systemctl is-active <service>`                       | Checks if a service is currently active.                       |
| `systemctl list-units --type=service --state=running` | Lists all running units of type service.                       |
| `journalctl -u <service>`                             | Displays the journal logs for a specific service.              |
| `journalctl -f`                                       | Tails the journal logs in real-time.                           |

Note: In the commands above, `<service>` can be specified with or without the `.service` suffix (e.g., `kubelet` or `kubelet.service`). Including `.service` is optional and used for more clarity, especially in scripts or when different unit types with the same name exist.
