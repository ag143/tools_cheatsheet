Introduction

Helm is a Kubernetes package manager for deploying helm charts (collections of pre-configured Kubernetes application resources). It features all the necessary commands for simpler management of apps in a Kubernetes cluster.

This tutorial covers all important Helm operations and provides examples to help you understand its syntax and features.

Helm Cheat Sheet
Prerequisites

A Kubernetes cluster (which you can create with minikube)
Helm package manager installed
Note: This tutorial covers Helm 3 commands. Aside from the command syntax, Helm 3 is also architecturally different from Helm 2. The most significant distinction is that Helm 3 improves security by eliminating Tiller, the server-side component present in Helm 2.

Basic Helm Concepts
Helm commands work with several Helm-related concepts. Understanding them makes the syntax easier to follow.

The most important Helm concept is a chart. A chart is a set of Kubernetes yaml manifests packaged together for easy manipulation. Helm charts make it possible to deploy a containerized application using a single command.
Charts are grouped in online collections called repositories. Each repository has a name and URL, making the charts easy to locate, download, and install.
Helm Hub is an online collection of distributed repositories available on the internet. It works as an information center, where you can find apps and their repository addresses. As of today, it is not possible to install an app directly from Helm Hub.
A release is a single instance of a chart deployed in a Kubernetes cluster.
List of Helm Commands
Use the commands listed below as a quick reference when working with Helm inside Kubernetes.

Install and Uninstall Apps
The main function of Helm is Kubernetes app management. Besides the basic operations of installing and uninstalling apps, Helm enables you to perform test installations and customize the installation process.

Install an app:

helm install [app-name] [chart]

Install an app in a specific namespace:

helm install [app-name] [chart] --namespace [namespace]

Override the default values with those specified in a file of your choice:

helm install [app-name] [chart] --values [yaml-file/url]

Run a test installation to validate and verify the chart:

helm install [app-name] --dry-run --debug

Uninstall a release:

helm uninstall [release]

Perform App Upgrade and Rollback
Helm offers users multiple options for app upgrades, such as automatic rollback and upgrading to a specific version. Rollbacks can also be executed on their own. For detailed instructions on how to perform a rollback, check out How to Roll Back Changes with Helm.

Upgrade an app:

helm upgrade [release] [chart]

Instruct Helm to rollback changes if the upgrade fails:

helm upgrade [release] [chart] --atomic

Upgrade a release. If it does not exist on the system, install it:

helm upgrade [release] [chart] --install

Upgrade to a specified version:

helm upgrade [release] [chart] --version [version-number]

Roll back a release:

helm rollback [release] [revision]

Download Release Information
The helm get command lets you download information about a release.

Download all the release information:

helm get all [release]

Download all hooks:

helm get hooks [release]

Download the manifest:

helm get manifest [release]

Download the notes:

helm get notes [release]

Download the values file:

helm get values [release]

Fetch release history:

helm history [release] 

Add, Remove, and Update Repositories
The command helm repo helps you manipulate chart repositories.

Add a repository from the internet:

helm repo add [repository-name] [url]

Remove a repository from your system:

helm repo remove [repository-name]

Update repositories:

helm repo update

List and Search Repositories
Use the helm repo and helm search commands to list and search Helm repositories. helm search also enables you to find apps and repositories in Helm Hub.

List chart repositories:

helm repo list

Generate an index file containing charts found in the current directory:

helm repo index

Search charts for a keyword:

helm search [keyword]

Search repositories for a keyword:

helm search repo [keyword]

Search Helm Hub:

helm search hub [keyword]

Release Monitoring
The helm list command enables listing releases in a Kubernetes cluster according to several criteria, including using regular (Pearl compatible) expressions to filter results. Commands such as helm status and helm history provide more details about releases.

List all available releases in the current namespace:

helm list

List all available releases across all namespaces:

helm list --all-namespaces

List all releases in a specific namespace:

helm list --namespace [namespace]

List all releases in a specific output format:

helm list --output [format]

Apply a filter to the list of releases using regular expressions:

helm list --filter '[expression]'

See the status of a specific release:

helm status [release]

Display the release history:

helm history [release]

See information about the Helm client environment:

helm env

Note: Learn more about managing Kubernetes cluster namespaces and unwanted or multiple copies of Helm deployments by referring to our article How To Delete Helm Deployment And Namespace.

Plugin Management
Install, manage and remove Helm plugins by using the helm plugin command.

Install plugins:

helm plugin install [path/url1] [path/url2] ...

View a list of all installed plugins:

helm plugin list

Update plugins:

helm plugin update [plugin1] [plugin2] ...

Uninstall a plugin:

