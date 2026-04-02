# Day 28 — Advanced Networking with VPCs and Peering

## 🔹 Ansible — Set Up VPC Peering Between AWS Accounts

```yaml
- name: Setup VPC Peering Between Accounts
  hosts: localhost
  tasks:
    - name: Create VPC peering connection
      command: aws ec2 create-vpc-peering-connection --vpc-id vpc-12345678 --peer-vpc-id vpc-87654321
🔹 Terraform — VPC Peering and Route Tables
resource "aws_vpc_peering_connection" "HireReady_peering" {
  vpc_id      = aws_vpc.HireReady_vpc.id
  peer_vpc_id = aws_vpc.peer_vpc.id
}

resource "aws_route" "HireReady_route" {
  route_table_id         = aws_route_table.HireReady_rt.id
  destination_cidr_block = "10.0.0.0/16"
  vpc_peering_connection_id = aws_vpc_peering_connection.HireReady_peering.id
}
🔹 Kubernetes — Multi-Cluster VPC Peering and Network Policy
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: HireReady-vpc-policy
spec:
  podSelector:
    matchLabels:
      app: HireReady-app
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: HireReady-db
