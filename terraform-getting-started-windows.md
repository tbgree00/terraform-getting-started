# Getting Started with Terraform on Windows 10

[![Installing Terraform on Windows 10](http://img.youtube.com/vi/X7XrHvW1w3c/0.jpg)](http://www.youtube.com/watch?v=X7XrHvW1w3c)

Terraform is a tool for defining and provisioning infrastructure as code (IaC). You can use Terraform to deploy a consistent infrastructure platform with predictable results every time. In this guide, you will install Terraform on your computer, and then you will create and destroy a Docker container.

## Prerequisites

- Computer with Windows 10 updated version 2004
- [Windows Subsystem for Linux version 2 (WSL2)](https://aka.ms/wsl2kernel)
- [Docker Desktop](https://docs.docker.com/engine/install/)

## Install Terraform

To install Terraform, visit [Terraform.io](https://www.terraform.io/downloads.html) and download the package compatible with your computer. During the extraction process you will create a new folder, for example `C:\HashiCorp\Terraform`. Extract the `terraform.exe` file in this folder.

## Create PATH Variable on Windows

Adding Terraform to the PATH environmental variable will allow you to run `terraform` from the command prompt.
- Search the Windows Start menu for the **Advanced System Settings**
- On the Advanced tab, select **Environment Variables...**
- Click on **Path** and then **Edit**
- Click **New** and enter the path to the `terraform.exe` file
- Close and reopen any open command prompt windows

Finally, test Terraform from the command prompt.
```shell
terraform -help
```
If you see the following output, the installation is complete.
```hcl
Usage: terraform [-version] [-help] <command> [args]

The available commands for execution are listed below.
The most common, useful commands are shown first, followed by
less common or more advanced commands. If you're just getting
started with Terraform, stick with the common commands. For the
other commands, please read the help and docs before usage.
```
## Troubleshoot
If Windows returns `'terraform' is not recognized as an internal or external command,
operable program or batch file.` you may need to close and reopen the command prompt.

Now that you have installed Terraform, you can create a folder and deploy a test environment.

## Create a Working Folder

A working folder is a folder on your computer which holds Terraform files for a project. Use the command prompt to create the working folder.

```shell
mkdir terraform-demo
cd terraform-demo
```

Next, create a file for your Terraform configuration code.
## Create Terraform Main File

```hcf
start notepad main.tf
```

Paste the following lines into the file and save.

```hcl
provider "docker" {
    host = "tcp://localhost:2375"
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 80
  }
}

resource "docker_image" "nginx" {
  name = "nginx:latest"
}
```

## Initialize Terraform Environment
Initialize Terraform with the `init` command. This will install the Docker provider and allow you to work with Terraform commands.

```shell
$ terraform init
```
The output should display as follows.

```hcl
Initializing the backend...

Initializing provider plugins...
- Checking for available provider plugins...
- Downloading plugin for provider "docker" (terraform-providers/docker) 2.7.1...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.docker: version = "~> 2.7"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```


If the command completed without any errors, provision the resource using the `apply` command.

## Apply Terraform Environment
```shell
$ terraform apply
```
The output will detail the environment configuration. At the bottom of the output it asks for confirmation. Type `yes` and hit ENTER. Terraform will create the environment.

The expected output is as follows:
```hcl
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach           = false
      + bridge           = (known after apply)
      + command          = (known after apply)
      + container_logs   = (known after apply)
      + entrypoint       = (known after apply)
      + env              = (known after apply)
      + exit_code        = (known after apply)
      + gateway          = (known after apply)
      + hostname         = (known after apply)
      + id               = (known after apply)
      + image            = (known after apply)
      + ip_address       = (known after apply)
      + ip_prefix_length = (known after apply)
      + ipc_mode         = (known after apply)
      + log_driver       = (known after apply)
      + log_opts         = (known after apply)
      + logs             = false
      + must_run         = true
      + name             = "training"
      + network_data     = (known after apply)
      + read_only        = false
      + restart          = "no"
      + rm               = false
      + shm_size         = (known after apply)
      + start            = true
      + user             = (known after apply)

      + labels {
          + label = (known after apply)
          + value = (known after apply)
        }

      + ports {
          + external = 80
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id     = (known after apply)
      + latest = (known after apply)
      + name   = "nginx:latest"
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 7s [id=sha256:2622e6cca7ebbb6e310743abce3fc47335393e79171b9d76ba9d4f446ce7b163nginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 4s [id=b4c56384b514adc77db44b0e52e1790573be76cf44df56c2efa22ea1e4db7d1c]
```


Once the process is complete it will display a message indicating that it created the resource. You can validate the container is available by opening a browser to [http:\\localhost:80](http:\\localhost:80)

## Destroy Terraform Environment
Once you have confirmed the deployment succeeded, destroy the infrastructure.

```shell
$ terraform destroy
```

Look for a message are the bottom of the output asking for confirmation. Type `yes` and hit ENTER. Terraform will destroy the resources it had created earlier.

The output is as follows:
```hcl
docker_image.nginx: Refreshing state... [id=sha256:2622e6cca7ebbb6e310743abce3fc47335393e79171b9d76ba9d4f446ce7b163nginx:latest]
docker_container.nginx: Refreshing state... [id=b4c56384b514adc77db44b0e52e1790573be76cf44df56c2efa22ea1e4db7d1c]

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
      - attach            = false -> null
      - command           = [
          - "nginx",
          - "-g",
          - "daemon off;",
        ] -> null
      - cpu_shares        = 0 -> null
      - dns               = [] -> null
      - dns_opts          = [] -> null
      - dns_search        = [] -> null
      - entrypoint        = [
          - "/docker-entrypoint.sh",
        ] -> null
      - env               = [] -> null
      - gateway           = "172.17.0.1" -> null
      - group_add         = [] -> null
      - hostname          = "b4c56384b514" -> null
      - id                = "b4c56384b514adc77db44b0e52e1790573be76cf44df56c2efa22ea1e4db7d1c" -> null
      - image             = "sha256:2622e6cca7ebbb6e310743abce3fc47335393e79171b9d76ba9d4f446ce7b163" -> null
      - ip_address        = "172.17.0.2" -> null
      - ip_prefix_length  = 16 -> null
      - ipc_mode          = "private" -> null
      - links             = [] -> null
      - log_driver        = "json-file" -> null
      - log_opts          = {} -> null
      - logs              = false -> null
      - max_retry_count   = 0 -> null
      - memory            = 0 -> null
      - memory_swap       = 0 -> null
      - must_run          = true -> null
      - network_data      = [
          - {
              - gateway          = "172.17.0.1"
              - ip_address       = "172.17.0.2"
              - ip_prefix_length = 16
              - network_name     = "bridge"
            },
        ] -> null
      - network_mode      = "default" -> null
      - privileged        = false -> null
      - publish_all_ports = false -> null
      - read_only         = false -> null
      - restart           = "no" -> null
      - rm                = false -> null
      - shm_size          = 64 -> null
      - start             = true -> null
      - sysctls           = {} -> null
      - tmpfs             = {} -> null

      - ports {
          - external = 80 -> null
          - internal = 80 -> null
          - ip       = "0.0.0.0" -> null
          - protocol = "tcp" -> null
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      - id     = "sha256:2622e6cca7ebbb6e310743abce3fc47335393e79171b9d76ba9d4f446ce7b163nginx:latest" -> null
      - latest = "sha256:2622e6cca7ebbb6e310743abce3fc47335393e79171b9d76ba9d4f446ce7b163" -> null
      - name   = "nginx:latest" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

docker_container.nginx: Destroying... [id=b4c56384b514adc77db44b0e52e1790573be76cf44df56c2efa22ea1e4db7d1c]
docker_container.nginx: Destruction complete after 0s
docker_image.nginx: Destroying... [id=sha256:2622e6cca7ebbb6e310743abce3fc47335393e79171b9d76ba9d4f446ce7b163nginx:latest]
docker_image.nginx: Destruction complete after 1s

Destroy complete! Resources: 2 destroyed.
```

## Next Steps

You have installed Terraform, deployed and destroyed your first environment using Infrastructure as Code. This code is reusable and will consistently create the same environment. Next you will use Terraform to create a new environment in the cloud.
