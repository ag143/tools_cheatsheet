Terraform Command Lines
Terraform CLI tricks

terraform -install-autocomplete #Setup tab auto-completion, requires logging back in
Format and Validate Terraform code

terraform fmt #format code per HCL canonical standard
terraform validate #validate code for syntax
terraform validate -backend=false #validate code skip backend validation
Initialize your Terraform working directory

terraform init #initialize directory, pull down providers
terraform init -get-plugins=false #initialize directory, do not download plugins
terraform init -verify-plugins=false #initialize directory, do not verify plugins for Hashicorp signature
Plan, Deploy and Cleanup Infrastructure

terraform apply --auto-approve #apply changes without being prompted to enter “yes”
terraform destroy --auto-approve #destroy/cleanup deployment without being prompted for “yes”
terraform plan -out plan.out #output the deployment plan to plan.out
terraform apply plan.out #use the plan.out plan file to deploy infrastructure
terraform plan -destroy #outputs a destroy plan
terraform apply -target=aws_instance.my_ec2 #only apply/deploy changes to the targeted resource
terraform apply -var my_region_variable=us-east-1 #pass a variable via command-line while applying a configuration
terraform apply -lock=true #lock the state file so it can’t be modified by any other Terraform apply or modification action(possible only where backend allows locking)
terraform apply refresh=false # do not reconcile state file with real-world resources(helpful with large complex deployments for saving deployment time)
terraform apply --parallelism=5 #number of simultaneous resource operations
terraform refresh #reconcile the state in Terraform state file with real-world resources
terraform providers #get information about providers used in current configuration
Terraform Workspaces

terraform workspace new mynewworkspace #create a new workspace
terraform workspace select default #change to the selected workspace
terraform workspace list #list out all workspaces
Terraform State Manipulation

terraform state show aws_instance.my_ec2 #show details stored in Terraform state for the resource
terraform state pull > terraform.tfstate #download and output terraform state to a file
terraform state mv aws_iam_role.my_ssm_role module.custom_module #move a resource tracked via state to different module
terraform state replace-provider hashicorp/aws registry.custom.com/aws #replace an existing provider with another
terraform state list #list out all the resources tracked via the current state file
terraform state rm  aws_instance.myinstace #unmanage a resource, delete it from Terraform state file
Terraform Import And Outputs

terraform import aws_instance.new_ec2_instance i-abcd1234 #import EC2 instance with id i-abcd1234 into the Terraform resource named “new_ec2_instance” of type “aws_instance”
terraform import 'aws_instance.new_ec2_instance[0]' i-abcd1234 #same as above, imports a real-world resource into an instance of Terraform resource
terraform output #list all outputs as stated in code
terraform output instance_public_ip # list out a specific declared output
terraform output -json #list all outputs in JSON format
Terraform Miscelleneous commands

terraform version #display Terraform binary version, also warns if version is old
terraform get -update=true #download and update modules in the “root” module.
Terraform Console(Test out Terraform interpolations)

echo 'join(",",["foo","bar"])' | terraform console #echo an expression into terraform console and see its expected result as output
echo '1 + 5' | terraform console #Terraform console also has an interactive CLI just enter “terraform console”
echo "aws_instance.my_ec2.public_ip" | terraform console #display the Public IP against the “my_ec2” Terraform resource as seen in the Terraform state file
Terraform Graph(Dependency Graphing)

terraform graph | dot -Tpng > graph.png #produce a PNG diagrams showing relationship and dependencies between Terraform resource in your configuration/code
Terraform Taint/Untaint(mark/unmark resource for recreation -> delete and then recreate)

terraform taint aws_instance.my_ec2 #taints resource to be recreated on next apply
terraform untaint aws_instance.my_ec2 #Remove taint from a resource
terraform force-unlock LOCK_ID #forcefully unlock a locked state file, LOCK_ID provided when locking the State file beforehand
Terraform Cloud

terraform login #obtain and save API token for Terraform cloud
terraform logout #Log out of Terraform Cloud, defaults to hostname app.terraform.io
The ACG Terraform Commands Cheatsheet
The 10 most useful Terraform commands
A key part of using Terraform is learning to use the Terraform CLI by understanding the configurations.

Here are the 10 most common Terraform commands in the AWS CLI to help you navigate and use the powerful tool and started with Terraform.

So if you’ve got your Terraform binary installed and you’re ready to rock and roll, now’s a good time to familiarize yourself with the Terraform commands you’ll use most.


When you need a quick reference guide, you can always pull up Terraform’s built-in command-line documentation. There are a couple of commands for doing that:

terraform
terraform -h
terraform --help
The resulting help page will have the main commands at the top, followed by the less common or more complex commands below.


You can also enter the terraform command and then a subcommand with -h or --help to pull up a list of commands that are specific to that subcommand. Let’s do a walkthrough of the 10 Terraform commands you may end up using the most.

1. fmt
When I’ve finished my Terraform configuration, I want to make sure that everything is formatted correctly, so I run the fmt command first. This command reformats your configuration in the standard style, so it’ll make sure that the spacing and everything else is formatted correctly. If it comes back blank, that means the configuration files within your working directory are already correctly formatted. If it does format a file, it will let you know what file it touched.

2. init
After you use the format command, you’ll want to initialize your working directory to prepare it for what you need. The init command looks at your configuration files and determines which providers and modules it needs to pull down from the registry to allow your configuration to work properly.

3. validate
Once you’ve initialized the directory, it’s good to run thevalidate command before you run plan or apply. Validation will catch syntax errors, version errors, and other issues. One thing to note here is that you can’t run validate before you run the init command. You have to initialize the working directory before you can run the validation.

4. plan
Next, it’s always a good idea to do a dry run of your plan to see what it’s actually going to do. You can even use one of the subcommands with terraform plan to output your plan to apply it later.

5. apply
And then of course you have your apply command, which is one of the commands you’re going to use the most. This is the command that deploys or applies your configuration to a provider.

6. destroy
The destroy command, obviously, will destroy your infrastructure — or, when used with the target flag, individual resources within your infrastructure.

7. output
If you’ve put together a good output variable file, you can use the output command to make those defined outputs to display certain information. For example, if you’re deploying EC2 instances, you can output tag names, instance names, instance IDs, the IP of the instance, and so on. You can gather some really good information that makes it simple to look up later. And if you’re working as a team, people coming behind you can use the output command to figure things out and get up to speed.

8. show
The show command shows the current state of a saved plan, providing good information about the infrastructure you’ve deployed. For example, if you have an EC2 instance or a VM deployed in your configuration, it’ll show you the state that it’s in — if it’s up and ready or if it’s being terminated. It also provides useful information like IP addresses.

