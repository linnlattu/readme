# 01 VPC CloudFormation Template
# Parameters
    - ProjectName: The name of the project.
    - Environment: Deployment environment (e.g., dev, prod).
    - Region: AWS region where the resources will be deployed. Defaults to ap-southeast-1.
    - VPCcidr: CIDR range for the VPC.
    - PublicSubnet1cidr: CIDR range for Public Subnet 1.
    - PublicSubnet2cidr: CIDR range for Public Subnet 2.
    - PublicSubnet3cidr: CIDR range for Public Subnet 3.
    - PrivateSubnet1cidr: CIDR range for Private Subnet 1.
    - PrivateSubnet2cidr: CIDR range for Private Subnet 2.
    - PrivateSubnet3cidr: CIDR range for Private Subnet 3.
# Resources & Properties
1. Virtual Private Cloud
    - VPC: Creates a VPC with the specified CIDR block, DNS support, and DNS hostnames enabled.
    - InternetGateway: Creates an internet gateway and attaches it to the VPC.
    - PublicSubnets: Creates three public subnets across different availability zones.
    - PrivateSubnets: Creates three private subnets across different availability zones.
    - PublicRouteTable: Creates a route table for public subnets and adds a default route to the internet gateway.
    - PrivateRouteTable: Creates a route table for private subnets and adds a default route to the NAT gateway.
    - NATGateway: Creates a NAT gateway in the first public subnet with an elastic IP address.
    - SubnetRouteTableAssociations: Associates each subnet with the corresponding route table.
# Outputs
    - VpcId: The ID of the created VPC.
    - PublicSubnet1Id: The ID of Public Subnet 1.
    - PublicSubnet2Id: The ID of Public Subnet 2.
    - PublicSubnet3Id: The ID of Public Subnet 3.
    - PrivateSubnet1Id: The ID of Private Subnet 1.
    - PrivateSubnet2Id: The ID of Private Subnet 2.
    - PrivateSubnet3Id: The ID of Private Subnet 3.
    - NATGatewayId: The ID of the NAT Gateway.
# Usage
1. Parameters
    - Ensure you have the appropriate values for the parameters before deploying the stack. This includes the CIDR blocks for the VPC and subnets.
2. Deploy
    - Use the AWS Management Console, AWS CLI, or an infrastructure-as-code tool (AWS CloudFormation) to deploy the CloudFormation stack.
3. Access Resources
    - After deployment, use the outputs to reference the created resources in your applications or other CloudFormation stacks.

# 02 ECR CloudFormation Template
# Parameters
    - ProjectName: The name of the project.
    - Environment: Deployment environment (e.g., dev, prod).
# Resources & Properties
1. Elastic Container Registry
    - RepositoryName: The name of the repository. It is formed using the project name and environment.
    - ImageScanningConfiguration: Automatically scans images on push.
    - Tags: Tags for the repository.
    - Environment: The deployment environment.
    - Project: The project name.
    - LifecyclePolicy: A lifecycle policy that keeps only the last 5 images.
    - RepositoryPolicyText: A repository policy that allows push and pull actions for the root user of the account.
# Outputs
    - ECRRepositoryName: The name of the created ECR repository.
# Usage
1. Parameters
    - Ensure you have the appropriate values for the parameters before deploying the stack. This includes the project name and environment.
2. Deploy
    - Use the AWS Management Console, AWS CLI, or an infrastructure-as-code tool (AWS CloudFormation) to deploy the CloudFormation stack.
3. Access Resources
    - After deployment, use the outputs to reference the created resources in your applications or other CloudFormation stacks.

# 03 Security CloudFormation Template
# Parameters
    - ProjectName: The name of the project.
    - Environment: Deployment environment (e.g., dev, prod).
    - ContainerPort: Container port for the ECS service.
# Resources & Properties
1. EcsSecurityGroup
    - GroupDescription: Description of the security group.
    - VpcId: VPC ID imported from an existing stack.
    - SecurityGroupIngress: Ingress rule allowing traffic on the specified container port from any IP address.
2. EcsTaskExecutionRole
    - AssumeRolePolicyDocument: Policy that allows ECS tasks to assume this role.
    - Policies: Policies that provide permissions to interact with ECR and CloudWatch Logs.
