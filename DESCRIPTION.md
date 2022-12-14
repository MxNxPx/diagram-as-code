## Big Bang Deployment Flow

### All Begins with Customer Big Bang Deployment Repo (built from template)

* Link: [https://repo1.dso.mil/platform-one/big-bang/customers/template](https://repo1.dso.mil/platform-one/big-bang/customers/template)
* Supports defining multiple environments (ex: prod and dev)
* Upon deploying, creates various Flux Custom Resources (CRs)

### **[1]** Flux Custom Resources (CRs) created by the Customer Big Bang Deployment Repo (per environment)

* Link: [https://repo1.dso.mil/platform-one/big-bang/customers/template/-/tree/main/dev](https://repo1.dso.mil/platform-one/big-bang/customers/template/-/tree/main/dev)
* Flux Customer Big Bang GitRepository CR **[1a]** to reconcile between the cluster and repo **[1b]**
* Flux Customer Big Bang Kustomization CR **[1b]** that is kept in sync with the git repo thru the Flux Customer Big Bang GitRepository CR **[1c]**
* Flux Big Bang GitRepository CR **[1d]** to keep in sync between the cluster and git **[1e]**
* Flux Big Bang HelmRelease CR **[1f]**

> ************
> **_NOTE:_** Code for the core flux components are maintained here as well
> ************

### **[2]** Flux Big Bang HelmRelease CR which creates the Big Bang Helm Release

* Link: [https://repo1.dso.mil/platform-one/big-bang/bigbang/base](https://repo1.dso.mil/platform-one/big-bang/bigbang/base)
* This implements an instance of the Big Bang Repo resources
* The Flux Big Bang HelmRelease CR renders and creates the Big Bang Helm Release **[2]**

### **[3]** Big Bang Helm Release which orchestrates installation of the Big Bang components

* Link: [https://repo1.dso.mil/platform-one/big-bang/bigbang/-/tree/master/chart](https://repo1.dso.mil/platform-one/big-bang/bigbang/-/tree/master/chart)
* The Big Bang Helm Release is umbrella chart of Flux references to components (ex: HAProxy, Logging, etc) defined in other charts
* This Helm Release will create the following Flux CRs for each component
  * Flux GitRepository CRs **[3a]** that reference git repositories **[3b]** containing each component (ex: HAProxy)
  * Flux HelmRelease CRs **[3c]** which utilizes the code in the GitRepository CRs **[3d]** to creates Flux HelmChart CRs **[3e]**

### **[4]** Big Bang Component Helm Releases

* Link: [https://repo1.dso.mil/platform-one/big-bang/bigbang/-/tree/master/chart/templates](https://repo1.dso.mil/platform-one/big-bang/bigbang/-/tree/master/chart/templates)
* The Flux HelmRelease CRs renders the Helm Charts and deploys them as Helm Releases **[4]**

### **[5]** Kubernetes Namespace Resources

* Helm Releases of each Big Bang component apply the Kubernetes resources contained in the rendered Helm Release **[5]**
  * ConfigMaps
  * Services
  * Starts up Kubernetes Pods as part of the spec (ReplicaSet, DaemonSet, StatefulSet, Deployment)

### **[6]** Container Images

* Pods defined in the Helm Releases will pulls the container images **[6]** required from the container registry
* Now the Big Bang workloads are finally running!

> ************
> **_NOTE:_** The digram represents the sequence of events (not ownership), and only the HAProxy and Logging Big Bang components are depicted in this diagram for brevity
> ************