9. state
Another good way to check your work is to use the state command. If you use state and then the subcommand list, it’ll give you a consolidated list of the resources that are being managed by your configuration. If you are moving your Terraform instance, such as from a local instance to a remote backup, you would use the state mv command. And just like the show command, there’s a state show command that shows a resource in the state. You can also remove instances from a state by using the state rm command.

10. version
You’ll use the version command quite a bit to check your Terraform version, especially if you have any version conflicts. Sometimes providers work only with certain versions of Terraform, so if you’re defining those versions within your configuration, you’ll need to use the version command here and there.

Those are some of the most popular Terraform commands. Keep in mind that -h is a very handy way to quickly look up commands when you’re not 100% sure how to use one or what it does, especially when you’re getting started. Take a look at it when you get a chance!

Jesse Hoch and Moosa Khalid contributed to this post.


https://www.techbeatly.com/terraform-cheat-sheet/

Terraform Cheat Sheet


Gineesh Madapparambath
November 24, 2021


What is Terraform
Terraform is an open-source software tool to manage end to end lifecycle of your IT infrastructure. Terraform provides a consistent CLI workflow to manage hundreds of cloud services.

Latest Terraform Articles
10 Free Courses to Learn Terraform
Terraform Cheat Sheet
Creating the Elastic Stack on AWS using Terraform
Using Ansible with Terraform – Ansible Real Life Series
How to Import Existing VMWare VM’s into Terraform
HashiCorp Certified Terraform Associate – Learning & Exam Tips
Contents  hide 
What is Terraform
Latest Terraform Articles
Installing Terraform
Terraform CLI Cheat Sheet
terraform version
terraform init
terraform plan
terraform get
terraform apply
terraform destroy
terraform refresh
terraform show
terraform validate
terraform providers
terraform state
terraform graph
terraform fmt
terraform taint
terraform import
terraform workspaces
Terraform Default Plugin Directories
Terraform Variable Assignment
Resources
Share this to your network:
Installing Terraform
You can download the Terraform software from HashiCorp’s download page and use native installation methods for your operating system. Also you can install Terraform using the package managers like yum, apt, homebrew, Chocolatey (choco) etc. Refer install Terraform page for the appropriate method for your operating system.


Terraform CLI Cheat Sheet
Please note, this cheat sheet is a living document and I will make changes whenever there is an update or changes in the Terraform CLI options or versions. This cheat sheet does not written in an alphabetical order or based on workflow.

Planning HashiCorp Certified Terraform Associate Certification ? Watch the video for details.


terraform version
$ terraform version
Terraform v1.0.1
on darwin_amd64
terraform init
$ terraform init
Ask for input if necessary. If false, will error if input was required.

$ terraform init -input=false
You can also change the backend details using -backend-config option. -reconfigure will reconfigure the backend, ignoring any saved configuration.

$ terraform init -backend-config=PATH/TO/CONFIGURATION_FILE -reconfigure
terraform plan
The plan will check the configuration files (basically all the *.tf files in the directory) and will show you the items or changes going to made on target infrastructure or resources. Please note, this command will not actually perform the planned actions.

$ terraform plan
You can optionally save the plan to a file, which you can then pass to the apply command to perform exactly the actions described in the plan.

$ terraform plan -out plan.out
terraform get
Downloads and installs modules needed for the configuration given by PATH. get recursively downloads all modules needed, such as modules imported by modules imported by the root and so on. Module installation also happens automatically by default as part of
the “terraform init” command, so you should rarely need to run this command separately.

$ terraform get
You can update the already downloaded modules using -update=true option.

$ terraform get -update=true
terraform apply
apply will do the actual operation on the infrastructure resources. apply will show the plan and actions in detail.

$ terraform apply
apply will ask for your confirmation to proceed with changes. You can use -auto-approve for auto-confirmation.

$ terraform apply -auto-approve
You can pass different variables or variable files.

$ terraform plan -var="instancetype=t2.small"
$ terraform plan -var-file="custom.tfvars
You can use -target option to target specific resources, modules, or collections of resources.

$ terraform apply -target="aws_s3_bucket_object.objects"
terraform destroy
Warning: destroy will delete all resource but with confirmation.

$ terraform destroy
You can create a deletion plan as below.

$ terraform plan –destroy
Use the -target to destroy a specific resource.

$ terraform destroy -target="aws_s3_bucket_object.objects"
Also note, you can comment out the resource, then terraform will detect it as not part of config and will remove when you do plan or apply.

terraform refresh
You can update the terraform state file with metadata that matches the physical resources they are tracking.

$ terraform refresh
terraform show
Show the terraform state information in a human readable format. You can also use it for displaying information from plan file.

$ terraform show
terraform validate
You can check the syntax and validate the configuration using validate subcommand.

$ terraform validate
Success! The configuration is valid.
terraform providers
You can see the providers in use by the modules and configurations in your Terraform files.

$ terraform providers

Providers required by configuration:
.
└── provider[registry.terraform.io/hashicorp/aws]
terraform state
terraform state has multiple subcommands to manage the terraform state. You can move, rm (delete), list or show the resource state.

Subcommands:
    list                List resources in the state
    mv                  Move an item in the state
    pull                Pull current state and output to stdout
    push                Update remote state from a local state file
    replace-provider    Replace provider in the state
    rm                  Remove instances from the state
    show                Show a resource in the state
Example usages

# List state
$ terraform state list
aws_iam_user.lb
aws_instance.myec2

# Show resource
$ terraform state show aws_instance.myec2

# Push terraform state to remote backend
$ tarraform state push

# Pull the remote terraform state to a local copy
$ terraform state pull > terraform.tfstate

# Update and tell terraform that packet_device.worker has been renamed to packet_device.helper
$ terraform state mv packet_device.worker packet_device.helper

# Move the resource block into the child module configuration
$ terraform state mv packet_device.worker module.worker.packet_device.worker

# Remove the resource from state but it will not remove the resource from cloud/provider.
$ terraform state rm aws_instance.myec2
Remove the resource from state but it will not remove the resource from cloud/provider. But next time when you run terraform plan or apply, Terraform will recreate the instance as again as the resource definition is still there.

$ terraform state rm aws_instance.myec2
Removed aws_instance.myec2
Successfully removed 1 resource instance(s).
terraform graph
graph will generate the visual graph of your infrastructure based on Terraform configuration files.

Outputs the visual execution graph of Terraform resources according to
either the current configuration or an execution plan.

$ terraform graph
The output of terraform graph will be in DOT format and you can use tools like dot to generate image files from dot files.

sudo apt-get install graphviz
# or 
sudo yum install graphviz

