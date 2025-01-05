# VPC-project-for-2-tier-application

![vpc-example-private-subnets](https://github.com/user-attachments/assets/23750c40-49f5-4770-a811-993ee8429f96)


This example demostrates how to create VPC that you can use for servers in a production environment.

To improve resiliency the servers are deployed in two avaliability zones, by using an auto scaling group and an application Load balancer. For additional security, the servers are deployed in private subnets. The servers receive requests through the load balancer. The servers receive requests through the load balancer. The servers can connect to the internet by using a NAT gateway. To improve resiliency, the NAT gateway in both avaliability zones.

# Overview 
The VPC has public subnets and private subnets in two avaliability zones
Each public subnet contains a NAT gateway and a load balancer 
The servers run in private subnets, are launched and terminated by using an auto scaling group, and receive traffic from the load balancer.
The servers can connect using internet by using the NAT gateway.

## Features
- **Load Balancing**: Distributes traffic evenly between instances using a load balancer.
- **Auto Scaling**: Automatically adjusts the number of instances based on traffic and performance metrics.
- **Bastion Host**: Provides secure SSH access to the private instances in the network.
- **High Availability**: Ensures application availability by routing traffic to healthy instances.

## Architecture Overview
1. **Load Balancer**:
   - Handles incoming traffic.
   - Routes requests to backend instances based on a configured algorithm (e.g., Round Robin).
2. **Target Group**:
   - Contains the backend instances registered to the load balancer.
   - Used for health checks and traffic routing.
3. **Auto Scaling**:
   - Dynamically scales the number of instances in the target group based on policies.
4. **Bastion Host**:
   - Deployed in a public subnet.
   - Allows secure access to private instances using SSH.

## Prerequisites
- An AWS account.
- EC2 instances running your application.
- An Elastic Load Balancer (ALB or CLB).
- Auto Scaling group configured.
- Bastion host configured in the same VPC.

## Setup Instructions

### 1. Create a VPC
1. Navigate to the **VPC** section in the AWS Management Console.
2. Create a new VPC:
   - Define an IPv4 CIDR block (e.g., `10.0.0.0/16`).
3. Add subnets:
   - Create public subnets for the bastion host and load balancer.
   - Create private subnets for application instances.
4. Configure an Internet Gateway and route tables:
   - Attach the Internet Gateway to the VPC.
   - Update the route table of public subnets to route traffic through the Internet Gateway.

### 2. Set Up Auto Scaling
1. Navigate to the **Auto Scaling Groups** section in the AWS Management Console.
2. Create an Auto Scaling group:
   - Use the previously created AMI.
   - Attach the group to the target group associated with your load balancer.
3. Configure Scaling Policies:
   - Set rules to scale out (add instances) or scale in (remove instances) based on metrics like CPU utilization or request counts.
4. Save the configuration.

### 3. Configure the Bastion Host
1. Launch an EC2 instance in a public subnet to act as the bastion host.
2. Assign a public IP to the bastion host.
3. Configure security groups:
   - Allow SSH access (port 22) only from your IP address.
   - Allow the bastion host to access private instances over SSH (port 22).
4. Install an SSH client on the bastion host for accessing private instances

### 4. SSH to Bastion Host and Connect to Private EC2 Instances
1. From your local machine, connect to the bastion host:
   ```bash
   ssh -i <your-key-pair>.pem ec2-user@<bastion-host-public-ip>
   ```
2. Once connected to the bastion host, use SSH to connect to a private EC2 instance:
   ```bash
   ssh -i <your-key-pair>.pem ec2-user@<private-ec2-private-ip>
   ```
3. Verify connectivity and ensure you can access application logs or perform necessary management tasks.

### 5. Configure Index.html on Private Instances
1. Log in to each private EC2 instance via the bastion host.
2. Create or update the `index.html` file in the web server's document root directory (e.g., `/var/www/html/`).
3. Add custom content to `index.html` to differentiate between instances, such as:
   ```html
   <html>
   <body>
       <h1>This is my first private instance</h1>
   </body>
   </html>
   ```
   ```html
   <html>
   <body>
       <h1>This is my second private instance</h1>
   </body>
   </html>
   ```

### 2. Configure the Load Balancer
1. Navigate to the **Load Balancer** section in the AWS Management Console.
2. Create an Application Load Balancer (ALB):
   - Scheme: Internet-facing.
   - Listeners: HTTP or HTTPS.
   - Target Group: Register the auto-scaling group.
3. Configure Health Checks:
   - Path: `/` (or the health check endpoint of your application).
   - Set appropriate thresholds for healthy/unhealthy status.
4. Save the configuration.   

### 5. Test the Setup
1. Access the load balancer's DNS name in a browser.
2. Refresh the page multiple times to observe traffic routing to different instances.
3. Simulate load to trigger auto-scaling and verify new instances are added automatically.
4. Use the bastion host to SSH into private instances for debugging or management.

## Troubleshooting
### Observing Delays After Refreshing
- Ensure all instances are healthy and pass health checks.
- Verify the network latency between the client and the load balancer.
- Check for session initialization delays on the backend.
- Use browser developer tools or AWS CloudWatch metrics to debug.

### Health Check Failing
- Confirm the health check path is accessible and returns a `200 OK` status.
- Check security group rules to allow load balancer traffic.

## License
This project is licensed under the MIT License. See the LICENSE file for details.



## Outputs

![Screenshot 2025-01-04 225959](https://github.com/user-attachments/assets/1d35f4fd-ae26-43e9-a63d-f64b78821fcd)
![Screenshot 2025-01-04 231138](https://github.com/user-attachments/assets/91039856-ee0b-4e15-9086-8962859e455d)


