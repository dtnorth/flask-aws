## 🚀 Flask Application Deployment on AWS with Terraform, Docker, and GitHub Actions  

This project automates the deployment of a **Flask application** to **AWS ECS (Fargate)** using **Terraform** for infrastructure provisioning, **Docker** for containerization, and **GitHub Actions** for CI/CD.  

---

## **Table of Contents**  
- [Infrastructure Deployment (Terraform)](#infrastructure-deployment-terraform)  
- [Containerization (Docker)](#containerization-docker)  
- [CI/CD Pipeline (GitHub Actions)](#cicd-pipeline-github-actions)  
- [Setup Instructions](#setup-instructions)  
- [Future Enhancements](#future-enhancements)  

---

## **Infrastructure Deployment (Terraform)** 🏗️  
The infrastructure is provisioned using **Terraform**, which includes:  
- **S3 Backend for State Storage** – Ensures infrastructure state is securely stored with version control.  
- **VPC Configuration** – Creates public and private subnets with a NAT gateway for secure networking.  
- **ECS Cluster & Task Definition** – Defines the application deployment environment using AWS Fargate.  
- **CloudWatch Logs** – Enables logging and monitoring of the application.  
- **Security Groups** – Controls access to the application by defining network rules.  

This setup allows for a scalable, resilient, and secure deployment of the Flask application.  

---

## **Containerization (Docker)** 🐳  
The Flask application is containerized using **Docker**, following a multi-stage build process for efficiency.  

### **Key Features of the Dockerfile:**  
- **Uses a Slim Python Base Image** – Reduces the final image size.  
- **Multi-Stage Build** – Ensures dependencies are installed efficiently.  
- **Runs as a Non-Root User** – Improves security.  
- **Exposes Port 5000** – Configured for Flask and Gunicorn.  
- **Gunicorn for Production** – Enhances performance and reliability.  

### **Local Development and Testing:**  
1. **Build the Docker Image**  
   ```sh
   docker build --no-cache -t flask-app:1.0.0 .
   ```  
2. **Run the Container Locally**  
   ```sh
   docker run -d -p 5000:5000 --name flask-container flask-app:1.0.0
   ```  
3. **Access the Application on `localhost:5000`**  

This containerized approach ensures portability and consistency across environments.  

---

## **CI/CD Pipeline (GitHub Actions)** ⚙️  
The **GitHub Actions** workflow automates the deployment process, ensuring seamless integration and delivery.  

### **Pipeline Workflow:**  
1. **Triggers on Code Push** – Any branch push initiates the pipeline.  
2. **Builds the Docker Image** – Ensures a fresh and updated image is created.  
3. **Scans for Security Vulnerabilities** – Uses **Trivy** to identify potential threats.  
4. **Pushes the Image to AWS ECR** – Ensures centralized and secure image storage.  
5. **Deploys to AWS ECS** – Updates the running application on AWS Fargate.  

This automated workflow ensures **secure, fast, and reliable** application deployments.  

---

## **Setup Instructions** 🔧  
### **1. Clone the Repository**  
Download the project to your local system.  
```sh
git clone https://github.com/your-repo/flask-terraform-ecs.git
cd flask-terraform-ecs
```  

### **2. Initialize and Apply Terraform**  
Configure AWS resources by running Terraform commands.  
```sh
terraform init
terraform validate
terraform plan -out plan.out
terraform apply
```  

### **3. Push Code to GitHub**  
Trigger the CI/CD pipeline by pushing changes to GitHub.  

### **4. Monitor Deployment**  
- **GitHub Actions:** Check logs to monitor the pipeline execution.  
- **AWS ECS Console:** Verify that the application is deployed and running.  

---

## **Future Enhancements** 🔥  
- **Automate Terraform Deployment** – Integrate Terraform within GitHub Actions.  
- **Implement Blue-Green Deployment** – Reduce downtime during application updates.  
- **Add Performance Monitoring** – Leverage AWS CloudWatch for real-time observability.  

---

## Directory Structure

![image](https://github.com/user-attachments/assets/c0e50184-5270-4c5d-9846-13d09bda1ae4)

📂 Repository Structure

Here are the files and directories present in the repository:

🔹 Root Directory
  - .github/workflows/docker-image.yml – GitHub Actions workflow for Docker image automation.
  - Dockerfile – Instructions for building a Docker container.
  - LICENSE – Repository license.
  - README.md – Documentation for the project.
  - self-hosted-runner.sh – Script to set up a self-hosted GitHub Actions runner.
🔹 Application Code (app/)
  - app.py – Main application logic.
  - requirements.txt – Dependencies for the application.
🔹 Terraform Infrastructure (terraform/)
  - .infracost/pricing.gob – Cached pricing file for Infracost.
  - .infracost/terraform_modules/manifest.json – Terraform module manifest.
  - .terraform.lock.hcl – Lock file for Terraform dependencies.
  - backend.tf – Defines Terraform backend storage.
  - cloudwatch.tf – CloudWatch logging and monitoring setup.
  - ecs.tf – ECS cluster definition.
  - iam.tf – IAM role and policy configurations.
  - outputs.tf – Terraform output definitions.
  - plan.out – Terraform plan output file.
  - providers.tf – Configures Terraform providers.
  - security_groups.tf – Security group rules.
  - variables.tf – Input variables for Terraform.
  - vpc.tf – VPC configuration.
```
---
## terraform plan

Terraform will perform the following actions:

  # aws_appautoscaling_policy.scale_up will be created
  + resource "aws_appautoscaling_policy" "scale_up" {
      + alarm_arns         = (known after apply)
      + arn                = (known after apply)
      + id                 = (known after apply)
      + name               = "ecs-scale-up"
      + policy_type        = "TargetTrackingScaling"
      + resource_id        = "service/flask-app-cluster/flask-app-service"
      + scalable_dimension = "ecs:service:DesiredCount"
      + service_namespace  = "ecs"

      + target_tracking_scaling_policy_configuration {
          + disable_scale_in = false
          + target_value     = 75

          + predefined_metric_specification {
              + predefined_metric_type = "ECSServiceAverageCPUUtilization"
            }
        }
    }

  # aws_appautoscaling_target.ecs_target will be created
  + resource "aws_appautoscaling_target" "ecs_target" {
      + arn                = (known after apply)
      + id                 = (known after apply)
      + max_capacity       = 4
      + min_capacity       = 1
      + resource_id        = "service/flask-app-cluster/flask-app-service"
      + role_arn           = (known after apply)
      + scalable_dimension = "ecs:service:DesiredCount"
      + service_namespace  = "ecs"
      + tags_all           = (known after apply)

      + suspended_state (known after apply)
    }

  # aws_cloudwatch_log_group.vpc_flow_logs will be created
  + resource "aws_cloudwatch_log_group" "vpc_flow_logs" {
      + arn               = (known after apply)
      + id                = (known after apply)
      + log_group_class   = (known after apply)
      + name              = "/aws/vpc-flow-logs"
      + name_prefix       = (known after apply)
      + retention_in_days = 30
      + skip_destroy      = false
      + tags_all          = (known after apply)
    }

  # aws_ecs_cluster.app will be created
  + resource "aws_ecs_cluster" "app" {
      + arn      = (known after apply)
      + id       = (known after apply)
      + name     = "flask-app-cluster"
      + tags_all = (known after apply)

      + setting (known after apply)
    }

  # aws_ecs_service.app will be created
  + resource "aws_ecs_service" "app" {
      + availability_zone_rebalancing      = "DISABLED"
      + cluster                            = (known after apply)
      + deployment_maximum_percent         = 200
      + deployment_minimum_healthy_percent = 100
      + desired_count                      = 2
      + enable_ecs_managed_tags            = false
      + enable_execute_command             = false
      + iam_role                           = (known after apply)
      + id                                 = (known after apply)
      + launch_type                        = "FARGATE"
      + name                               = "flask-app-service"
      + platform_version                   = (known after apply)
      + scheduling_strategy                = "REPLICA"
      + tags_all                           = (known after apply)
      + task_definition                    = (known after apply)
      + triggers                           = (known after apply)
      + wait_for_steady_state              = false

      + network_configuration {
          + assign_public_ip = false
          + security_groups  = (known after apply)
          + subnets          = (known after apply)
        }
    }

  # aws_ecs_task_definition.app will be created
  + resource "aws_ecs_task_definition" "app" {
      + arn                      = (known after apply)
      + arn_without_revision     = (known after apply)
      + container_definitions    = jsonencode(
            [
              + {
                  + essential        = true
                  + image            = "123456789012.dkr.ecr.us-west-2.amazonaws.com/my-flask-app:latest"
                  + logConfiguration = {
                      + logDriver = "awslogs"
                      + options   = {
                          + awslogs-group         = "/ecs/flask-app"
                          + awslogs-region        = "us-west-2"
                          + awslogs-stream-prefix = "ecs"
                        }
                    }
                  + name             = "flask-container"
                  + portMappings     = [
                      + {
                          + containerPort = 5000
                        },
                    ]
                },
            ]
        )
      + cpu                      = "256"
      + enable_fault_injection   = (known after apply)
      + execution_role_arn       = (known after apply)
      + family                   = "flask-app-task"
      + id                       = (known after apply)
      + memory                   = "512"
      + network_mode             = "awsvpc"
      + requires_compatibilities = [
          + "FARGATE",
        ]
      + revision                 = (known after apply)
      + skip_destroy             = false
      + tags_all                 = (known after apply)
      + track_latest             = false
    }

  # aws_flow_log.vpc will be created
  + resource "aws_flow_log" "vpc" {
      + arn                      = (known after apply)
      + iam_role_arn             = (known after apply)
      + id                       = (known after apply)
      + log_destination          = (known after apply)
      + log_destination_type     = "cloud-watch-logs"
      + log_format               = (known after apply)
      + log_group_name           = (known after apply)
      + max_aggregation_interval = 600
      + tags_all                 = (known after apply)
      + traffic_type             = "ALL"
      + vpc_id                   = (known after apply)
    }

  # aws_iam_policy.ecs_task_execution_policy will be created
  + resource "aws_iam_policy" "ecs_task_execution_policy" {
      + arn              = (known after apply)
      + attachment_count = (known after apply)
      + description      = "Policy for ECS Task Execution to interact with AWS resources"
      + id               = (known after apply)
      + name             = "ecs-task-execution-policy"
      + name_prefix      = (known after apply)
      + path             = "/"
      + policy           = jsonencode(
            {
              + Statement = [
                  + {
                      + Action   = [
                          + "logs:CreateLogStream",
                          + "logs:PutLogEvents",
                          + "ecr:GetAuthorizationToken",
                          + "ecr:BatchGetImage",
                          + "ecr:GetImage",
                        ]
                      + Effect   = "Allow"
                      + Resource = "*"
                    },
                ]
              + Version   = "2012-10-17"
            }
        )
      + policy_id        = (known after apply)
      + tags_all         = (known after apply)
    }

  # aws_iam_policy.vpc_flow_logs_policy will be created
  + resource "aws_iam_policy" "vpc_flow_logs_policy" {
      + arn              = (known after apply)
      + attachment_count = (known after apply)
      + description      = "Allows VPC Flow Logs to write logs to CloudWatch"
      + id               = (known after apply)
      + name             = "vpc-flow-logs-policy"
      + name_prefix      = (known after apply)
      + path             = "/"
      + policy           = (known after apply)
      + policy_id        = (known after apply)
      + tags_all         = (known after apply)
    }

  # aws_iam_policy_attachment.vpc_flow_logs_policy_attach will be created
  + resource "aws_iam_policy_attachment" "vpc_flow_logs_policy_attach" {
      + id         = (known after apply)
      + name       = "vpc-flow-logs-policy-attach"
      + policy_arn = (known after apply)
      + roles      = [
          + "vpc-flow-logs-role",
        ]
    }

  # aws_iam_role.ecs_task_execution will be created
  + resource "aws_iam_role" "ecs_task_execution" {
      + arn                   = (known after apply)
      + assume_role_policy    = jsonencode(
            {
              + Statement = [
                  + {
                      + Action    = "sts:AssumeRole"
                      + Effect    = "Allow"
                      + Principal = {
                          + Service = "ecs-tasks.amazonaws.com"
                        }
                    },
                ]
              + Version   = "2012-10-17"
            }
        )
      + create_date           = (known after apply)
      + force_detach_policies = false
      + id                    = (known after apply)
      + managed_policy_arns   = (known after apply)
      + max_session_duration  = 3600
      + name                  = "ecs-task-execution-role"
      + name_prefix           = (known after apply)
      + path                  = "/"
      + tags_all              = (known after apply)
      + unique_id             = (known after apply)

      + inline_policy (known after apply)
    }

  # aws_iam_role.vpc_flow_logs_role will be created
  + resource "aws_iam_role" "vpc_flow_logs_role" {
      + arn                   = (known after apply)
      + assume_role_policy    = jsonencode(
            {
              + Statement = [
                  + {
                      + Action    = "sts:AssumeRole"
                      + Effect    = "Allow"
                      + Principal = {
                          + Service = "vpc-flow-logs.amazonaws.com"
                        }
                    },
                ]
              + Version   = "2012-10-17"
            }
        )
      + create_date           = (known after apply)
      + force_detach_policies = false
      + id                    = (known after apply)
      + managed_policy_arns   = (known after apply)
      + max_session_duration  = 3600
      + name                  = "vpc-flow-logs-role"
      + name_prefix           = (known after apply)
      + path                  = "/"
      + tags_all              = (known after apply)
      + unique_id             = (known after apply)

      + inline_policy (known after apply)
    }

  # aws_iam_role_policy_attachment.ecs_task_execution_policy_attach will be created
  + resource "aws_iam_role_policy_attachment" "ecs_task_execution_policy_attach" {
      + id         = (known after apply)
      + policy_arn = (known after apply)
      + role       = "ecs-task-execution-role"
    }

  # aws_lb.app will be created
  + resource "aws_lb" "app" {
      + arn                                                          = (known after apply)
      + arn_suffix                                                   = (known after apply)
      + client_keep_alive                                            = 3600
      + desync_mitigation_mode                                       = "defensive"
      + dns_name                                                     = (known after apply)
      + drop_invalid_header_fields                                   = false
      + enable_deletion_protection                                   = false
      + enable_http2                                                 = true
      + enable_tls_version_and_cipher_suite_headers                  = false
      + enable_waf_fail_open                                         = false
      + enable_xff_client_port                                       = false
      + enable_zonal_shift                                           = false
      + enforce_security_group_inbound_rules_on_private_link_traffic = (known after apply)
      + id                                                           = (known after apply)
      + idle_timeout                                                 = 60
      + internal                                                     = false
      + ip_address_type                                              = (known after apply)
      + load_balancer_type                                           = "application"
      + name                                                         = "flask-app-alb"
      + name_prefix                                                  = (known after apply)
      + preserve_host_header                                         = false
      + security_groups                                              = (known after apply)
      + subnets                                                      = (known after apply)
      + tags_all                                                     = (known after apply)
      + vpc_id                                                       = (known after apply)
      + xff_header_processing_mode                                   = "append"
      + zone_id                                                      = (known after apply)

      + subnet_mapping (known after apply)
    }

  # aws_lb_listener.app will be created
  + resource "aws_lb_listener" "app" {
      + arn                                                                   = (known after apply)
      + id                                                                    = (known after apply)
      + load_balancer_arn                                                     = (known after apply)
      + port                                                                  = 80
      + protocol                                                              = "HTTP"
      + routing_http_request_x_amzn_mtls_clientcert_header_name               = (known after apply)
      + routing_http_request_x_amzn_mtls_clientcert_issuer_header_name        = (known after apply)
      + routing_http_request_x_amzn_mtls_clientcert_leaf_header_name          = (known after apply)
      + routing_http_request_x_amzn_mtls_clientcert_serial_number_header_name = (known after apply)
      + routing_http_request_x_amzn_mtls_clientcert_subject_header_name       = (known after apply)
      + routing_http_request_x_amzn_mtls_clientcert_validity_header_name      = (known after apply)
      + routing_http_request_x_amzn_tls_cipher_suite_header_name              = (known after apply)
      + routing_http_request_x_amzn_tls_version_header_name                   = (known after apply)
      + routing_http_response_access_control_allow_credentials_header_value   = (known after apply)
      + routing_http_response_access_control_allow_headers_header_value       = (known after apply)
      + routing_http_response_access_control_allow_methods_header_value       = (known after apply)
      + routing_http_response_access_control_allow_origin_header_value        = (known after apply)
      + routing_http_response_access_control_expose_headers_header_value      = (known after apply)
      + routing_http_response_access_control_max_age_header_value             = (known after apply)
      + routing_http_response_content_security_policy_header_value            = (known after apply)
      + routing_http_response_server_enabled                                  = (known after apply)
      + routing_http_response_strict_transport_security_header_value          = (known after apply)
      + routing_http_response_x_content_type_options_header_value             = (known after apply)
      + routing_http_response_x_frame_options_header_value                    = (known after apply)
      + ssl_policy                                                            = (known after apply)
      + tags_all                                                              = (known after apply)
      + tcp_idle_timeout_seconds                                              = (known after apply)

      + default_action {
          + order            = (known after apply)
          + target_group_arn = (known after apply)
          + type             = "forward"
        }

      + mutual_authentication (known after apply)
    }

  # aws_lb_target_group.app will be created
  + resource "aws_lb_target_group" "app" {
      + arn                                = (known after apply)
      + arn_suffix                         = (known after apply)
      + connection_termination             = (known after apply)
      + deregistration_delay               = "300"
      + id                                 = (known after apply)
      + ip_address_type                    = (known after apply)
      + lambda_multi_value_headers_enabled = false
      + load_balancer_arns                 = (known after apply)
      + load_balancing_algorithm_type      = (known after apply)
      + load_balancing_anomaly_mitigation  = (known after apply)
      + load_balancing_cross_zone_enabled  = (known after apply)
      + name                               = "flask-app-tg"
      + name_prefix                        = (known after apply)
      + port                               = 5000
      + preserve_client_ip                 = (known after apply)
      + protocol                           = "HTTP"
      + protocol_version                   = (known after apply)
      + proxy_protocol_v2                  = false
      + slow_start                         = 0
      + tags_all                           = (known after apply)
      + target_type                        = "ip"
      + vpc_id                             = (known after apply)

      + health_check (known after apply)

      + stickiness (known after apply)

      + target_failover (known after apply)

      + target_group_health (known after apply)

      + target_health_state (known after apply)
    }

  # aws_security_group.alb_sg will be created
  + resource "aws_security_group" "alb_sg" {
      + arn                    = (known after apply)
      + description            = "Allow HTTP/HTTPS access to ALB"
      + egress                 = [
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + from_port        = 0
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "-1"
              + security_groups  = []
              + self             = false
              + to_port          = 0
                # (1 unchanged attribute hidden)
            },
        ]
      + id                     = (known after apply)
      + ingress                = [
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + from_port        = 443
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 443
                # (1 unchanged attribute hidden)
            },
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + from_port        = 80
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 80
                # (1 unchanged attribute hidden)
            },
        ]
      + name                   = "alb-sg"
      + name_prefix            = (known after apply)
      + owner_id               = (known after apply)
      + revoke_rules_on_delete = false
      + tags_all               = (known after apply)
      + vpc_id                 = (known after apply)
    }

  # aws_security_group.app will be created
  + resource "aws_security_group" "app" {
      + arn                    = (known after apply)
      + description            = "Allow inbound access to ECS service from ALB only"
      + egress                 = [
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + from_port        = 0
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "-1"
              + security_groups  = []
              + self             = false
              + to_port          = 0
                # (1 unchanged attribute hidden)
            },
        ]
      + id                     = (known after apply)
      + ingress                = [
          + {
              + cidr_blocks      = []
              + from_port        = 5000
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = (known after apply)
              + self             = false
              + to_port          = 5000
                # (1 unchanged attribute hidden)
            },
        ]
      + name                   = "ecs-service-sg"
      + name_prefix            = (known after apply)
      + owner_id               = (known after apply)
      + revoke_rules_on_delete = false
      + tags_all               = (known after apply)
      + vpc_id                 = (known after apply)
    }

  # module.vpc.aws_default_network_acl.this[0] will be created
  + resource "aws_default_network_acl" "this" {
      + arn                    = (known after apply)
      + default_network_acl_id = (known after apply)
      + id                     = (known after apply)
      + owner_id               = (known after apply)
      + tags                   = {
          + "Name" = "flash-app-vpc-default"
        }
      + tags_all               = {
          + "Name" = "flash-app-vpc-default"
        }
      + vpc_id                 = (known after apply)

      + egress {
          + action          = "allow"
          + from_port       = 0
          + ipv6_cidr_block = "::/0"
          + protocol        = "-1"
          + rule_no         = 101
          + to_port         = 0
            # (1 unchanged attribute hidden)
        }
      + egress {
          + action          = "allow"
          + cidr_block      = "0.0.0.0/0"
          + from_port       = 0
          + protocol        = "-1"
          + rule_no         = 100
          + to_port         = 0
            # (1 unchanged attribute hidden)
        }

      + ingress {
          + action          = "allow"
          + from_port       = 0
          + ipv6_cidr_block = "::/0"
          + protocol        = "-1"
          + rule_no         = 101
          + to_port         = 0
            # (1 unchanged attribute hidden)
        }
      + ingress {
          + action          = "allow"
          + cidr_block      = "0.0.0.0/0"
          + from_port       = 0
          + protocol        = "-1"
          + rule_no         = 100
          + to_port         = 0
            # (1 unchanged attribute hidden)
        }
    }

  # module.vpc.aws_default_route_table.default[0] will be created
  + resource "aws_default_route_table" "default" {
      + arn                    = (known after apply)
      + default_route_table_id = (known after apply)
      + id                     = (known after apply)
      + owner_id               = (known after apply)
      + route                  = (known after apply)
      + tags                   = {
          + "Name" = "flash-app-vpc-default"
        }
      + tags_all               = {
          + "Name" = "flash-app-vpc-default"
        }
      + vpc_id                 = (known after apply)

      + timeouts {
          + create = "5m"
          + update = "5m"
        }
    }

  # module.vpc.aws_default_security_group.this[0] will be created
  + resource "aws_default_security_group" "this" {
      + arn                    = (known after apply)
      + description            = (known after apply)
      + egress                 = (known after apply)
      + id                     = (known after apply)
      + ingress                = (known after apply)
      + name                   = (known after apply)
      + name_prefix            = (known after apply)
      + owner_id               = (known after apply)
      + revoke_rules_on_delete = false
      + tags                   = {
          + "Name" = "flash-app-vpc-default"
        }
      + tags_all               = {
          + "Name" = "flash-app-vpc-default"
        }
      + vpc_id                 = (known after apply)
    }

  # module.vpc.aws_eip.nat[0] will be created
  + resource "aws_eip" "nat" {
      + allocation_id        = (known after apply)
      + arn                  = (known after apply)
      + association_id       = (known after apply)
      + carrier_ip           = (known after apply)
      + customer_owned_ip    = (known after apply)
      + domain               = "vpc"
      + id                   = (known after apply)
      + instance             = (known after apply)
      + ipam_pool_id         = (known after apply)
      + network_border_group = (known after apply)
      + network_interface    = (known after apply)
      + private_dns          = (known after apply)
      + private_ip           = (known after apply)
      + ptr_record           = (known after apply)
      + public_dns           = (known after apply)
      + public_ip            = (known after apply)
      + public_ipv4_pool     = (known after apply)
      + tags                 = {
          + "Name" = "flash-app-vpc-us-west-2a"
        }
      + tags_all             = {
          + "Name" = "flash-app-vpc-us-west-2a"
        }
      + vpc                  = (known after apply)
    }

  # module.vpc.aws_eip.nat[1] will be created
  + resource "aws_eip" "nat" {
      + allocation_id        = (known after apply)
      + arn                  = (known after apply)
      + association_id       = (known after apply)
      + carrier_ip           = (known after apply)
      + customer_owned_ip    = (known after apply)
      + domain               = "vpc"
      + id                   = (known after apply)
      + instance             = (known after apply)
      + ipam_pool_id         = (known after apply)
      + network_border_group = (known after apply)
      + network_interface    = (known after apply)
      + private_dns          = (known after apply)
      + private_ip           = (known after apply)
      + ptr_record           = (known after apply)
      + public_dns           = (known after apply)
      + public_ip            = (known after apply)
      + public_ipv4_pool     = (known after apply)
      + tags                 = {
          + "Name" = "flash-app-vpc-us-west-2b"
        }
      + tags_all             = {
          + "Name" = "flash-app-vpc-us-west-2b"
        }
      + vpc                  = (known after apply)
    }

  # module.vpc.aws_internet_gateway.this[0] will be created
  + resource "aws_internet_gateway" "this" {
      + arn      = (known after apply)
      + id       = (known after apply)
      + owner_id = (known after apply)
      + tags     = {
          + "Name" = "flash-app-vpc"
        }
      + tags_all = {
          + "Name" = "flash-app-vpc"
        }
      + vpc_id   = (known after apply)
    }

  # module.vpc.aws_nat_gateway.this[0] will be created
  + resource "aws_nat_gateway" "this" {
      + allocation_id                      = (known after apply)
      + association_id                     = (known after apply)
      + connectivity_type                  = "public"
      + id                                 = (known after apply)
      + network_interface_id               = (known after apply)
      + private_ip                         = (known after apply)
      + public_ip                          = (known after apply)
      + secondary_private_ip_address_count = (known after apply)
      + secondary_private_ip_addresses     = (known after apply)
      + subnet_id                          = (known after apply)
      + tags                               = {
          + "Name" = "flash-app-vpc-us-west-2a"
        }
      + tags_all                           = {
          + "Name" = "flash-app-vpc-us-west-2a"
        }
    }

  # module.vpc.aws_nat_gateway.this[1] will be created
  + resource "aws_nat_gateway" "this" {
      + allocation_id                      = (known after apply)
      + association_id                     = (known after apply)
      + connectivity_type                  = "public"
      + id                                 = (known after apply)
      + network_interface_id               = (known after apply)
      + private_ip                         = (known after apply)
      + public_ip                          = (known after apply)
      + secondary_private_ip_address_count = (known after apply)
      + secondary_private_ip_addresses     = (known after apply)
      + subnet_id                          = (known after apply)
      + tags                               = {
          + "Name" = "flash-app-vpc-us-west-2b"
        }
      + tags_all                           = {
          + "Name" = "flash-app-vpc-us-west-2b"
        }
    }

  # module.vpc.aws_route.private_nat_gateway[0] will be created
  + resource "aws_route" "private_nat_gateway" {
      + destination_cidr_block = "0.0.0.0/0"
      + id                     = (known after apply)
      + instance_id            = (known after apply)
      + instance_owner_id      = (known after apply)
      + nat_gateway_id         = (known after apply)
      + network_interface_id   = (known after apply)
      + origin                 = (known after apply)
      + route_table_id         = (known after apply)
      + state                  = (known after apply)

      + timeouts {
          + create = "5m"
        }
    }

  # module.vpc.aws_route.private_nat_gateway[1] will be created
  + resource "aws_route" "private_nat_gateway" {
      + destination_cidr_block = "0.0.0.0/0"
      + id                     = (known after apply)
      + instance_id            = (known after apply)
      + instance_owner_id      = (known after apply)
      + nat_gateway_id         = (known after apply)
      + network_interface_id   = (known after apply)
      + origin                 = (known after apply)
      + route_table_id         = (known after apply)
      + state                  = (known after apply)

      + timeouts {
          + create = "5m"
        }
    }

  # module.vpc.aws_route.public_internet_gateway[0] will be created
  + resource "aws_route" "public_internet_gateway" {
      + destination_cidr_block = "0.0.0.0/0"
      + gateway_id             = (known after apply)
      + id                     = (known after apply)
      + instance_id            = (known after apply)
      + instance_owner_id      = (known after apply)
      + network_interface_id   = (known after apply)
      + origin                 = (known after apply)
      + route_table_id         = (known after apply)
      + state                  = (known after apply)

      + timeouts {
          + create = "5m"
        }
    }

  # module.vpc.aws_route_table.private[0] will be created
  + resource "aws_route_table" "private" {
      + arn              = (known after apply)
      + id               = (known after apply)
      + owner_id         = (known after apply)
      + propagating_vgws = (known after apply)
      + route            = (known after apply)
      + tags             = {
          + "Name" = "flash-app-vpc-private-us-west-2a"
        }
      + tags_all         = {
          + "Name" = "flash-app-vpc-private-us-west-2a"
        }
      + vpc_id           = (known after apply)
    }

  # module.vpc.aws_route_table.private[1] will be created
  + resource "aws_route_table" "private" {
      + arn              = (known after apply)
      + id               = (known after apply)
      + owner_id         = (known after apply)
      + propagating_vgws = (known after apply)
      + route            = (known after apply)
      + tags             = {
          + "Name" = "flash-app-vpc-private-us-west-2b"
        }
      + tags_all         = {
          + "Name" = "flash-app-vpc-private-us-west-2b"
        }
      + vpc_id           = (known after apply)
    }

  # module.vpc.aws_route_table.public[0] will be created
  + resource "aws_route_table" "public" {
      + arn              = (known after apply)
      + id               = (known after apply)
      + owner_id         = (known after apply)
      + propagating_vgws = (known after apply)
      + route            = (known after apply)
      + tags             = {
          + "Name" = "flash-app-vpc-public"
        }
      + tags_all         = {
          + "Name" = "flash-app-vpc-public"
        }
      + vpc_id           = (known after apply)
    }

  # module.vpc.aws_route_table_association.private[0] will be created
  + resource "aws_route_table_association" "private" {
      + id             = (known after apply)
      + route_table_id = (known after apply)
      + subnet_id      = (known after apply)
    }

  # module.vpc.aws_route_table_association.private[1] will be created
  + resource "aws_route_table_association" "private" {
      + id             = (known after apply)
      + route_table_id = (known after apply)
      + subnet_id      = (known after apply)
    }

  # module.vpc.aws_route_table_association.public[0] will be created
  + resource "aws_route_table_association" "public" {
      + id             = (known after apply)
      + route_table_id = (known after apply)
      + subnet_id      = (known after apply)
    }

  # module.vpc.aws_route_table_association.public[1] will be created
  + resource "aws_route_table_association" "public" {
      + id             = (known after apply)
      + route_table_id = (known after apply)
      + subnet_id      = (known after apply)
    }

  # module.vpc.aws_subnet.private[0] will be created
  + resource "aws_subnet" "private" {
      + arn                                            = (known after apply)
      + assign_ipv6_address_on_creation                = false
      + availability_zone                              = "us-west-2a"
      + availability_zone_id                           = (known after apply)
      + cidr_block                                     = "10.0.1.0/24"
      + enable_dns64                                   = false
      + enable_resource_name_dns_a_record_on_launch    = false
      + enable_resource_name_dns_aaaa_record_on_launch = false
      + id                                             = (known after apply)
      + ipv6_cidr_block_association_id                 = (known after apply)
      + ipv6_native                                    = false
      + map_public_ip_on_launch                        = false
      + owner_id                                       = (known after apply)
      + private_dns_hostname_type_on_launch            = (known after apply)
      + tags                                           = {
          + "Name" = "flash-app-vpc-private-us-west-2a"
        }
      + tags_all                                       = {
          + "Name" = "flash-app-vpc-private-us-west-2a"
        }
      + vpc_id                                         = (known after apply)
    }

  # module.vpc.aws_subnet.private[1] will be created
  + resource "aws_subnet" "private" {
      + arn                                            = (known after apply)
      + assign_ipv6_address_on_creation                = false
      + availability_zone                              = "us-west-2b"
      + availability_zone_id                           = (known after apply)
      + cidr_block                                     = "10.0.2.0/24"
      + enable_dns64                                   = false
      + enable_resource_name_dns_a_record_on_launch    = false
      + enable_resource_name_dns_aaaa_record_on_launch = false
      + id                                             = (known after apply)
      + ipv6_cidr_block_association_id                 = (known after apply)
      + ipv6_native                                    = false
      + map_public_ip_on_launch                        = false
      + owner_id                                       = (known after apply)
      + private_dns_hostname_type_on_launch            = (known after apply)
      + tags                                           = {
          + "Name" = "flash-app-vpc-private-us-west-2b"
        }
      + tags_all                                       = {
          + "Name" = "flash-app-vpc-private-us-west-2b"
        }
      + vpc_id                                         = (known after apply)
    }

  # module.vpc.aws_subnet.public[0] will be created
  + resource "aws_subnet" "public" {
      + arn                                            = (known after apply)
      + assign_ipv6_address_on_creation                = false
      + availability_zone                              = "us-west-2a"
      + availability_zone_id                           = (known after apply)
      + cidr_block                                     = "10.0.101.0/24"
      + enable_dns64                                   = false
      + enable_resource_name_dns_a_record_on_launch    = false
      + enable_resource_name_dns_aaaa_record_on_launch = false
      + id                                             = (known after apply)
      + ipv6_cidr_block_association_id                 = (known after apply)
      + ipv6_native                                    = false
      + map_public_ip_on_launch                        = false
      + owner_id                                       = (known after apply)
      + private_dns_hostname_type_on_launch            = (known after apply)
      + tags                                           = {
          + "Name" = "flash-app-vpc-public-us-west-2a"
        }
      + tags_all                                       = {
          + "Name" = "flash-app-vpc-public-us-west-2a"
        }
      + vpc_id                                         = (known after apply)
    }

  # module.vpc.aws_subnet.public[1] will be created
  + resource "aws_subnet" "public" {
      + arn                                            = (known after apply)
      + assign_ipv6_address_on_creation                = false
      + availability_zone                              = "us-west-2b"
      + availability_zone_id                           = (known after apply)
      + cidr_block                                     = "10.0.102.0/24"
      + enable_dns64                                   = false
      + enable_resource_name_dns_a_record_on_launch    = false
      + enable_resource_name_dns_aaaa_record_on_launch = false
      + id                                             = (known after apply)
      + ipv6_cidr_block_association_id                 = (known after apply)
      + ipv6_native                                    = false
      + map_public_ip_on_launch                        = false
      + owner_id                                       = (known after apply)
      + private_dns_hostname_type_on_launch            = (known after apply)
      + tags                                           = {
          + "Name" = "flash-app-vpc-public-us-west-2b"
        }
      + tags_all                                       = {
          + "Name" = "flash-app-vpc-public-us-west-2b"
        }
      + vpc_id                                         = (known after apply)
    }

  # module.vpc.aws_vpc.this[0] will be created
  + resource "aws_vpc" "this" {
      + arn                                  = (known after apply)
      + cidr_block                           = "10.0.0.0/16"
      + default_network_acl_id               = (known after apply)
      + default_route_table_id               = (known after apply)
      + default_security_group_id            = (known after apply)
      + dhcp_options_id                      = (known after apply)
      + enable_dns_hostnames                 = true
      + enable_dns_support                   = true
      + enable_network_address_usage_metrics = (known after apply)
      + id                                   = (known after apply)
      + instance_tenancy                     = "default"
      + ipv6_association_id                  = (known after apply)
      + ipv6_cidr_block                      = (known after apply)
      + ipv6_cidr_block_network_border_group = (known after apply)
      + main_route_table_id                  = (known after apply)
      + owner_id                             = (known after apply)
      + tags                                 = {
          + "Name" = "flash-app-vpc"
        }
      + tags_all                             = {
          + "Name" = "flash-app-vpc"
        }
    }

Plan: 41 to add, 0 to change, 0 to destroy
```
---
To create the resources in AWS, run:
```plaintext
## terraform apply
---
## Push Docker Image to ECR
```
After the initial infrastructure is set up, build and push your Docker image to ECR:
or allow the github action to perform the operation.
```plaintext
## docker build --no-cache -t flask-app:1.0.0 .
## docker run -d -p 5000:5000 --name flask-container flask-app:1.0.0
```
```plaintext
##$(aws ecr get-login --no-include-email --region us-east-1)
## docker tag flask-app:latest <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/flask-app-repository:latest
## docker push <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/flask-app-repository:latest##
```
---
## Verify ECS Deployment:

Go to the AWS ECS console and ensure that the service is running with the Flask app.
---
# Clean Up

To delete all the resources created by Terraform, run:
```plaintext
## terraform destroy
```
This will remove the VPC, ECS cluster, task definition, service, and other resources.
---
# Further Considerations

## Infracost 

Build infracost into the terraform deploy CI/CD pipeline to allow costing of any terraform changes
before deployment.   

This tool utilises the AWS Cost API to build its analysis on any scanned terraform code.

![image](https://github.com/user-attachments/assets/618cd06a-6442-4340-95be-fa9d0359c022)

[Infracost:](https://www.infracost.io/)
---
## Trivy

Consider adding a **TRIVY** step in the docker CI/CD pipeline to highlight CVE Vulnerabilities before image push to ECR.

![image](https://github.com/user-attachments/assets/f9274a86-930f-4368-9ef1-7cfc961d1b8d)

[Trivy](https://github.com/aquasecurity/trivy)
---
## tfsec

Consider adding a **TFSEC** step in the terraform CI/CD pipeline to highlight terraform vulnerabilities and ensure best practice.

![image](https://github.com/user-attachments/assets/e4f0e4b7-e815-494a-93a9-71d1592d6e75)

[Tfsec](https://github.com/aquasecurity/tfsec)
---
## Utilise a self-hosted github actions runner 

Consider a self hosted actions runner to mitigate potential github runner run limits and costs

![image](https://github.com/user-attachments/assets/50e9575b-1b87-4de6-be81-fd6b0fc494d9)
---