$ terraform graph | dot –Tpng > graph.png
terraform fmt
Rewrites all Terraform configuration files to a canonical format with appropriate indentation and styling. (JSON files (.tf.json or .tfvars.json) are not modified.)

$ terraform fmt
terraform taint
You can manually mark a terraform managed resource as tainted and forcing it to be destroyed and recreated on the next apply. terraform taint command will make modification in the tfstate file and recreate action will happen in next apply. Please note, terraform taint command will not modify the .tf file or the infrastructure.

$ terraform taint aws_instance.myec2
terraform import
You can import your existing infrastructure into Terraform and manage using Terraform.

# Importing VMWare VM to terraform
$ terraform import vsphere_virtual_machine.vm /DC1/vm/DEV/DEV2
Read our detailed guide: How to Import Existing VMWare VM’s into Terraform

terraform workspaces
Terraform Workspaces will help to manage same terraform configurations for different environments (eg: dev, staging, production) in the same project directory.

# Check the workspace
$ terraform workspace show
default

# Create new workspace
$ terraform workspace new dev
Created and switched to workspace "dev"!

# List all workspaces
$ terraform workspace list
  default
* dev

# Switch to a specific workspace
$ terraform workspace select dev
Switched to workspace "dev".
Terraform will create separate terraform.tfstate files in terraform.tfstate.d/WORKSPACE_NAME/ directories in the project directory.

$ tree terraform.tfstate.d/
terraform.tfstate.d/
├── dev
│   └── terraform.tfstate
├── prod
└── stage
    └── terraform.tfstate

3 directories, 2 files
You can use ${terraform.workspace} interpolation to dynamically use the workspace name inside your terraform configuration (*.tf). Eg: you can use it for selecting instance type from an array based on workspace.

resource "aws_instance" "myec2" {
  ami           = "ami-0cd31be676780afa7"
  instance_type = lookup(var.instance_type,terraform.workspace)

}
variable "instance_type" {
 type = map

 default = {
   default = "t2.nano"
   stage = "t2.nano"
   dev = "t2.micro"
   prod = "t2.large"
 }
}
Or you can use this ${terraform.workspace} for tagging the instance.

resource "aws_instance" "example" {
  # ... other arguments
  tags = {
    Name = "web-${terraform.workspace}"
  }
}
Refer Terraform Workspaces documentation for more details.

Terraform Default Plugin Directories
Windows: %APPDATA%\terraform.d\plugins
All other systems: ~/.terraform.d/plugins
Terraform Variable Assignment
You can pass variables to Terraform in different methods.

1. Environment variables – with a prefix TF_VAR_

$ export TF_VAR_instance_type=t2.micro
2. Command Line Flags

$ terraform plan -var="instancetype=t2.small"
3. From a variable file – use terraform.tfvars – terraform will load all variables from this file. If different var files to be used then,

$ terraform plan -var-file="custom.tfvars
4. Variable Defaults – can keep variable default in another .tf file.

$ cat variables.tf
variable "my_ip" {
default = "10.1.10.10/32"
}
if no value mentioned, then default value will be used.
if default value not defined, then terraform will ask for variable when you do apply or plan operation.

https://jayendrapatil.com/terraform-cheat-sheet/

Terraform Cheat Sheet
NOVEMBER 30, 2020 ~ LAST UPDATED ON : NOVEMBER 30, 2020 ~ JAYENDRAPATIL
Table of Contents  hide 
Terraform Architecture
Terraform Workflow
Terraform Modules
Terraform Read and write configuration
Terraform Backends
Terraform State Management
State Security
Terraform Logging
Terraform Cloud and Terraform Enterprise
Advertisement
An open source provisioning declarative tool that based on Infrastructure as a Code paradigm

designed on immutable infrastructure principles
Written in Golang and uses own syntax – HCL (Hashicorp Configuration Language), but also supports JSON
Helps to evolve the infrastructure, safely and predictably
Applies Graph Theory to IaaC and provides Automation, Versioning and Reusability
Terraform is a multipurpose composition tool:
○ Composes multiple tiers (SaaS/PaaS/IaaS)
○ A plugin-based architecture model
Terraform is not a cloud agnostic tool. It embraces all major Cloud Providers and provides common language to orchestrate the infrastructure resources
Terraform is not a configuration management tool and other tools like chef, ansible exists in the market.
Terraform Architecture
Terraform Architecture

Terraform Providers (Plugins)
provide abstraction above the upstream API and is responsible for understanding API interactions and exposing resources.

Invoke only upstream APIs for the basic CRUD operations
Providers are unaware of anything related to configuration loading, graph
theory, etc.
supports multiple provider instances using alias for e.g. multiple aws provides with different region
can be integrated with any API using providers framework
Most providers configure a specific infrastructure platform (either cloud or self-hosted).
can also offer local utilities for tasks like generating random numbers for unique resource names.
Terraform Provisioners
run code locally or remotely on resource creation
local exec executes code on the machine running terraform
remote exec
runs on the provisioned resource
supports ssh and winrm
requires inline list of commands
should be used as a last resort
are defined within the resource block.
support types – Create and Destroy
if creation time fails, resource is tainted if provisioning failed, by default. (next apply it will be re-created)
behavior can be overridden by setting the on_failure to continue, which means ignore and continue
for destroy, if it fails – resources are not removed
Terraform Workspaces
helps manage multiple distinct sets of infrastructure resources or environments with the same code.
just need to create needed workspace and use them, instead of creating a directory for each environment to manage
state files for each workspace are stored in the directory terraform.tfstate.d
terraform workspace new dev creates a new workspace and switches to it as well
terraform workspace select dev helps select workspace
terraform workspace list lists the workspaces and shows the current active one with *
does not provide strong separation as it uses the same backend
Terraform Workflow
Terraform Workflow

init


initializes a working directory containing Terraform configuration files.
performs
backend initialization , storage for terraform state file.
modules installation, downloaded from terraform registry to local path
provider(s) plugins installation, the plugins are downloaded in the sub-directory of the present working directory at the path of .terraform/plugins
supports -upgrade to update all previously installed plugins to the newest version that complies with the configuration’s version constraints
is safe to run multiple times, to bring the working directory up to date with changes in the configuration
does not delete the existing configuration or state
validate

validates syntactically for format and correctness.
is used to validate/check the syntax of the Terraform files.
verifies whether a configuration is syntactically valid and internally consistent, regardless of any provided variables or existing state.
A syntax check is done on all the terraform files in the directory, and will display an error if any of the files doesn’t validate.
plan


