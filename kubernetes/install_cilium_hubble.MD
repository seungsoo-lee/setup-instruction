## Install the Cilium CLI
- Install the latest version of the Cilium CLI. The Cilium CLI can be used to install Cilium, inspect the state of a Cilium installation, and enable/disable various features (e.g. clustermesh, Hubble).
```
CILIUM_CLI_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/master/stable.txt)
CLI_ARCH=amd64
if [ "$(uname -m)" = "aarch64" ]; then CLI_ARCH=arm64; fi
$ curl -L --fail --remote-name-all https://github.com/cilium/cilium-cli/releases/download/${CILIUM_CLI_VERSION}/cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
$ sha256sum --check cilium-linux-${CLI_ARCH}.tar.gz.sha256sum
$ sudo tar xzvfC cilium-linux-${CLI_ARCH}.tar.gz /usr/local/bin
$ rm cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
```

## Install Cilium
```
$ cilium install
```

## Validate the Installation
- To validate that Cilium has been properly installed, you can run
```
$ cilium status --wait
   /¯¯\
/¯¯\__/¯¯\    Cilium:         OK
\__/¯¯\__/    Operator:       OK
/¯¯\__/¯¯\    Hubble:         disabled
\__/¯¯\__/    ClusterMesh:    disabled
   \__/

DaemonSet         cilium             Desired: 2, Ready: 2/2, Available: 2/2
Deployment        cilium-operator    Desired: 2, Ready: 2/2, Available: 2/2
Containers:       cilium-operator    Running: 2
                  cilium             Running: 2
Image versions    cilium             quay.io/cilium/cilium:v1.9.5: 2
                  cilium-operator    quay.io/cilium/operator-generic:v1.9.5: 2
```

- Run the following command to validate that your cluster has proper network connectivity:
```
$ cilium connectivity test
ℹ️  Monitor aggregation detected, will skip some flow validation steps
✨ [k8s-cluster] Creating namespace for connectivity check...
(...)
---------------------------------------------------------------------------------------------------------------------
📋 Test Report
---------------------------------------------------------------------------------------------------------------------
✅ 69/69 tests successful (0 warnings)
```

- Install Hubble 
```
$ export HUBBLE_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/hubble/master/stable.txt)
$ curl -L --remote-name-all https://github.com/cilium/hubble/releases/download/$HUBBLE_VERSION/hubble-linux-amd64.tar.gz{,.sha256sum}
$ sha256sum --check hubble-linux-amd64.tar.gz.sha256sum
$ sudo tar xzvfC hubble-linux-amd64.tar.gz /usr/local/bin
$ rm hubble-linux-amd64.tar.gz{,.sha256sum}
```

- https://docs.cilium.io/en/v1.12/gettingstarted/k8s-install-default/#install-cilium
