## Big Bang Deployment Flow
* Customer Deployment Repo  (built from template)
  - Link: [https://repo1.dso.mil/platform-one/big-bang/customers/template](https://repo1.dso.mil/platform-one/big-bang/customers/template)
  - Supports defining multiple environments (ex: prod and dev)
  - Upon deploying, creates the following Flux Custom Resources (CRs)
    * Flux Customer GitRepository CR **[1a]** to keep in sync between the cluster and git
    * Flux Customer Big Bang Kustomization CR **[1b]** that is kept in sync with the git repo thru the Flux Customer Big Bang GitRepository CR **[1c]**
      - This implements a customized instance of the Big Bang Repo resources
      - Link: [https://repo1.dso.mil/platform-one/big-bang/bigbang](https://repo1.dso.mil/platform-one/big-bang/bigbang)
      - The Flux Customer Big Bang Kustomization CR creates the following Flux CRs
        * Flux Big Bang GitRepository CR **[2a]** to keep in sync with the Big Bang Git Repo **[2b]**
        * Flux Big Bang HelmRelease CR **[2c]** which pulls from the Big Bang GitRepository CR **[2d]**
          - The Flux Big Bang HelmRelease CR renders and creates the Big Bang K8s Helm Release **[3]**
          - The Big Bang Helm Release is umbrella chart of Flux references to components (ex: HAProxy, Logging, etc) defined in other charts
          - This Helm Release will create the following Flux CRs for each component
            * Flux GitRepository CRs **[4]**
            * These Flux GitRepsitory CRs have references git repositories **[5]** containing each component (ex: HAProxy)
          - Pulls down a copy of the git repositories into the K8s cluster
        * Flux HelmRelease CRs **[6]**
          - Utilizes the code in the GitRepository CRs **[7]** to creates Flux HelmChart CRs **[8]**
          - Renders the Helm Charts and deploys them as Kubernetes "Helm Releases" **[9]**
            * Helm Releases of each Big Bang Component
              - Applies the Kubernetes resources contained in the rendered Helm Release **[10]**
                * ConfigMaps
                * Services
                * Starts up Kubernetes Pods as part of the spec (ReplicaSet, DaemonSet, StatefulSet, Deployment)
                  - Pods will pulls the container images **[11]** required from the container registry
                  - Now the Big Bang workloads are finally running!

                    >  ********
                    >
                    > **_NOTE:_** The digram represents the sequence of events (not ownership), and only the HAProxy and Logging Big Bang components are depicted in this diagram for brevity
                    > 
                    >  ********