create a execution plan
traverses each vertex and requests each provider using parallelism
calculates the difference between the last-known state and
the current state and presents this difference as the output of the terraform plan operation to user in their terminal
does not modify the infrastructure or state.
allows a user to see which actions Terraform will perform prior to making any changes to reach the desired state
will scan all *.tf  files in the directory and create the plan
will perform refresh for each resource and might hit rate limiting issues as it calls provider APIs
all resources refresh can be disabled or avoided using
-refresh=false or
target=xxxx or
break resources into different directories.
supports -out to save the plan
apply


apply changes to reach the desired state.
scans the current directory for the configuration and applies the changes appropriately.
can be provided with a explicit plan, saved as out from terraform plan
If no explicit plan file is given on the command line, terraform apply will create a new plan automatically and prompt for approval to apply it
will modify the infrastructure and the state.
if a resource successfully creates but fails during provisioning,
Terraform will error and mark the resource as “tainted”.
A resource that is tainted has been physically created, but can’t be considered safe to use since provisioning failed.
Terraform also does not automatically roll back and destroy the resource during the apply when the failure happens, because that would go against the execution plan: the execution plan would’ve said a resource will be created, but does not say it will ever be deleted.
does not import any resource.
supports -auto-approve to apply the changes without asking for a confirmation
supports -target to apply a specific module
refresh

used to reconcile the state Terraform knows about (via its state file) with the real-world infrastructure
does not modify infrastructure, but does modify the state file
destroy

destroy the infrastructure and all resources
modifies both state and infrastructure
terraform destroy -target can be used to destroy targeted resources
terraform plan -destroy allows creation of destroy plan
import

helps import already-existing external resources, not managed by Terraform, into Terraform state and allow it to manage those resources
Terraform is not able to auto-generate configurations for those imported modules, for now, and requires you to first write the resource definition in Terraform and then import this resource
taint

marks a Terraform-managed resource as tainted, forcing it to be destroyed and recreated on the next apply.
will not modify infrastructure, but does modify the state file in order to mark a resource as tainted. Infrastructure and state are changed in next apply.
can be used to taint a resource within a module
fmt

format to lint the code into a standard format
console

command provides an interactive console for evaluating expressions.
Terraform Modules
enables code reuse
supports versioning to maintain compatibility
stores code remotely
enables easier testing
enables encapsulation with all the separate resources under one configuration block
modules can be nested inside other modules, allowing you to quickly spin up whole separate environments.
can be referred using source attribute
supports Local and Remote modules
Local modules are stored alongside the Terraform configuration (in a separate directory, outside of each environment but in the same repository) with source path ./ or ../
Remote modules are stored externally in a separate repository, and supports versioning
supports following backends
Local paths
Terraform Registry
GitHub
Bitbucket
Generic Git, Mercurial repositories
HTTP URLs
S3 buckets
GCS buckets
Module requirements
must be on GitHub and must be a public repo, if using public registry.
must be named terraform-<PROVIDER>-<NAME>, where <NAME> reflects the type of infrastructure the module manages and <PROVIDER> is the main provider where it creates that infrastructure. for e.g. terraform-google-vault or terraform-aws-ec2-instance.
must maintain x.y.z tags for releases to identify module versions. Release tag names must be a semantic version, which can optionally be prefixed with a v for example, v1.0.4 and 0.9.2. Tags that don’t look like version numbers are ignored.
must maintain a Standard module structure, which allows the registry to inspect the module and generate documentation, track resource usage, parse submodules and examples, and more.
Terraform Read and write configuration
terraform_sample

Resources
resource are the most important element in the Terraform language that describes one or more infrastructure objects, such as compute instances etc
resource type and local name together serve as an identifier for a given resource and must be unique within a module for e.g.  aws_instance.local_name
Data Sources
data allow data to be fetched or computed for use elsewhere in Terraform configuration
allows a Terraform configuration to make use of information defined outside of Terraform, or defined by another separate Terraform configuration
Variables
variable serve as parameters for a Terraform module and act like function arguments
allows aspects of the module to be customized without altering the module’s own source code, and allowing modules to be shared between different configurations
can be defined through multiple ways
command line for e.g.-var="image_id=ami-abc123"
variable definition files .tfvars or .tfvars.json. By default, terraform automatically loads
Files named exactly terraform.tfvars or terraform.tfvars.json.
Any files with names ending in .auto.tfvars or .auto.tfvars.json
file can also be passed with -var-file
environment variables can be used to set variables using the format TF_VAR_name
Environment variables
terraform.tfvars file, if present.
terraform.tfvars.json file, if present.
Any *.auto.tfvars or *.auto.tfvars.json files, processed in lexical order of their filenames.
Any -var and -var-file options on the command line, in the order they are provided.Terraform loads variables in the following order, with later sources taking precedence over earlier ones:
Local Values
locals assigns a name to an expression, allowing it to be used multiple times within a module without repeating it.
are like a function’s temporary local variables.
helps to avoid repeating the same values or expressions multiple times in a configuration.
Output
are like function return values.
output can be marked as containing sensitive material using the optional sensitive argument, which prevents Terraform from showing its value in the list of outputs. However, they are still stored in the state as plain text.
In a parent module, outputs of child modules are available in expressions as module.<MODULE NAME>.<OUTPUT NAME>.
Named Values
is an expression that references the associated value for e.g. aws_instance.local_name, data.aws_ami.centos, var.instance_type etc.
support Local named values for e.g count.index
Dependencies
identifies implicit dependencies as Terraform automatically infers when one resource depends on another by studying the resource attributes used in interpolation expressions for e.g aws_eip on resource aws_instance
explicit dependencies can be defined using depends_on where dependencies between resources that are not visible to Terraform
Data Types
supports primitive data types of
string, number and bool
Terraform language will automatically convert number and bool values to string values when needed
supports complex data types of
list – a sequence of values identified by consecutive whole numbers starting with zero.
map – a collection of values where each is identified by a string label.
set –  a collection of unique values that do not have any secondary identifiers or ordering.
supports structural data types of
object – a collection of named attributes that each have their own type
tuple – a sequence of elements identified by consecutive whole numbers starting with zero, where each element has its own type.
Built-in Functions
includes a number of built-in functions that can be called from within expressions to transform and combine values for e.g. min, max, file, concat, element, index, lookup etc.
does not support user-defined functions
Dynamic Blocks
acts much like a for expression, but produces nested blocks instead of a complex typed value. It iterates over a given complex value, and generates a nested block for each element of that complex value.
Terraform Comments
supports three different syntaxes for comments:
#
//
/* and */
Terraform Backends
determines how state is loaded and how an operation such as apply is executed
are responsible for storing state and providing an API for optional state locking
needs to be initialized
if switching the backed for the first time setup, Terraform provides a migration option
helps
collaboration and working as a team, with the state maintained remotely and state locking
can provide enhanced security for sensitive data
support remote operations
supports local vs remote backends
local (default) backend stores state in a local JSON file on disk
remote backend stores state remotely like S3, OSS, GCS, Consul and support features like remote operation, state locking, encryption, versioning etc.
supports partial configuration with remaining configuration arguments provided as part of the initialization process
Backend configuration doesn’t support interpolations.
GitHub is not the supported backend type in Terraform.
Terraform State Management
state helps keep track of the infrastructure Terraform manages
stored locally in the terraform.tfstate
recommended not to edit the state manually
Use terraform state command
mv – to move/rename modules
rm – to safely remove resource from the state. (destroy/retain like)
pull – to observe current remote state
list & show – to write/debug modules

