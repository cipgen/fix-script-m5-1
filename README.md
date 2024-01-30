## Fix script

Let's fix errors in the script (script)

source script with error 
```console
$>bash kubeplugin                
error: flag needs an argument: 'n' in -n
See 'kubectl --help' for usage.
```

```console
#!/bin/bash

# Define command-line arguments

RESOURCE_TYPE=$0

# Retrieve resource usage statistics from Kubernetes
kubectl $2 $RESOURCE_TYPE -n $1 | tail -n +2 | while read line
do
  # Extract CPU and memory usage from the output
  NAME=$(echo $line | awk '{print $1}')
  CPU=$(echo $line | awk '{print $2}')
  MEMORY=$(echo $line | awk '{print $3}')

  # Output the statistics to the console
  # "Resource, Namespace, Name, CPU, Memory"
done
```
--------

```console
Error - 1:
The definition of the RESOURCE_TYPE variable is incorrect, initially set as $0, which is the name of the script itself. It should be changed to $1.

Error - 2:
The script lacks a data output block. It is necessary to add echo "$RESOURCE_TYPE, $1, $NAME, $CPU, $MEMORY" to display the results.

Adding a help block and argument input check:

```


```console
#!/bin/bash

# Define command-line arguments

RESOURCE_TYPE=$0

# Retrieve resource usage statistics from Kubernetes
kubectl $2 $RESOURCE_TYPE -n $1 | tail -n +2 | while read line
do
  # Extract CPU and memory usage from the output
  NAME=$(echo $line | awk '{print $1}')
  CPU=$(echo $line | awk '{print $2}')
  MEMORY=$(echo $line | awk '{print $3}')

  # Output the statistics to the console
  # "Resource, Namespace, Name, CPU, Memory"
done
```

Adding a help block and argument input check:

```console
show_help() {
  echo "Usage: kubectl stats <namespace> <command> <resource-type>"
  echo ""
  echo "This plugin for kubectl retrieves and displays resource usage statistics for specified Kubernetes resources."
  echo ""
  echo "Arguments:"
  echo "  namespace      Kubernetes namespace"
  echo "  command        kubectl command to retrieve resources (e.g., get)"
  echo "  resource-type  Type of the resource (e.g., pods, deployments)"
}

# Check for --help, -h, or no arguments
if [[ "$1" == "--help" || "$1" == "-h" || "$#" -eq 0 ]]; then
  show_help
  exit 0
fi
```
### Add kubeplugin as kubectl plugin

- Ensure that the plugin file is located in a directory included in the PATH environment variable. Check this using the echo $PATH command.
- Copy the file to a directory, such as /usr/local/bin, and rename it to kubectl-kubeplugin so that the kubectl utility can recognize the file as a plugin: 

```console
sudo cp ./kubeplugin /usr/local/bin/kubectl-kubeplugin
```
- Add execution rights to the file:

```console
sudo chmod +x /usr/local/bin/kubectl-kubeplugin
```
- If you want to use your own, you need to add it to PATH, run the command export

```console
PATH=$PATH:< your path>
```
- To make the changes to PATH permanent, add this command to your .bashrc, .bash_profile, or equivalent file in your shell.

- Verify the presence of the plugin using kubectl plugin list. If everything is done correctly, kubectl should detect the plugin and display it in the list of available plugins.


**-=test=-**

```console
kubectl kubeplugin kube-system get pods 
```

![result](https://github.com/cipgen/mod5_task1/blob/main/scripts/img/kubeplugin-result.png)