# Outputs
    - EcsTaskExecutionRole: The ARN of the created IAM role for ECS tasks.
    - EcsSecurityGroup: The ID of the created security group for ECS tasks.
# Usage
1. Parameters
    - Ensure you have the appropriate values for the parameters before deploying the stack. This includes the project name, environment, and container port.
2. Deploy
    - Use the AWS Management Console, AWS CLI, or an infrastructure-as-code tool (AWS CloudFormation) to deploy the CloudFormation stack.
3. Access Resources
    - After deployment, use the outputs to reference the created resources in your applications or other CloudFormation stacks.

# 04 ECS Cluster CloudFormation Template
# Parameters
    - ProjectName: The name of the project.
    - Environment: Deployment environment (e.g., dev, prod).
# Resources & Properties
1. ECS Cluster
    - ClusterName: The name of the ECS cluster. It is formed using the project name and environment.
    - ClusterSettings: Enables container insights for enhanced monitoring.
    - Tags: Tags for the ECS cluster.
        - Environment: The deployment environment.
# Outputs
    - ClusterName: The name of the created ECS cluster.
# Usage
1. Parameters
    - Ensure you have the appropriate values for the parameters before deploying the stack. This includes the project name and environment.
2. Deploy
    - Use the AWS Management Console, AWS CLI, or an infrastructure-as-code tool (AWS CloudFormation) to deploy the CloudFormation stack.
3. Access Resources
    - After deployment, use the outputs to reference the created resources in your applications or other CloudFormation stacks.

# 05 ECS Service CloudFormation Template
# Parameters
    - ProjectName: The name of the project.
    - Environment: Deployment environment (e.g., dev, prod).
    - CPU: CPU size (MB) for the ECS task. Default is 256 MB.
    - Memory: Memory size (MB) for the ECS task. Default is 512 MB.
    - ContainerPort: Container port for the ECS service.
    - DesiredCount: Desired count for ECS service.
    - ECRName: ECR repository name.
# Resources & Properties
1. LoadBalancer
    - Name: The name of the load balancer.
    - Subnets: Public subnets for the load balancer.
    - SecurityGroups: Security group for the load balancer.
    - Scheme: Set to internet-facing.
    - LoadBalancerAttributes: Specifies attributes such as idle timeout and HTTP/2 routing.
2. LoadBalancerListener
    - DefaultActions: Forwards traffic to the target group.
    - LoadBalancerArn: The ARN of the load balancer.
    - Port: Listener port (HTTP).
    - Protocol: Listener protocol (HTTP).
3. TargetGroup
    - Name: The name of the target group.
    - Port: Port for the target group.
    - Protocol: Protocol for the target group.
    - VpcId: VPC ID for the target group.
    - TargetType: Set to ip.
    - HealthCheckSettings: Settings for health checks including protocol, port, path, intervals, and thresholds.
4. ECSTaskDefinition
    - Family: The name of the task family.
    - Cpu: CPU size for the task.
    - Memory: Memory size for the task.
    - NetworkMode: Set to awsvpc.
    - RequiresCompatibilities: Requires Fargate.
    - ExecutionRoleArn: IAM execution role for the task.
    - TaskRoleArn: IAM task role.
    - EphemeralStorage: Size of ephemeral storage.
    - ContainerDefinitions: Container settings including image, essential status, port mappings, and protocol.
5. ECSService
    - Cluster: The ECS cluster.
    - DesiredCount: The desired count of tasks.
    - TaskDefinition: The task definition.
    - LaunchType: Set to FARGATE.
    - NetworkConfiguration: Network settings including subnets, security groups, and public IP assignment.
    - LoadBalancers: Load balancer settings including container name, container port, and target group ARN.
# Outputs
    - ECSServiceName: The name of the created ECS service.
    - LoadBalancerDNSName: The DNS name of the created load balancer.
# Usage
1. Parameters
    - Ensure you have the appropriate values for the parameters before deploying the stack. This includes the project name, environment, CPU, memory, container port, desired count, and ECR repository name.
2. Deploy
    - Use the AWS Management Console, AWS CLI, or an infrastructure-as-code tool (AWS CloudFormation) to deploy the CloudFormation stack.
3. Access Resources
    - After deployment, use the outputs to reference the created resources in your applications or other CloudFormation stacks.