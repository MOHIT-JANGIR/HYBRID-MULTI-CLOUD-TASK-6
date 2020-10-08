![image](https://user-images.githubusercontent.com/61896468/95457032-e4499b80-098d-11eb-8951-e24628e93d32.png)

# Deploying WordPress application on Kubernetes with AWS RDS using terraform

# <p align="center">Task Description </p>

# I have written Infrastructure as code using terraform, which automatically deploy the Wordpress application. On AWS, I have used RDS service for the relational database for Wordpress application. Then I deployed the Wordpress as a container on top of Minikube, the Wordpress application is accessible to the public world.



# `Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation`



## A Kubernetes cluster is a set of node machines for running containerized applications. If you’re running Kubernetes, you’re running a cluster. At a minimum, a cluster contains a control plane and one or more compute machines, or nodes. The control plane is responsible for maintaining the desired state of the cluster, such as which applications are running and which container images they use. Nodes actually run the applications and workloads.


# *AWS RDS Amazon Relational Database Service (Amazon RDS) is a web service that makes it easier to set up, operate, and scale a relational database in the AWS Cloud. It provides cost-efficient, resizable capacity for an industry-standard relational database and manages common database administration tasks.*

## *Minikube is an open-source tool that helps to run Kubernetes on a local computer. Before using minikube we need to start it so here I wrote terraform code to start minikube on my Local Computer.*
# Practical part-

`Deployed the WordPress Application on Kubernetes and MySQL Database on AWS Cloud using Terraform Code
`
# wp.tf File-
## It will launch the Wordpress Application.

```
resource "null_resource" "minikubestart" {
 provisioner "local-exec" {
      command = "minikube start"
   }
}
provider "kubernetes" {
 config_context_cluster = "minikube"
}
resource "kubernetes_deployment" "wordpress" {
 metadata {
  name = "wp"
 }
spec {
 replicas = 3
 selector {
  match_labels = {
   env = "production"
   region = "IN"
   App = "wordpress"
  }
  match_expressions {
   key = "env"
   operator = "In"
   values = ["production" , "webserver"]
  }
 }
 template {
  metadata {
   labels = {
    env = "production"
    region = "IN"
    App = "wordpress"
   }
  }
  spec {
   container {
    image = "wordpress:4.8-apache"
    name = "wp" 
    }
   }
  }
 }
}
resource "kubernetes_service" "wordpresslb" {
 metadata {
  name = "wplb"
 }
 spec {
  selector = {
   app = "wordpress"
  }
  port {
   protocol = "TCP"
   port = 80
   target_port = 80
  }
  type = "NodePort"
 }
}
```

# dbsql.tf File
## It will create the RDS on top of AWS.
```
provider "aws" {
  region = "ap-south-1"
  profile = "myshailja"
}
resource "aws_db_instance" "mydb" {
  allocated_storage    = 20
  identifier           = "dbinstance"
  storage_type         = "gp2"
  engine               = "mysql"
  engine_version       = "5.7.30"
  instance_class       = "db.t2.micro"
  name                 = "mydb"
  username             = "shailja"
  password             = "root12345678"
  iam_database_authentication_enabled = true
  parameter_group_name = "default.mysql5.7"
  skip_final_snapshot  = true
  publicly_accessible = true
  tags = {
    Name = "sqldb"
  }
}
```

# main.tf File-
## It will provide the IP to connect to Wordpress.

```
resource "null_resource" "minikubeservice" {
  provisioner "local-exec" {
    command = "minikube service list"
    
  }
  depends_on = [
      kubernetes_deployment.wordpress,
      kubernetes_service.wordpresslb,
      aws_db_instance.mydb
      ]
}
```

# When we run this RDS will be created on top of AWS.
![image](https://user-images.githubusercontent.com/61896468/95456437-eeb76580-098c-11eb-856d-28d5efb3e980.png) 
# *To deploy complete Infrastructure-*
## In Order to Build the Complete Infrastructure, At first we have to initialize Terraform -

# Step1: # terraform init

![image](https://user-images.githubusercontent.com/61896468/95456522-17d7f600-098d-11eb-8bfe-18076eef292c.png) 

# To check if any error if present or not-

# Step2: #terraform validate

![image](https://user-images.githubusercontent.com/61896468/95456637-42c24a00-098d-11eb-882a-10ed13034201.png)

 
## It’s always good to check what we are planning to build , So before running the Actual Code . First we should check our plan


# Step3: #terraform plan

![image](https://user-images.githubusercontent.com/61896468/95456704-58d00a80-098d-11eb-8321-97b46e1a2825.png) 

## So , After knowing the Plan . Here , We can set-up the Complete Infrastructure in just a single-click .


# Step4: #terraform apply — auto-approve
![image](https://user-images.githubusercontent.com/61896468/95456756-70a78e80-098d-11eb-8825-8963207366ad.png) 


*To check the IP*

# Step5: #minikube service list

![image](https://user-images.githubusercontent.com/61896468/95456805-85842200-098d-11eb-82d5-b239162913c4.png) 

# `To check if the deployment is running fine or not-`

# Step6: #kubectl get all

![image](https://user-images.githubusercontent.com/61896468/95456849-9b91e280-098d-11eb-8998-868f9c9cbcc5.png) 

# `Using the IP address launch the WordPress Application and enter the details of RDS .`

![image](https://user-images.githubusercontent.com/61896468/95456296-bd3e9a00-098c-11eb-8f3b-a3131a488074.png)

![image](https://user-images.githubusercontent.com/61896468/95456316-c465a800-098c-11eb-8569-68d1eb264d6a.png)

![image](https://user-images.githubusercontent.com/61896468/95456339-cdef1000-098c-11eb-9af4-84fae6de5fc9.png)


# <p align="center"> so finally i completed Hybrid cloud task 6,thanks a lot vimal sir and all the 24*7 working technical volunteers for all your seamless and thankful support  </p>


