---
layout: post
title: Terraform
---
Developed by HashiCorp in 2014, Terraform is an Infrastructure as Code (IaC) tool written in Go that uses a declarative approach. It allows users to define and provision infrastructure across a vast array of targets, including all major Cloud Service Providers and on-premise environments. This versatility is thanks to its provider-based architecture. All configurations are written and managed using the custom HashiCorp Configuration Language (HCL). 

# Table of Contents
- [Key points](#key-points)
- [Study guides and Certification Exam](#study-guides)
- [Alternatives](#alternatives)
- [Install and setup auto-complete](#install-and-setup-auto-complete)
- [General workflow](#general-workflow)
- [Terraform terms](#terraform-terms)
- [Best Practices](#best-practices)
- [Example](#example)
- [Providers](#providers)
- [Input Variable](#input-variable)
- [Output](#output)
- [Sensitive data](#sensitive-data)
- [Dependency](#dependency)
- [State file](#state-file)
- [Backend](#backend)
- [Module example](#module-example)
- [Meta arguments](#meta-arguments)
- [Built-in functions](#built-in-functions)
- [Logging](#logging)
- [The nested block confusion](#the-nested-block-confusion)
- [Terraform Cloud](#terraform-cloud)
- [More](#more)
- [Github repository](#github-repository)


## Key points
1. History, Ownership & Licensing
   - Origin: Terraform is an Infrastructure as Code (IaC) tool created by HashiCorp in 2014.
   - Acquisition: 
      - HashiCorp was acquired by IBM; the deal officially closed in October 2024. 
      - HashiCorp officially joined IBM in Feb, 2025 after regulatory approvals in US and EU.
   - Licensing Shift: Originally strictly Open Source (Mozilla Public License v2.0), HashiCorp switched Terraform to the Business Source License (BSL) in August 2023.
     - Note: Consequently, it is no longer compliant with the OSI "Open Source" definition, though the source code remains viewable.

    - OpenTofu: Due to the license switch, the community forked the older open-source version to create OpenTofu (managed by the Linux Foundation).

    - Commercial Editions:

        - Terraform Cloud (HCP Terraform): A SaaS version hosted and managed by HashiCorp.

        - Terraform Enterprise: A self-hosted distribution designed for on-premise installation (supports Linux/Replicated).

2. Core Philosophy

    - Declarative: Terraform is declarative. You define the desired state (outcome), and Terraform figures out how to achieve it.

    - Immutable Infrastructure: It favors immutable infrastructure. While it can update resources in place, it prefers to destroy and recreate resources when significant changes occur to ensure consistency.

    - Idempotent: Unless the code is changed, running  ```terraform apply``` will result in the same outcome every time. 

    - Cloud Agnostic: Unlike AWS CloudFormation, Terraform is platform-agnostic. It can manage AWS, Azure, Google Cloud, Kubernetes, and more via Providers.

    - Agentless: It is agentless and masterless. It does not require agents to be installed on the target infrastructure.

    - Language: Written in Go (Golang).

    - Syntax: Uses a Domain Specific Language (DSL) called HCL (HashiCorp Configuration Language) and also supports JSON.

3. Architecture & Internals

    - Split Architecture: Logically divided into Terraform Core (CLI) and Plugins (Providers).
    - Both the core and providers are written in Go.
    - Providers are plugins Terraform uses to to interact with cloud providers, SaaS providers, and other APIs.

    - Core ↔ Provider Communication: Terraform Core communicates with Providers (plugins) via gRPC (Remote Procedure Call).

    - Provider ↔ Target Communication: The Provider talks to the target infrastructure (e.g., AWS) using the target's native APIs (usually REST/HTTP), not RPC.

    - Providers are available on [Terraform Registry](https://registry.terraform.io/).

4. Configuration & Blocks

    - Directory Scope: A configuration is contained in a directory. The CLI processes all *.tf files in that directory.

    - Modules:

        - Every configuration is technically a "root module."

        - Reusable Modules: Packages of Terraform code meant to be called by other configurations.

        - Can be sourced from local paths, Git, private registry or the public Terraform Registry.

    - Key Configuration Blocks:

        - terraform {}: Settings and version constraints.

        - provider {}: Plugin configuration (e.g., AWS region).

        - resource {}: Infrastructure objects (e.g., EC2 instance).

        - data {}: Queries to fetch data about existing external resources.

        - variable {}: Input parameters.

        - locals {}: Local temporary variables/constants.

        - output {}: Return values to the CLI or other modules.

        - module {}: Calls another package of code.

        - provisioner {}: Executes scripts on machines (Note: Considered a last resort/anti-pattern).

5. Workflow & State Management

    - The Minimum Workflow:

        - terraform init: Downloads providers and modules (idempotent).

        - terraform plan: Creates a dry-run execution plan.

        - terraform apply: Executes the changes.

    - The State File (terraform.tfstate):

       - Maps real-world resources to your configuration.

       - Acts as the "source of truth."

       - Often contains sensitive data (must be secured).

    - Backends:

        - Defines where the state file is stored and where operations run.

        - Local Backend: Default; stores state on the local disk.

        - Remote Backends: Store state remotely (S3, GCS, Azure Blob) to enable team collaboration.

    - State Locking: Prevents concurrent runs from corrupting the state file, subject to the backend supporting (e.g., using DynamoDB when using the S3 backend).

6. Ecosystem & Advanced Features

    - Dependency Graph: Terraform builds a graph of all resources to determine the correct order of creation and parallelizes non-dependent resources.

    - Importing: Terraform can take management of existing resources created outside of it using terraform import or import blocks.

    - Meta-Arguments:

        - count: Creates multiple instances based on a number.

        - for_each: Creates multiple instances based on a list or map.

        - lifecycle: Controls creation/deletion behavior (e.g., create_before_destroy).

    - Drift Detection: terraform plan detects differences between your code and the actual infrastructure state (e.g., if someone manually deleted a server).

    - Wrappers: Tools built on top of Terraform include Terragrunt, Spacelift, Env0, and Scalr.  

## Study guides
- [Course by Andrew Brown](#course-by-andrew-brown)
- [Book by Yevgeniy Brikman](#book-by-yevgeniy-brikman)
- [Exam Prep](#exam-prep)
- [Etc](#etc)

### Course by Andrew Brown
I recommend this playlist [HashiCorp Terraform Associate (003)](https://www.youtube.com/watch?v=CoGoo1n1NWE&list=PLBfufR7vyJJ6OmMNbvfkBx1HcL-XI8ymg) on Youtube because:
- It is free of cost.
- Good coverage of concepts and exam topics.
- He flies through the theory but the practicals are pretty good.
- He keeps looking up the official doc. This is how it's done in the real world.
- The choice of tutorials can also be personal. I think I struck a chord with Andrew. I just love his style.
- The same content is available free of cost on [ExamPro](https://www.exampro.co).

### Book by Yevgeniy Brikman
The book _Terraform: Up and Running: Writing Infrastructure as Code_: 
  - This book by the co-founder of Gruntwork, Yevgeniy (Jim) Brikman comes highly recommended. 
  - The book is well written and I recommend coming straight to this if you have no intention of writing the certification exam.  
  - He uses launch configuration in the example. AWS blocks this and forces the use of launch template on new accounts. This could be a hindrance for newbies. He did note we should be using launch template. I hope he updates it in future revision.
  
  - After chapter 2, I paused the book, prepared and went for the exam. After the exam, I returned to the book.

  - As most of the topics felt familiar, I skimmed through the book but consumed enough to really like it. A few points that like:

      - I really like how he introduced and explained workspace and state file isolation in chapter 3.

      - The code example are available on this [report](https://github.com/brikis98/terraform-up-and-running-code) 

      - Excellent explanation of meta-arguments. I love it how he shows how it might be done in a programming language using a pseudo code which looks very close to many languages. If I need a refresher (which I will) on count, for_each etc., I will get back to chapter.

      - Good coverage on Secret Management. I have been storing AWS credentials in plain text file because it's just my practice. After reading this chapter, i immediately adopted aws-vault to store the secrets.

    
            brew install --cask aws-vault
            aws-vault add dev
            aws-vault exec dev -- aws ec2 describe-instances
            aws-vault exec dev — terraform plan

  - This is not just a book on Terraform, it is beyond.
  - He explains AWS along the way
  - He provides a crash course on Docker and Kubernetes
  - There's section on using Kubernetes provider
  - A guide on how to convince your boss and team to adopt IaC
  - A good description of software deployment
  - Lastly but not he least, the reading list at the end of the book is a wealth of resources. I visited his blog and from there I found his latest book [Fundamentals of DevOps and Software Delivery](https://www.fundamentals-of-devops.com/) which is really tempting to read. It's pretty costly in INR but you can get a trial access at [O'reilly](https://www.oreilly.com). Oh, really? 

  - I am a Y Brikman fan.


### Exam Prep
   - Andrew Brown's playlist
   - Bryan’s practice test on Udemy. As the reviews say, this is indeed pretty close to the exam. 
   - I love the flashcards generated by Google NoteBookLM using sources mentioned plus the official exam list. 
   - Practice helps. Andrew's follow alongs are pretty good.
   - Familiarize yourself with HCP Terraform, find the options by inspecting the settings at the Org and Workspace level.

### Etc
  - Spacelift and Env0 provides good articles on Terraform. If you find these sources in your search result, go for them.   

After studying for two weeks, I took the exam and passed. I finished it in 30 minutes, half the time alloted. If you don't know the answer at once, no amount of thinking is going help. 

They are retiring Exam 003 and launching Exam 004 in January, 2026. 

## Alternatives

1. [OpenTofu](https://opentofu.org/): A fork of Terraform
2. [Pulumi](https://www.pulumi.com): Write the configuration in a language of your choice

## Install and setup auto-complete

1. To install terraform, follow the instruction for your OS from [here](https://developer.hashicorp.com/terraform/install)
2. If correctly installed, `terraform version` should return the version of the installed core.
3. To enable the CLI auto-completion using tab, run `terraform -install-autocomplete`. This is a useful tip that most of the tutorials do not include. 
4. If you use VS code, install the [HashiCorp Terraform](https://marketplace.visualstudio.com/items?itemName=HashiCorp.terraform) extension.

## General workflow

1. mkdir new-dir
2. cd new-dir
3. write (main.tf)
4. `terraform init`  
5. `terraform apply`
6. `terraform destroy`

This is the bare minimum. In reality, after writing and `terraform init`, you are likely to run 

1. `terraform validate` for syntax validation
1. `terraform fmt` to format your code to HashiCorp standard
1. `terraform plan` to get a preview of what Terraform will do

Tips
1. `terraform apply -auto-approve` to skip the prompt for confirmation
1. `terraform apply -destroy` is another way to run `terraform destroy`.
1. `terraform -fmt -recursive` to format the code in the sub directories as well. 
1. Install [TFlint](https://github.com/terraform-linters/tflint) linter which goes beyond `terraform validate`.


## Terraform terms
1. The terraform cli is the core
1. Providers are plugins 
1. The root of the working directory is the root module
1. If you have codes in a sub-directory under the working directory, they are modules or child modules
1. Arguments are the values we assigned to parameters. They are an input to the provider (resources) based on which they will be created. Example, to create an AWS instance, we have to assign the AMI ID we want to use to the ami parameter in the resource block.
1. Attributes are the properties of infrastructure objects. Example, the ID of an AWS instance.  
1. Terraform is the software, the product and terraform is the CLI. Simple, I know.
1. The terraform cli, if you use just that, you are using what they call the community edition. 
1. HCP Terraform: Hashicorp Cloud Platform formerly known as Terraform Cloud. 
1. HCL: Hashicorp Configuration Language

## Best Practices
1. Terraform will process any file with .tf extension in the working directory. It is not mandatory to code your configuration in a file called main.tf but this is generally followed as the entry point.
1. You can keep the entire code in a single file. It will work but it will very hard to read and manage. 
1. It is generally broken up into different files. This is the [recommend way](https://developer.hashicorp.com/terraform/language/style#file-names) to spit it.
    - backend.tf for your backend configuration
    - main.tf for all resource and data source blocks
    - outputs.tf for output blocks
    - providers.tf for the providers
    - terraform.tf to define terraform and provider version constraints
    - variables.tf for variable declaration 
    - locals.tf to define the local values
    - override.tf for override definitions 
1. The files should be at the same directory level. Otherwise it becomes a apart of a different module. 
1. Provisioners should be used only as a last resort.
1. Since Terraform and the providers are constantly updated, it is recommended to add version constraint for both. 

## Example
Enough said. Let us get to work and see what it is like to use Terraform to create infrastructure.

We will deploy an EC2 instance. We will need to have an IAM user with the right permissions and the credentials configured on our machine. The simplest was is to follow the setup you would do for aws cli but you don't have to install aws cli. Assuming we got the set up, lets proceed.

1. Create a new directory called example. Inside the directory, create a file called main.tf.
1. Since we want to deploy an EC2 instance, we need the provider aws.
1. Go to Terraform Registry and lookup aws provider. Once you find it, click on the "use provider" which will give a sample code to get started. Paste that into your main.tf.
1. The terraform block is filled but the provider isn't. How does one know what goes into this block. No one knows by heart. We look at the examples, we look at the documentation.
1. From memory, I know that we need to a region argument in the provider block. I will add `region = "us-east-1"`. You can also see this in the documentation section of [aws provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs). 
1. EC2 instance is a resource. So I will need a resource block. The resource we need is called aws_instance, search it within the aws provider in the registry. I will create a resource block and add just the ami and instance_type arguments. 
1. So far, this is our code. The minimum required to deploy an EC2 instance. As a matter of fact, this is the minimum sensible version. It will work with just the resource block. The instance will be created with default everything else, region(us-east-1, default vpc etc.)
    ```
    terraform {
      required_providers {
        aws = {
          source = "hashicorp/aws"
          version = "6.23.0"
        }
      }
    }

    provider "aws" {
      # Configuration options
      region = "us-east-1"
    }

    resource "aws_instance" "example"{
      ami = "ami-0fa3fe0fa7920f68e"
      instance_type = "t2.micro"
    }
    ```    
1. Run `terraform init`. 
    - This initializes the backend (later on this) and downloads the provider that our code references which is aws. 
    - The provider is saved in the .terraform directory. 
    - There will be a new file terraform.lock.hcl which locks the version used during init. 
    - Inspect your current working directory with `ls, ls -la, tree, tree -a`. 
    - For a thorough look into the init process, read [this](https://developer.hashicorp.com/terraform/tutorials/cli/init?utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS).
1. I can actually jump to plan or apply but lets run `terraform validate` to validate and `terraform fmt` to format. The order doesn't matter. Some may prefer to format at a later stage.
1. The `terraform fmt` will align the equal sign in the resource block because they weren't, among other things.
1. `terraform plan` to preview what the code will do if applied.
1. Finally, `terraform apply` to deploy the instance
1. The plan and apply output will show  "+, - and ~". Find out what those mean.
1. After apply, notice the `terraform.tfstate` file in the directory. It's a record of the resource created by the code. It maps the resource name to their identifies in AWS. It also has the attributes that comes with it such as IP addresses etc. More on state file later. 
1. Run `terraform state list` to see the resources created.
1. At this point, even if you apply again, nothing will happen because Terraform is idempotent. 


How can we improve this code?
1. The ami value is hardcoded. We can use an input variable to make our code modular. Define the variable using the variable block and assign the ami argument as `ami = var.var_name` in the resource block. 
1. The other way is to use the data block to query AWS for ami's and filter it to our need using the filter block.  
1. We do not mention which VPC and security group will be applied. The default ones are used if we don't mention it. We can fetch existing VPCs and SGs with the data block.
1. We won't be able to SSH to the instance without key pairs. The aws_instance resource has an argument called key_name. You can assign it to the name of the key pair you have in AWS: `key_name = "name of key pair"`. If you prefer to use your existing keys on your machine, create a key pair with the aws_key_pair resource and assign the name to the key_name argument.
1. To configure the instance on first boot, may be run some commands, install some package, the aws_instance resource has an argument called user_data. It becomes the input to cloud init. It can take a file as an input or you can provide the commands inline with the here document. 

Finally, remember to destroy by running `terraform destroy` and I have a habit of running `terraform state list` right after destroy for a peace of mind.

## Providers
1. Terraform relies on plugins called "providers" to interact with cloud providers, SaaS providers, and other APIs.
1. Each provider adds a set of resource types and data sources that Terraform can manage.
2. Providers are distributed separately from Terraform itself, and each provider has its own release cadence and version numbers.
2. They are downloaded by terraform init on need basis. 
3. Even if they are not added in the code, init will download a provider if the code references a resource that belongs to it.
4. We can define the same provider multiple times in a configuration.
5. But we need "alias" within the provider block to uniquely refer to them. Example: We can have AWS providers for different regions in the same configuration.
7. To use that provider, inside a resource block, use provider argument. Example: `provider = aws.alias_name`
8. Alias for provider can also be defined in the terraform block. 
9. Providers are frequently updated. To lock the version of the provider, use provider version constraint in terraform block.
10. In the absence of version constraint in the code, init will download the latest version.
13. `terraform providers` give you a list of providers used by the configuration
14. The provider downloaded is saved in .terraform directory. The terraform.lock.hcl file is created with the exact provider version used. 


## Input Variable
This is how we avoid hard coding argument assignment in Terraform. Input variable let us parameterize terraform configuration. 

- [Data types](#data-types)
- [variable block](#variable-block)
- [variable reference](#variable-reference)
- [Interpolation](#interpolation)
- [Assigning value](#assigning-value)
- [Precedence](#precedence)
- [Locals](#locals)

### Data types
Terraform provides the following data types:
- Primitive types:
   - string
   - number
   - bool
- Complex types
  - Collection types
    - list
    - set
    - map  
  - Structural types
    - tuple
    - object

Special mention to tuple and set: 
- Tuple is a list that accepts dissimilar values. Set is collection of values but unlike map or list, we cannot address a single element. There is no ordering or index. For example, we cannot use example[1] for a set.
- Tuple and object requires a schema. You have to define what type of primitive and in what order it can accept.  

Refer the document: [Types and Values](https://developer.hashicorp.com/terraform/language/expressions/types)

### Variable block
This is how you define a variable. It can be main.tf but it is recommended to define variables in it's own file named variables.tf. Example:

    
    variable "example"{
      description = "It is best practice to have a description."  
      type = string
    }
   
To refer variable this variable, we use `var.example`. 

### Variable reference
Variables are referenced in the form var.variable_name.

    something = var.something_else

### Interpolation
To mix variable reference with strings, we use "${...}". For example:

    tags = {
        Name = "This is ${var.example}"
    }

### Assigning value
1. Using default argument in the variable block: `default = "Something"`
1. terraform.tfvars or terraform.tfvars.json (auto loaded)
1. something.tfvars (not auto loaded)
1. something.auto.tfvars file (auto loaded)
1. TF_VAR_some environment variable will applying 
1. -var="var.something=value" during apply
1. -var-file=something.tfvars during apply
1. If no value is provided, you will be prompted to enter it during apply.

### Precedence
If we assign value to variables by more than one way, which one will be effective? This is the order of precedence from lowest to highest.

1. default (lowest)
2. env (TF_VAR_something)
3. terraform.tfvars
4. terraform.tfvars.json
5. *.auto.tfvars or *.auto.tfvars.json (lexical order  of their file names)
6. -var and -var-file (whichever comes last) (highest)

### Locals
Locals are like variables but meant to be used only within a module. Recall what is a module? 

Say you are going to assign the port 80 multiple times, you can use locals to avoid repetition. It's not hard to type 80, may be even easier but if you have to change it, it wouldn't be fun to change it in 10 places. 

To declare a local, note "locals":

    locals {
        port = "80"
    }

To reference a local, note "local":

    something = local.port

## Output
The output block is a way to expose data. For instance, when we deploy the EC2 instance, we can output the public IP for us to connect to it rather than looking up from the AWS console or the state file.

To extend our example code to deploy EC2 instance, to output the public IP:

    output "public_ip"{
        description = "Public IP of the instance. Having a description is best practice."
        value = aws_instance.example.public_ip
    }

We can see the output when we run apply. We can also use:
1. `terraform output`
1. `terraform output public_ip`
1. `terraform output -json`
1. `terraform output -raw public_ip`

The raw output is useful when you want to insert it in a bash command or script. Example: `ssh ec2-user@$(terraform output -raw public_ip)`

Output is also a way to pass data from a child module to the calling module. Otherwise, variables are available only within a module. We will cover this in the module section.    

## Sensitive data
To protect sensitive data, we can use the sensitive argument in variable and output block. Add `sensitive = true`. This will redact the value in plan and apply but it will still be stored as plain text in the state file.

If we output a variable marked sensitive, it must also be made sensitive in the output block.

There is also a built-in function called sensitive.

## Dependency
By default, Terraform creates as much as 10 resources in parallel. This can be controlled by the flag `-parallelism=n`. Sometimes, a resource will require the existence of a another resource before it can be created. Terraform analyses the code and build a dependency graph. It will create the resources in the order required. 

### Implicit dependency 
When a resource refers the attribute of a another resource, it creates an implicit dependency. If you create a security group and an instance resource block in the same configuration uses it, Terraform knows that it should create the security group before the instance.   

### Explicit dependency
In the absence of an implicit dependency but when a resource have a dependency on something else, an explicit dependency can be created using the `depends_on` block inside the resource block that is dependent on something else.  

To view the dependency, run `terraform graph`. 

## State file
1. If Terraform is an empire, the state file would be the first target by the enemies. It is the heart of your Terraform instance.
1. It is the record of yor infrastructure managed by Terraform.
1. It is how Terraform knows what exists and avoid recreating them on subsequent applies.
1. It is refreshed by every run of plan and apply.
1. `terraform apply -refresh-only` when you don't to refresh the state without creating resources. 
1. Resources created outside of Terraform can be brought under Terraform management by a combination of adding a block for it and running `terraform import ...` command. 
1. It is always in plain text which means even variables marked as sensitive will not be masked in the state file. This is why Enterprises will use a backend that can safely store the state file by using encryption or other means. 

## Backend
Backend is where the state file resides. Apart from storing the state file, some backend provides a run time. 

When we use the community edition, we use the terraform core installed in our local machine. The state file is also stored locally. This is local backend. The default backend when you don't specify the backend in your code.

Another function of the backend is state locking which is a feature that prevents concurrent execution of the same configuration. When you collaborate with a team, you must use a backend that supports state locking.

There are several supported backend types, some support one or all of:  storing state file, run time, locking. Refer to this [list](https://developer.hashicorp.com/terraform/language/backend).

To change the backend, we use the backend block nested in the terraform block. To use HCP Terraform (TF Cloud), though the backend block supports it, there is a dedicated block called cloud.

When you have a single workspace, the state file is saved terraform.tfstate. If there are multiple workspaces, in case of local backend, it will be stored as:

```
  terraform.tfstate.d/
    ├── development/
    │   └── terraform.tfstate
    └── production/
        └── terraform.tfstate
```
Other backend may do it differently to isolate the state file.  

Take good care of your back(end).

## Module example
A module is (usually) someone else's Terraform code that you call from yours. It lets you reuse your code or others' code. You can find them on Terraform registry. AWS VPC module is a good example to check. 

Lets convert our example of EC2 creation into a module.

1. Go to that directory
2. mkdir terraform-aws-ec2-example 
3. mv main.tf terraform-aws-ec2-example
4. vi main.tf

 Hence, forth terraform-aws-ec2-example is our child module. The directory looks like:

    $ tree -rl example 
        example
        ├── terraform-aws-ec2-example
        │   └── main.tf
        └── main.tf

Since variablize is not a widely accepted word, lets say we will convert the ami ID in the child module to a variable.

Create a variable block:

    variable "ami_id" {
      description = "To input the AMI ID"
      type        = string
    }

In the source block, the ami ID is currently hardcoded. Change it to:

      ami           = var.ami_id

This is how our child module looks like:

    terraform {
      required_providers {
        aws = {
          source  = "hashicorp/aws"
          version = "6.23.0"
        }
      }
    }

    provider "aws" {
    # Configuration options
    region = "us-east-1"
    }

    variable "ami_id" {
    description = "To input the AMI ID"
    type        = string
    }

    resource "aws_instance" "example" {
    ami           = var.ami_id
    instance_type = "t2.micro"
    }

    output "public_ip" {
    description = "Public IP of the instance. Having a description is best practice."
    value       = aws_instance.example.public_ip
    }

Next, from the main.tf file in our root module, we call our child module using the module block and inside the block, we assign a value to the ami_id argument, which is an input variable declared in the child module. 

main.tf in the root module:

    module ec2_example {
    source = "./terraform-aws-ec2-example"
    ami_id = "ami-0fa3fe0fa7920f68e"
    }

ex2_example is the local name of terraform-aws-ec2-example module in our root module, the module we call, the one pointed by the source argument.

At this point, we can run init, plan and apply. How what about the output in the child module? Merely calling the module with it's required argument will not output any value. We have to call it from calling module. Adding this to our root module code:

    output "public_ip" {
    description = "This is the public IP of the instance"
    value = module.ec2_example.public_ip
    }

If we apply it now, the instance will be created and we will have the public IP as an output.

To publish a module to the registry, follow [this](https://developer.hashicorp.com/terraform/registry/modules/publish). 

There are requirements to public a module. One of them is the naming format. That is why we name our child module directory as terraform-aws-ec2-example following the convention: `terraform-<PROVIDER>-<NAME>`.

This is a module ([apache-example2](https://registry.terraform.io/modules/rualthan/apache-example2/aws/latest)) which I published on the registry. Please use it

## Meta arguments
Meta-arguments are special class of arguments that are available across different resource types. We have mentioned `depends_on` for explicit dependency and `provider` arguments to refer to a specific provider using the alias. These are meta-arguments.

Others are count, for_each, lifecycle etc. Look them up on the [documentation](https://developer.hashicorp.com/terraform/language/meta-arguments) with special attention to lifecycle.

## Built-in functions
Terraform provides a lot of functions to transform and combine values.

For a list of built-in functions, go [here](https://developer.hashicorp.com/terraform/language/functions) and look at the left pane. 

There are functions for string, numeric, filesystem and more. 

A good way to test the functions is using the terraform console.

1. Run `terraform console` to enter it
1. Example:
```
    > upper("function")
    "FUNCTION"
    > sensitive("don't tell anyone")
    (sensitive value)
    > basename(path.cwd)
    "example"
```

## Logging
- Terraform logging can be enabled using the TF_LOG env. 
- To set: `export TF_LOG="log level"`
- The levels in decreasing order of verbosity are: TRACE, DEBUG, INFO, WARN or ERROR.
- TF_LOG combines the core and provider logs. To enable logging granularly, use TF_LOG_CORE and _TF_LOG_PROVIDER 
- To save logs to a file, use TF_LOG_PATH
- The document on logging is available [here](https://developer.hashicorp.com/terraform/internals/debugging)

## The nested block confusion
There are two kinds of nested blocks that confused me. One is of the form name = {...} and another is name{...}. Note the presence/absence of equal sign.

    something = {
    ...
    }

    something {
        ...
    }

The difference lies in if it is a nested block that is allowed inside the outer block or if it is an argument that accepts a complex type like a map.

Inside aws_instance block, it is common to have:

    tags = {
        Name = "HelloWorld"
    }

This "tags" is an argument that takes map as value. Whereas, the egress block inside aws_security_group is a nested block allowed for this resource type. 

    egress {
        from_port        = 0
        to_port          = 0
        protocol         = "-1"
        cidr_blocks      = ["0.0.0.0/0"]
        ipv6_cidr_blocks = ["::/0"]
    }

My confusion must have come from the fact that both of them contain a map. 

Actually, what I refer to as the nested block has a name. It is called inline block. 

## Terraform Cloud 
HCP Terraform is a SaaS offering from HashiCorp that provides a complete infrastructure lifecycle management. It is a backend that can store the state file securely, provides state locking and run time. It can also store secrets and variable assignment value securely.  

You can try it out as it is free for up to 5 users. You can follow [this](https://developer.hashicorp.com/terraform/tutorials/cloud-get-started) but the overview would be:
1. Sign up
1. Create an org
1. Create a workspace (choose CLI)
1. Add your AWS credentials in the workspace
1. Add variable (if any) in the workspace
1. Modify your existing code and switch to HCP using cloud block.
1. Run init again to migrate the state.
1. Hereafter, plan and apply will run on HCP and the output will be streamed to your local machine.

If you choose VCS workspace, you will combine it with a repository from the likes of GitHub and it can function like a CI/CD workflow. A merge on the repository will trigger a terraform apply on the workspace.

## More
### Provisioners
Provisioners let you perform operations after terraform apply. Terraform provides the following provisioners:

- file: Copies files from the host where Terraform is running to the new resource.
- local-exec: Runs command on the local machine after Terraform creates the resource.
- remote-exec: Runs on the remote resource after Terraform creates the resource.
- Cloud-init(user_data): Pass data or run commands on first boot in the compute instance created by Terraform 

Provisioner should be added inside the resource block.
Terraform cannot manage the outcome of the tasks done by provisioners. Therefore, it is recommended to avoid it as much as possible. When possible, the operations done by provisioners should be built into the image itself.

More on provisioners [here](https://developer.hashicorp.com/terraform/language/provisioners#perform-post-apply-operations).

### null_resource and terraform_data
- Let us say we need to perform post apply operation. 
- We cannot use provisioner because the task has no relation to the resources and provisioner cannot be called outside of a resource block. 
- The solution to this is to use a null_resource and place the provisioner inside it.

A null resource is a fake resource in the sense that it is like a resource but it doesn't create any object anywhere. A resource belongs to a provider and guess which provider provides a null resource? [Null provider](https://registry.terraform.io/providers/hashicorp/null/latest/docs/resources/resource). Here is a Spacelift [article](https://spacelift.io/blog/terraform-null-resource) on null_resource.

Terraform 1.4 adds [terraform_data](https://registry.terraform.io/providers/hashicorp/null/latest/docs/guides/terraform-migration) to replace null_resource. 

## Github repository
These ([repo](https://github.com/rualthan/Andrew-Brown-Terraform-Associate-Labs)) are the codes from the follow alongs of Andrew Brown's HashiCorp Terraform Associate (003) course.

The full course is available on Youtube and ExamPro for free.

Andrew shares the codes on this [repository](https://github.com/ExamProCo/Terraform-Associate-Labs). You can clone it but I wanted to write from scratch as I followed along.