helm plugin uninstall [plugin]

Chart Management
Helm charts use Kubernetes resources to define an application. To find out more about their structure and requirements for their creation, refer to How to Create a Helm Chart.

Create a directory containing the common chart files and directories (chart.yaml, values.yaml, charts/ and templates/):

helm create [name]

Package a chart into a chart archive:

helm package [chart-path]

Run tests to examine a chart and identify possible issues:

helm lint [chart]

Inspect a chart and list its contents:

helm show all [chart] 

Display the chart’s definition:

helm show chart [chart] 

Display the chart’s values:

helm show values [chart]

Download a chart:

helm pull [chart]

Download a chart and extract the archive’s contents into a directory:

helm pull [chart] --untar --untardir [directory]

Display a list of a chart’s dependencies:

helm dependency list [chart]

Get Help and Version Information
Display the general help output for Helm:

helm --help

Show help for a particular helm command:

helm [command] --help

See the installed version of Helm:

helm version

Helm Cheat Sheet PDF

https://www.tutorialworks.com/helm-cheatsheet/

Helm: A cheat sheet
Written by Tom Donohue 

Updated: 10 January 2022

Helm is such a powerful tool and one to check out when you’re learning Kubernetes. In this article we’ll take a look at what it is, some of the main concepts, and some useful commands for Helm.

ON THIS PAGE
What is Helm?
Why is Helm called the ‘package manager for Kubernetes’?
Helm concepts
Helm commands
Finding and viewing charts
Installing and managing charts
Getting information about deployed charts
Uninstalling a release
Developing your own charts
Useful links
Advertisements

What is Helm?
Helm is called the package manager for Kubernetes. It makes it easier to package and deploy software on a Kubernetes cluster using app definitions called charts.

A chart is a package that can be shared and reused, which contains an application’s Kubernetes resource definitions (YAML files) and some templating logic.

Helm keeps track of the charts it installs on your Kubernetes cluster by adding labels to the objects created. These labels look like app.kubernetes.io/managed-by=Helm and app.kubernetes.io/instance: myapp.

Why is Helm called the ‘package manager for Kubernetes’?
Helm works like a package manager because it can discover, install, upgrade and uninstall software on a Kubernetes cluster.

In this way, it can be thought of like apt for Ubuntu, or yum for Fedora, which are all capable of discovering, downloading and installing software onto a system.

However there is an important distinction. A Helm chart repository does not usually contain the Docker images that will be pulled onto the Kubernetes. Instead, each Helm chart usually references images by pointing to their location in a public or private Docker registry.

Helm concepts
Chart is a package in Helm. It has a name, and contains a set of Kubernetes resource definitions that are used to deploy your application.

Repository is an online collection of charts. It has a URL. You can search, download and install charts from a repository.

Release is an instance or a deployment of a chart. When you perform a helm install command, you are creating a new release of that chart on your Kubernetes cluster.

Advertisements

Helm commands
So now let’s get into the most common Helm commands. Here’s your cheatsheet for using Helm!

Finding and viewing charts
Add a remote chart repository
This adds a collection of charts called a repository. Repositories often contain many charts, and can be found on an open source repository server like Chartmuseum, or a public chart repository like ArtifactHub:

helm repo add [name] [url]
Example - using the Bitnami chart repository:

helm repo add bitnami https://charts.bitnami.com/bitnami

List all your repositories
You can see all of the repositories (the remote locations for downloading charts) that you have added to your helm installation:

$ helm repo list
NAME         	URL                                              
kedacore     	https://kedacore.github.io/charts                
stable       	https://kubernetes-charts.storage.googleapis.com/
openfaas     	https://openfaas.github.io/faas-netes/           
jenkins      	https://charts.jenkins.io                        
bitnami      	https://charts.bitnami.com/bitnami               
concourse    	https://concourse-charts.storage.googleapis.com/
Search for a chart
Using helm search will search either Artifact Hub (hub option) or all repositories configured on your system (repo option):

$ helm search hub prometheus
URL                                               	CHART VERSION 	APP VERSION	DESCRIPTION                                       
https://hub.helm.sh/charts/prometheus-community...	12.0.1        	2.21.0     	Prometheus is a monitoring system and time seri...
...

$ helm search repo prometheus
NAME                                 	CHART VERSION	APP VERSION	DESCRIPTION                                       
bitnami/kube-prometheus              	3.1.1        	0.43.2     	kube-prometheus collects Kubernetes manifests t...
bitnami/prometheus-operator          	0.31.1       	0.41.0     	DEPRECATED The Prometheus Operator for Kubernet...
stable/prometheus                    	11.9.1       	2.19.0     	Prometheus is a monitoring system and time seri...
View information about a chart
To get information about a chart, or its values, use helm show, with chart or values:

$ helm repo add concourse https://concourse-charts.storage.googleapis.com/
$ helm show chart concourse/concourse
$ helm show values concourse/concourse
Installing and managing charts
The most common way people make use of Helm is to find and install charts onto a Kubernetes cluster. Here are the main commands you might use when installing charts.

Advertisements

Install a chart’s dependencies
If you have mentioned a dependency in your Chart.yaml, and you need to download it:

helm dependency update
Simulate an install (dry-run)
To simulate installing a chart without actually installing it, do a dry run:

helm install --dry-run ...
NB: this does actually seem to interact with the Kubernetes API, to check if objects already exist in the cluster, etc. For a fully local rendering of a template, use helm template ... (see further below).

Install a chart by reference from a repository
This will install a chart from a repository, so you should ensure that you’ve set up the repository first.

helm install myapp-instance sourcerepo/myapp
Install a chart by reference with an explicit URL
This allows you to specify the URL to the repository when you install:

helm install --repo http://charts.example.com my-app-instance myapp
Install a chart from a packaged file
helm install myapp-instance ./path/to/myappchart.tgz
Install a chart from an unpacked chart directory
This is especially useful when you are developing a chart locally, or you have not published it to a chart repository.

helm install myapp-instance ./path/to/chart
Install a template with a values override file
If you want to override the default values in the template with another values file, use -f:

helm install -f ./my-extra-values.yml ./mychart
Note that this will override default values where they are given, otherwise use the default values.

Install a template with specific values
When you want to install a Helm chart, and override its values explicitly at the command line:

helm install --set key1=val1,key2=val2 [name] [chart]
Example, installing chart sourcerepo/myapp and overriding the property foo.bar:

helm install --set foo.bar=hello myapp-instance sourcerepo/myapp
Getting information about deployed charts
List all releases (deployed charts)
When you want to see all the charts that have been deployed on your cluster (releases):

helm list
See all objects created by a release
When you want to get the YAML of all of the objects that comprise a release, or see all of the objects that were created when you did a helm install:


helm get manifest <release-name>
You’ll need to know the release name you’re looking for (you can list all releases using helm list).

Print the status message of a release
When a chart is deployed (a release is created), a status message is shown. To print this message again:

helm status <release-name>
Uninstalling a release
To uninstall a chart release from your cluster:

helm uninstall <release-name>
Developing your own charts
Packaging your own software for use with Helm is known as creating a chart. You can create a chart yourself from scratch, or you can use the helm create chart which will initialise a chart for you.

Creating a new chart
If you want to create your own chart:

helm create [path]
helm create mychart
This will create a new skeleton or boilerplate chart at ./mychart, which is ready for you to edit and customise.

Locally render a chart template
Use helm template if you want Helm to show the output YAML from your Helm chart, if you were to helm install it. This is useful when debugging your chart:

helm template [NAME] [CHART]
helm template my-app-instance sourcerepo/my-app
helm template my-app-instance .
Useful links
  
  
  https://helm.sh/docs/helm/
  
  Helm Commands
Here you’ll find the list of CLI commands for Helm, with help info on their usage.

Quicklinks
HelmHelm CompletionHelm Completion BashHelm Completion FishHelm Completion PowershellHelm Completion ZshHelm CreateHelm DependencyHelm Dependency BuildHelm Dependency ListHelm Dependency UpdateHelm EnvHelm GetHelm Get AllHelm Get HooksHelm Get ManifestHelm Get NotesHelm Get ValuesHelm HistoryHelm InstallHelm LintHelm ListHelm PackageHelm PluginHelm Plugin InstallHelm Plugin ListHelm Plugin UninstallHelm Plugin UpdateHelm PullHelm PushHelm RegistryHelm Registry LoginHelm Registry LogoutHelm RepoHelm Repo AddHelm Repo IndexHelm Repo ListHelm Repo RemoveHelm Repo UpdateHelm RollbackHelm SearchHelm Search HubHelm Search RepoHelm ShowHelm Show AllHelm Show ChartHelm Show CrdsHelm Show ReadmeHelm Show ValuesHelm StatusHelm TemplateHelm TestHelm UninstallHelm UpgradeHelm VerifyHelm Version
  

  https://dev.to/aashiya123/helm-cheatsheet-for-beginners-1cb3
  HELM Cheatsheet: For Beginners