State Locking
happens for all operations that could write state, if supported by backend
prevents others from acquiring the lock & potentially corrupting the state
backends which support state locking are
azurerm
Hashicorp consul
Tencent Cloud Object Storage (COS)
etcdv3
Google Cloud Storage GCS
HTTP endpoints
Kubernetes Secret with locking done using a Lease resource
AliCloud Object Storage OSS with locking via TableStore
PostgreSQL
AWS S3 with locking via DynamoDB
Terraform Enterprise
Backends which do not support state locking are
artifactory
etcd
can be disabled for most commands with the -lock flag
use force-unlock command to manually unlock the state if unlocking failed
State Security
can contain sensitive data, depending on the resources in use for e.g passwords and keys
using local state, data is stored in plain-text JSON files
using remote state, state is held in memory when used by Terraform. It may be encrypted at rest, if supported by backend for e.g. S3, OSS
Terraform Logging
debugging can be controlled using TF_LOG , which can be configured for different levels TRACE, DEBUG, INFO, WARN or ERROR, with TRACE being the more verbose.
logs path can be controlled TF_LOG_PATH.  TF_LOG needs to be specified.
Terraform Cloud and Terraform Enterprise
Terraform Cloud provides Cloud Infrastructure Automation as a Service. It is offered as a multi-tenant SaaS platform and is designed to suit the needs of smaller teams and organizations. Its smaller plans default to one run at a time, which prevents users from executing multiple runs concurrently.
Terraform Enterprise is a private install for organizations who prefer to self-manage. It is designed to suit the needs of organizations with specific requirements for security, compliance and custom operations.
Terraform Cloud provides features
Remote Terraform Execution – supports Remote Operations for Remote Terraform execution which helps provide consistency and visibility for critical provisioning operations.
Workspaces – organizes infrastructure with workspaces instead of directories. Each workspace contains everything necessary to manage a given collection of infrastructure, and Terraform uses that content whenever it executes in the context of that workspace.
Remote State Management – acts as a remote backend for the Terraform state. State storage is tied to workspaces, which helps keep state associated with the configuration that created it.
Version Control Integration – is designed to work directly with the version control system (VCS) provider.
Private Module Registry – provides a private and central library of versioned & validated modules to be used within the organization
Team based Permission System – can define groups of users that match the organization’s real-world teams and assign them only the permissions they need
Sentinel Policies – embeds the Sentinel policy-as-code framework, which lets you define and enforce granular policies for how the organization provisions infrastructure. Helps eliminate provisioned resources that don’t follow security, compliance, or operational policies.
Cost Estimation – can display an estimate of its total cost, as well as any change in cost caused by the proposed updates
Security – encrypts state at rest and protects it with TLS in transit.

Terraform Enterprise features
includes all the Terraform Cloud features with
Audit – supports detailed audit logging and tracks the identity of the user requesting state and maintains a history of state changes.
SSO/SAML – SAML for SSO provides the ability to govern user access to your applications.
Terraform Enterprise currently supports running under the following operating systems for a Clustered deployment:
Ubuntu 16.04.3 – 16.04.5 / 18.04
Red Hat Enterprise Linux 7.4 through 7.7
CentOS 7.4 – 7.7
Amazon Linux
Oracle Linux
Clusters currently don’t support other Linux variants.
Terraform Cloud currently supports following VCS Provider
GitHub.com
GitHub.com (OAuth)
GitHub Enterprise
GitLab.com
GitLab EE and CE
Bitbucket Cloud
Bitbucket Server
Azure DevOps Server
Azure DevOps Services
A Terraform Enterprise install that is provisioned on a network that does not have Internet access is generally known as an air-gapped install. These types of installs require you to pull updates, providers, etc. from external sources vs. being able to download them directly.
  
https://k21academy.com/terraform-iac/terraform-cheat-sheet/
  
Terraform Cheat Sheet

February 2, 2021 by Sumit Singh Leave a Comment

 9319 views
No more terror of commands!

Terraform has quickly become popular across the world to develop and change infrastructure. Terraform provides a command-line interface called Terraform CLI. However, for most people, terraform commands seem intimidating at first if you are not used to using the terminal. Of course, you won’t know all commands by heart as it takes hours of practice. Cheat sheets come in handy while you are still working on memorizing these commands.

In this blog, I have covered the Terraform cheat sheet consisting of the most basic and widely used Terraform commands and have tried to break down some of the most commonly used commands by category.

If you are a beginner in Terraform and confused about where to start? Check out our blog Terraform Beginner’s Guide: Everything You Should Know

What Is Terraform?
Terraform is one of the most popular Infrastructure-as-code (IaC) tool, used by DevOps teams to automate infrastructure tasks. It is used to automate the provisioning of your cloud resources. Terraform is an open-source, cloud-agnostic provisioning tool developed by HashiCorp and written in GO language.

If you want to know more about Terraform certification please go through our previous blog on Terraform Certification

Terraform Command Line Interface (CLI)
Terraform command-line interface is via the terraform command which accepts a variety of subcommands such as terraform init or terraform plan and it is commonly referred to as “Terraform CLI”.

Terraform CLI Help

Terraform has a built-in help system that can be accessed from the command line for commands that you are not familiar with, or want to learn more about. You can get specific help for any specific command, use the -help option with the relevant subcommand. For example, to see help about the “state” subcommand you can run terraform state -help.

terraform <commandName> -help

terraform cli help
Also Check: How to Install Terraform. Click here

Terraform Cheat Sheet
Terraform command accepts a variety of subcommands such as terraform init or terraform plan. Some of the widely used commands and subcommands  I have listed below.

Show version
terraform –version	Shows terraform version installed
 
Initialize infrastructure
terraform init	Initialize a working directory
terraform init -input=true	Ask for input if necessary
terraform init -lock=false	Disable locking of state files during state-related operations
 
Get
terraform get	downloads and update modules mentioned in the root module
terraform get -update=true	modules already downloaded will be checked for updates and updated
 
