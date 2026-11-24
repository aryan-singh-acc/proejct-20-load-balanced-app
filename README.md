# Project 20 -- Load Balanced App Deployment on AWS

## Overview

This project demonstrates a highly available web application deployed on
two Amazon EC2 instances behind an internet-facing Application Load
Balancer (ALB).

The ALB distributes incoming HTTP traffic across both web servers. Its
target group performs health checks and sends traffic only to healthy
registered instances.

## Architecture

``` text
                         Internet
                            |
                            | HTTP : 80
                            v
              Application Load Balancer
                 project-20-alb-aryan
                            |
                    Target Group : 80
                    project-20-web-tg
                       /          \
                      /            \
                     v              v
             EC2 Web Server 1   EC2 Web Server 2
                Apache              Apache
             HTTP : 80            HTTP : 80
             eu-west-1a           eu-west-1b
```

## AWS Services Used

-   Amazon VPC
-   Public subnets in two Availability Zones
-   Internet Gateway and route table
-   Amazon EC2
-   EC2 key pair
-   Security Groups
-   Application Load Balancer
-   Target Group and HTTP health checks

## Deployment Details

  ---------------------------------------------------------------------------------------------------
  Component                           Configuration
  ----------------------------------- ---------------------------------------------------------------
  AWS Region                          Europe (Ireland) -- `eu-west-1`

  VPC                                 `project-20-aryan-vpc`

  VPC ID                              `vpc-069bb8b8b4dd6e0db`

  Public subnet 1                     `subnet-0b3165592250d24d1` (`eu-west-1a`)

  Public subnet 2                     `subnet-0ae252b0cef2f802b` (`eu-west-1b`)

  EC2 instance 1                      `Project-20-Web-Server-1-aryan`

  Instance 1 ID                       `i-0a4ca53fd7f32eb7c`

  EC2 instance 2                      `Project-20-Web-Server-2-aryan`

  Instance 2 ID                       `i-0306beed88263a1d7`

  Instance type                       `t3.micro`

  Web server                          Apache HTTP Server

  Key pair                            `project-20-aryan-key`

  Target group                        `project-20-web-tg`

  Load balancer                       `project-20-alb-aryan`

  ALB DNS                             `project-20-alb-aryan-1758282771.eu-west-1.elb.amazonaws.com`
  ---------------------------------------------------------------------------------------------------

## Security Configuration

The deployment uses security groups to restrict access:

-   The ALB security group allows inbound HTTP traffic on port `80`.
-   The EC2 web-server security group allows inbound HTTP traffic on
    port `80` from the ALB security group.
-   SSH (`22`) and RDP (`3389`) are not opened.
-   The application is accessed through the ALB DNS name rather than
    directly through the EC2 instances.

## Application

Apache is installed and started automatically using EC2 user data.

Each instance serves a simple HTML page identifying which web server
handled the request:

-   **Server 1:** `Project 20 - Load Balanced Application` /
    `Response from Web Server 1`
-   **Server 2:** `Project 20 - Load Balanced Application` /
    `Response from Web Server 2`

## Load Balancer and Health Checks

The Application Load Balancer uses an HTTP listener on port `80` and
forwards requests to the `project-20-web-tg` target group.

The target group: - Uses instance targets. - Uses HTTP on port `80`. -
Performs HTTP health checks. - Registers both EC2 instances.

## How to Test

1.  Open the ALB DNS name in a browser using HTTP:

    `http://project-20-alb-aryan-1758282771.eu-west-1.elb.amazonaws.com`

2.  Refresh the page several times.

3.  Observe the response identify Web Server 1 or Web Server 2.

4.  In the AWS Console, open **EC2 → Target Groups →
    project-20-web-tg**.

5.  Confirm both registered targets show **Healthy**.

## Validation Results

The deployment was tested successfully:

-   Application Load Balancer status: **Active**
-   Registered targets: **2**
-   Healthy targets: **2**
-   Unhealthy targets: **0**
-   Browser test: responses from both Web Server 1 and Web Server 2 were
    observed through the ALB DNS name.