#
kubernetes
#
javascript
#
webdev
#
programming
The Helm is a package and operation manager for Kubernetes. Though handling Kubernetes applications and several releases can increase the development and deployment complexities. The Helm as a packaging manager allows you to wrap up all the Kubernetes components within a single package for deployment, thus reducing complexities. You can integrate several Kubernetes objects within the Helm chart, which is deployed as a whole. You can use Helm to deploy a single application or a part of an extensive application.

There are a number of tools that can be used with Helm charts to ease the Kubernetes deployment process. You can also integrate the Helm charts within the CI/CD process to automate each process, giving developers leverage to work on writing codes rather than running and handling production deployments. You can use and install the Helm with one click. Helm comes with the command-line user interface called ‘helm’ to perform the Helm functionalities.

Below are some Helm commands

helm help command
The above command will provide you information about the available Helm commands.

# helm help

If you want any details about the Helm command, you can use the helm help below.
# helm help search

helm search command
The above command will allow you to search for the charts. You can use helm search as mentioned below.

# helm search phpmyadmin

output-
NAME CHART VERSION APP VERSION DESCRIPTION
stable/phpmyadmin 4.3.5 5.0.1 DEPRECATED phpMyAdmin is an mysql administration frontend

helm fetch command
With the above command, you can download the chart locally without installing it. You can use the chart name with the fetch command to download all the charts and template files within the directory.

# helm fetch stable/phpmyadmin
# ls -ltr

output-
Total 32
-rw-r--r-- 1 root root 28921 Jun 29 11:04 phpmyadmin-4.3.5.tgz

helm install command
Using the above command, you can easily install the chart followed by the chart name. You can use the ‘- name’ option if you want to name the deploy chart and ‘- version’ to specify the chart version as per your requirement.

# helm install stable/phpmyadmin --name myphpadmin --version 4.3.3

The above command will provide the deployed resources overview, which can be checked from the Kubernetes with the below command.

# kubectl get all |grep -i myphpadmin

helm init command
You can use the above command to initialize the helm.

helm status command
You can check the chart installation status using the above command. You have to provide the chart name about which you want the status.

# helm status myphpadmin

helm list command
You can use the list command with complete details of the currently deployed chart.

# helm list

output-
NAME REVISION UPDATED STATUS CHART APP VERSION NAMESPACE
myphpadmin 1 Mon Jun 29 11:35:32 2020 DEPLOYED phpmyadmin-4.3.3 5.0.1 default

helm upgrade command
With the help of the upgrade command, you can upgrade the chart version. In the above examples, we have version 4.3.3, and now we are upgrading it to 4.3.4 using the below command.

# helm upgrade myphpadmin stable/phpmyadmin --version 4.3.4

helm history command
With the above command’s help, you can check the installed chart’s history followed by the chart name.

# helm history myphpadmin

output-

REVISION UPDATED STATUS CHART APP VERSION DESCRIPTION
1 Mon Jun 29 11:35:32 2020 SUPERSEDED phpmyadmin-4.3.3 5.0.1 Install complete
2 Mon Jun 29 11:53:48 2020 DEPLOYED phpmyadmin-4.3.4 5.0.1 Upgrade complete

helm rollback command
You can use the rollback command if you want to move to the previous version of the helm chart. You have to mention the version number with the command as mentioned below.

# helm rollback myphpadmin 1
Rollback was a success.

helm delete command
You can delete the helm chart using the delete command as mentioned below.

# helm delete myphpadmin
release "myphpadmin" deleted

helm repo list command
With the help of the above command, you can list down the repositories used currently.

# helm repo list

output-
NAME URL
stable https://kubernetes-charts.storage.googleapis.com
local http://127.0.0.1:8879/charts

helm repo update command With the help of the above command, you can update the repositories.
# helm repo update

output-
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.

helm reset command
You can use the reset command to uninstall the tiller component and the ‘-remove-helm-home’ option after the remove command to remove the helm’s home directory. You can use the ‘-f’ option to remove it forcefully.

# helm reset -f --remove-helm-home

output-
Deleting /root/.helm
Tiller (the Helm server-side component) has been uninstalled from your Kubernetes Cluster.

Chart installation and manipulation command
Creating chart template The above command will allow you to create the chart template with the .
# helm create

overriding helm values
helm install --name --values config.yaml --timeout 300 --wait stable/mysql

Setting environment variable on creating release
helm install --set x=somevalue -f config.yaml --name

To check the syntax of the helm chart
helm lint
helm lint

To upgrade the chart or variables in a release
helm upgrade --values config.yaml

To inspect the chart details along with the chart name.
helm inspect

To inspect the values assigned in the chart along with the chart name.
helm inspect values

To create package as a .tgz file [if you have chartmuseum]
_helm package _
_helm package . _

To install chart dependencies
helm dep up _
_helm dependency update
  

  