Provision infrastructure
terraform plan	Creates an execution plan (dry run)
terraform plan -out=path	save generated plan output as a file
terraform plan -destroy	Outputs a destroy plan
terraform apply	Executes changes to the actual environment
terraform apply –auto-approve	Apply changes without being prompted to enter ”yes”
terraform apply -refresh=true	Update the state for each resource prior to planning and applying
terraform apply -input=false	Ask for input for variables if not directly set
terraform apply -var ‘foo=bar’	Set a variable in the Terraform configuration, can be used multiple times
terraform apply -var-file=foo	Specify a file that contains key/value pairs for variable values
terraform apply -target	Only apply/deploy changes to the targeted resource
terraform destroy –auto-approve	Destroy/cleanup without being prompted to enter ”yes”
terraform destroy -target	Only destroy the targeted resource and its dependencies
 
Terraform Workspaces
terraform workspace new	Create a new workspace and select it
terraform workspace select	Select an existing workspace
terraform workspace list	List the existing workspaces
terraform workspace show	Show the name of the current workspace
terraform workspace delete	Delete an empty workspace
 
Format and validate Terraform Code
terraform fmt	Format code as per HCL canonical standard
terraform validate	validate configuration files for syntax
 
Inspect Infrastructure
terraform graph	creates a resource graph listing all resources in your configuration and their dependencies.
terraform output	List all the outputs for the root module
terraform output instance_public_ip	List only the specified output
terraform output -json	List all the outputs in JSON format
terraform show	provide human-readable output from a state or plan file
 
Terraform Import
terraform import aws_instance.foo i-abcd1234	import an AWS instance with ID i-abcd1234 into aws_instance resource named “foo”
 
Terraform State Manipulation
terraform state list	list all resources in the state file
terraform state list aws_instance.my_ec2	Only list resource with the given name
terraform state mv	move an item in the state file
terraform state rm	Remove items from the state file
terraform state pull	Pull current state and output to stdout
terraform state push	Update remote state from a local state file
terraform state show aws_instance.my_ec2	Show the attributes of a single resource
terraform state refresh	
 
Terraform miscellaneous commands
echo “1 + 5” | terraform console	Display expected result of an expression as output
terraform taint aws_instance.my_ec2	marks a Terraform-managed resource as tainted, forcing it to be destroyed and recreated on the next apply
terraform untaint aws_instance.my_ec2	unmarks a Terraform-managed resource as tainted
terraform force-unlock LOCK_ID	removes the lock on the state for the current configuration
terraform login	obtain and save an API token for Terraform Cloud
terraform logout	remove credentials stored by terraform login
Also Read: Our blog post on Terraform Create VM. Click here

Conclusion
In this blog, I have tried to cover most of the widely used Terraform commands in a cheat sheet. I must say that Terraform official docs are amazing and I, myself would like to prefer them most of the time but sometimes all you need is a simple, handy reference to get the work done.

Also Check: Our blog post on Terraform Interview Questions. Click here

Frequently Asked Questions (FAQs)
By now, you must have a brief understanding of Terraform CLI commands. Let’s take a look at some frequently asked questions, asked by trainees in our HashiCorp Infrastructure Automation Certification: Terraform Associate training.

Q: What is command fmt?
A: The terraform fmt command is used to rewrite Terraform configuration files to a canonical format and style. This command applies a subset of the Terraform language style conventions, along with other minor adjustments for readability.

Q: What is the command taint?
A: The terraform taint command manually marks a Terraform-managed resource as tainted, forcing it to be destroyed and recreated on the next apply. This command will not modify infrastructure but does modify the state file in order to mark a resource as tainted. Once a resource is marked as tainted, the next plan will show that the resource will be destroyed and recreated and the next apply will implement this change.

Check Out: Our blog post on Terraform Workflow. Click here

Q: What are workspaces?
A: Each Terraform configuration has an associated backend that defines how operations are executed and where persistent data such as the Terraform state are stored.
The persistent data stored in the backend belongs to a workspace. Initially, the backend has only one workspace, called “default”, and thus there is only one Terraform state associated with that configuration.

Q: Can you delete the default workspace?
A: No. You can’t ever delete the default workspace.

  https://dzone.com/articles/terraform-cli-cheat-sheet
  Terraform CLI Cheat Sheet
If all you need is a quick look, take a look at this reference article for Terraform CLI.
Aurelie Vache user avatar by Aurelie Vache · Dec. 19, 18 · DevOps Zone · Tutorial
 Like (11)
  Comment (2) 
 Save
   Tweet  90.42K Views
Join the DZone community and get the full member experience. JOIN FOR FREE

When we want to use a tool or improve our expertise in a particular technology, it's good to read a lot of articles and, of course, to manipulate the technology.

But sometimes, it can be useful to have a simple cheat sheet of the tool.

You might also enjoy Linode's Beginner's Guide to Terraform.

About Terraform CLI
Terraform, a tool created by Hashicorp in 2014, written in Go, aims to build, change and version control your infrastructure. This tool has a powerful and very intuitive Command Line Interface.

Installation
Install Through curl
1
$ curl -O https://releases.hashicorp.com/terraform/0.11.10/terraform_0.11.10_linux_amd64.zip
2
$ sudo unzip terraform_0.11.10_linux_amd64.zip -d /usr/local/bin/
3
$ rm terraform_0.11.10_linux_amd64.zip


...or Install Through tfenv, a Terraform Version Manager
First of all, download the tfenv binary and put it in your PATH.

1
$ git clone https://github.com/Zordrak/tfenv.git ~/.tfenv
2
$ echo 'export PATH="$HOME/.tfenv/bin:$PATH"' >> $HOME/bashrc


Then, you can install the desired version of Terraform:

1
$ tfenv install 0.11.10
Usage
Show Version
1
$ terraform --version
2
 Terraform v0.11.10
Init Terraform
$ terraform init

It’s the first command you need to execute. Unless  terraform plan , apply  , destroy   and import  will not work. The command terraform init   will install :

Terraform modules
Eventually a backend
Provider(s) plugins
Init Terraform and Don’t Ask Any Input
$ terraform init -input=false

Change Backend Configuration During the Init
$ terraform init -backend-config=cfg/s3.dev.tf -reconfigure

-reconfigure is used in order to tell Terraform to not copy the existing state to the new remote state location.

Get
This command is useful when you have defined some modules. Modules are vendored, so when you edit them, you need to get again modules content.

$ terraform get -update=true

When you use modules, the first thing you’ll have to do is to do a  terraform get . This pulls modules into the .terraform directory. Once you do that, unless you do another terraform get -update=true, you’ve essentially vendored those modules.

Plan
The plan step check configuration to execute and write a plan to apply to target infrastructure provider.

$ terraform plan -out plan.out

It’s an important feature of Terraform that allows a user to see which actions Terraform will perform prior to making any changes, increasing confidence that a change will have the desired effect once applied.

When you execute terraform plan, Terraform will scan all *.tf files in your directory and create the plan.

Apply
Now you have the desired state so you can execute the plan.

$ terraform apply plan.out

Good to know: Since Terraform v0.11+, in an interactive mode (non CI/CD/autonomous pipeline), you can just execute terraform apply command which will print out which actions TF will perform.

By generating the plan and applying it in the same command, Terraform can guarantee that the execution plan won’t change, without needing to write it to disk. This reduces the risk of potentially-sensitive data being left behind, or accidentally checked into version control.

$ terraform apply

Apply and Auto Approve
$ terraform apply -auto-approve

Apply and Define New Variables Value
1
$ terraform apply -auto-approve -var tags-repository_url=${GIT_URL}


Apply Only One Module
1
$ terraform apply -target=module.s3


This -target option works with Terraform plan too.

Destroy
$ terraform destroy

Delete all the resources!

A deletion plan can be created before:

$ terraform plan –destroy

-target option allows to destroy only one resource, for example, an S3 bucket :

1
$ terraform destroy -target aws_s3_bucket.my_bucket


Debug
The terraform console  command is useful for testing interpolations before using them in configurations. Terraform console will read configured state even if it is remote.

1
$ echo "aws_iam_user.notif.arn" | terraform console
2
arn:aws:iam::123456789:user/notif


Graph
$ terraform graph | dot –Tpng > graph.png

Visual dependency graph of Terraform resources.

Validate
The validate command is used to validate/check the syntax of the Terraform files. A syntax check is done on all the Terraform files in the directory and will display an error if any of the files don’t validate. The syntax check does not cover every syntax common issues.

1
$ terraform validate


Providers
You can use a lot of providers/plugins in your Terraform definition resources, so it can be useful to have a tree of providers used by modules in your project.

1
$ terraform providers
2
.
3
├── provider.aws ~> 1.24.0
4
├── module.my_module
5
│   ├── provider.aws (inherited)
6
│   ├── provider.null
7
│   └── provider.template
8
└── module.elastic
9
    └── provider.aws (inherited)


State
Pull Remote State in A Local Copy
1
$ terraform state pull > terraform.tfstate


Push State in a Remote Backend storage
1
$ terraform state push


This command is useful if, for example, you originally use a local tf state and then you define backend storage, in S3 or Consul…

How to Tell to Terraform You Moved a Resource in A Module?
If you moved an existing resource in a module, you need to update the state:

1
$ terraform state mv aws_iam_role.role1 module.mymodule


How to Import Existing Resource in Terraform?
If you have an existing resource in your infrastructure provider, you can import it in your Terraform state:

1
$ terraform import aws_iam_policy.elastic_post
2
arn:aws:iam::123456789:policy/elastic_post


Workspaces
To manage multiple distinct sets of infrastructure resources/environments.

Instead of creating a directory for each environment to manage, we need to just create needed workspace and use them:

Create Workspace
This command creates a new workspace and then select it

$ terraform workspace new dev

Select a Workspace
$ terraform workspace select dev

List Workspaces


1
$ terraform workspace list
2
  default
3
* dev
4
  preprod


Show Current Workspace


1
$ terraform workspace show
2
dev


Tools
jq
jq is a lightweight command-line JSON processor. Combined with Terraform output it can be powerful.

Installation
For Linux:

$ sudo apt-get install jq

or

$ yum install jq

For OS X:

$ brew install jq

Usage
For example, we defined outputs in a module and when we execute terraform apply outputs are displayed:



1
$ terraform apply
2
...
3
Apply complete! Resources: 0 added, 0 changed, 0 destroyed.
4
​
5
Outputs:
6
​
7
elastic_endpoint = vpc-toto-12fgfd4d5f4ds5fngetwe4.eu-central-1.es.amazonaws.com


We can extract the value that we want in order to use it in a script for example. With jq it’s easy:

1
$ terraform output -json
2
{
3
    "elastic_endpoint": {
4
        "sensitive": false,
5
        "type": "string",
6
        "value": "vpc-toto-12fgfd4d5f4ds5fngetwe4.eu-central-1.es.amazonaws.com"
7
    }
8
}
9
​
10
$ terraform output -json | jq '.elastic_endpoint.value'
11
"vpc-toto-12fgfd4d5f4ds5fngetwe4.eu-central-1.es.amazonaws.com"


Terraforming
If you have an existing AWS accountl for examples with existing components like S3 buckets, SNS, VPC … You can use Terraforming tool, a tool written in Ruby, which extracts existing AWS resources and converts it to Terraform files!

Installation
$ sudo apt install ruby or $ sudo yum install ruby

and

1
$ gem install terraforming


Usage
Pre-requisites:
Like for Terraform, you need to set AWS credentials:

1
$ export AWS_ACCESS_KEY_ID="an_aws_access_key"
2
$ export AWS_SECRET_ACCESS_KEY="a_aws_secret_key"
3
$ export AWS_DEFAULT_REGION="eu-central-1"


You can also specify credential profile in ~/.aws/credentials_s and with _–profile option.

1
$ cat ~/.aws/credentials
2
[aurelie]
3
aws_access_key_id = xxx
4
aws_secret_access_key = xxx
5
aws_default_region = eu-central-1


1
$ terraforming s3 --profile aurelie


Usage
1
$ terraforming --help
2
Commands:
3
terraforming alb # ALB
4
...
5
terraforming vgw # VPN Gateway
6
terraforming vpc # VPC


Example:

$ terraforming s3 > aws_s3.tf



Remarks: As you can see, Terraforming can’t extract API gateway resources for the moment so you need to write it manually.
  
https://www.bogotobogo.com/DevOps/Terraform/Terraform_commands_cheat_sheet.php
  $ terraform version
Terraform v0.15.0
on darwin_amd64
+ provider registry.terraform.io/hashicorp/aws v3.37.0    



Files
We'll use the following terraform files:

.
├── main.tf
├── providers.tf
└── variables.tf    

main.tf

terraform {
  required_version = ">= 0.12"
}

# 1. Create vpc
resource "aws_vpc" "prod-vpc" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = "production"
  }
}

# 2. Create Internet Gateway
resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.prod-vpc.id
}

# 3. Create Custom Route Table
resource "aws_route_table" "prod-route-table" {
  vpc_id = aws_vpc.prod-vpc.id
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.gw.id
  }
  route {
    ipv6_cidr_block = "::/0"
    gateway_id      = aws_internet_gateway.gw.id
  }

  tags = {
    Name = "Prod"
  }
}

# 4. Create subnets 
resource "aws_subnet" "subnet-1" {
  vpc_id            = aws_vpc.prod-vpc.id
  cidr_block        = var.subnet_cidr[0]
  availability_zone = "us-east-1a"
  tags = {
    Name = "subnet-1"
  }
}

resource "aws_subnet" "subnet-2" {
  vpc_id            = aws_vpc.prod-vpc.id
  cidr_block        = var.subnet_cidr[1]
  availability_zone = "us-east-1a"
  tags = {
    Name = "subnet-2"
  }
}
 
# 5. Associate subnet with Route Table
resource "aws_route_table_association" "a" {
  subnet_id      = aws_subnet.subnet-1.id
  route_table_id = aws_route_table.prod-route-table.id
}

# 6. Create Security Group to allow port 22,80,443
resource "aws_security_group" "allow_web" {
  name        = "allow_web_traffic"
  description = "Allow Web inbound traffic"
  vpc_id      = aws_vpc.prod-vpc.id
  ingress {
    description = "HTTPS"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    description = "HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"    # any protocol
    cidr_blocks = ["0.0.0.0/0"]
  }
  tags = {
    Name = "allow_web"
  }
}

# 7. Create a network interface with an ip in the subnet that was created in step 4
resource "aws_network_interface" "web-server-nic" {
  subnet_id       = aws_subnet.subnet-1.id
  private_ips     = ["10.0.0.50"]
  security_groups = [aws_security_group.allow_web.id]
}

# 8. Assign an elastic IP to the network interface created in step 7
resource "aws_eip" "one" {
  vpc                       = true
  network_interface         = aws_network_interface.web-server-nic.id
  associate_with_private_ip = "10.0.0.50"
  depends_on                = [aws_internet_gateway.gw]
}

# 9. Create a Ubuntu server and install/enable apache2
resource "aws_instance" "web-server-instance" {
  ami               = "ami-085925f297f89fce1"
  instance_type     = var.ec2_instance_type
  availability_zone = "us-east-1a"
  key_name          = "einsteinish"
  network_interface {
    device_index         = 0
    network_interface_id = aws_network_interface.web-server-nic.id
  }
  user_data = <<-EOF
                #!/bin/bash
                sudo apt update -y
                sudo apt install apache2 -y
                sudo systemctl start apache2
                sudo bash -c 'echo our first web server > /var/www/html/index.html'
                EOF
  tags = {
    Name = var.ec2_instance_name
  }
}

output "my_webserver_public_ip" {
  value = aws_eip.one.public_ip
}

output "my_webserver_private_ip" {
  value = aws_instance.web-server-instance.private_ip
}

output "my_webserver_instance_id" {
  value = aws_instance.web-server-instance.id
}    

providers.tf

provider "aws" {
  region = "us-east-1"
}

variables.tf

variable "subnet_cidr" {
    type = list
    default = ["10.0.0.0/24", "10.0.1.0/24"]
}

variable "ec2_instance_type" {
    type = string
    default = "t2.micro"
}

variable "ec2_instance_name" {
    type = string
    default = "web-server"
}



 





Commands
terraform init
Initializing the backend...
Initializing provider plugins...

terraform apply

terraform state list
$ terraform state list
aws_eip.one
aws_instance.web-server-instance
aws_internet_gateway.gw
aws_network_interface.web-server-nic
aws_route_table.prod-route-table
aws_route_table_association.a
aws_security_group.allow_web
aws_subnet.subnet-1
aws_subnet.subnet-2
aws_vpc.prod-vpc    

terraform state show
$ terraform state show aws_eip.one 
resource "aws_eip" "one" {
    associate_with_private_ip = "10.0.0.50"
    association_id            = "eipassoc-0dd0bb9db1ae47090"
    domain                    = "vpc"
    id                        = "eipalloc-083af3a61d30b4a5c"
    network_border_group      = "us-east-1"
    network_interface         = "eni-0cdfe4e326d6a9cb8"
    private_dns               = "ip-10-0-0-50.ec2.internal"
    private_ip                = "10.0.0.50"
    public_dns                = "ec2-107-21-102-150.compute-1.amazonaws.com"
    public_ip                 = "107.21.102.150"
    public_ipv4_pool          = "amazon"
    vpc                       = true
}

terraform destroy -target
To delete a specific resource.
$ terraform destroy \
-target aws_instance.web-server-instance \
--auto-approve
ws_vpc.prod-vpc: Refreshing state... [id=vpc-049c1374e943a9a98]
aws_security_group.allow_web: Refreshing state... [id=sg-0d382f2fec57effa3]
aws_subnet.subnet-1: Refreshing state... [id=subnet-07c5c01b5cc71833c]
aws_network_interface.web-server-nic: Refreshing state... [id=eni-0cdfe4e326d6a9cb8]
aws_instance.web-server-instance: Refreshing state... [id=i-054ae5efceb26784c]
aws_instance.web-server-instance: Destroying... [id=i-054ae5efceb26784c]
aws_instance.web-server-instance: Still destroying... [id=i-054ae5efceb26784c, 10s elapsed]
aws_instance.web-server-instance: Still destroying... [id=i-054ae5efceb26784c, 20s elapsed]
aws_instance.web-server-instance: Still destroying... [id=i-054ae5efceb26784c, 30s elapsed]
aws_instance.web-server-instance: Destruction complete after 31s
╷
│ Warning: Resource targeting is in effect
│ 
│ You are creating a plan with the -target option, which means that the result of
│ this plan may not represent all of the changes requested by the current
│ configuration.
│ 
│ The -target option is not for routine use, and is provided only for exceptional
│ situations such as recovering from errors or mistakes, or when Terraform
│ specifically suggests to use it as part of an error message.
╵
╷
│ Warning: Applied changes may be incomplete
│ 
│ The plan was created with the -target option in effect, so some changes requested
│ in the configuration may have been ignored and the output values may not be fully
│ updated. Run the following command to verify that no other changes are pending:
│     terraform plan
│ 
│ Note that the -target option is not suitable for routine use, and is provided only
│ for exceptional situations such as recovering from errors or mistakes, or when
│ Terraform specifically suggests to use it as part of an error message.
╵

Destroy complete! Resources: 1 destroyed.

$ terraform destroy -target aws_eip.one
...
Destroy complete! Resources: 1 destroyed.

terraform destroy
To destroy everything
$ terraform destroy
...
Destroy complete! Resources: 8 destroyed.    

terraform apply -target
To create a specific resource

terraform refresh
This terraform refresh is always performed before any plan or apply. It updates the state to match the real.

  
  
