# Day 28 — Advanced Networking with VPCs and Peering

## 🔹 Ansible — Set Up VPC Peering Between AWS Accounts

```yaml
- name: Setup VPC Peering Between Accounts
  hosts: localhost
  tasks:
    - name: Create VPC peering connection
      command: aws ec2 create-vpc-peering-connection --vpc-id vpc-12345678 --peer-vpc-id vpc-87654321
🔹 Terraform — VPC Peering and Route Tables
resource "aws_vpc_peering_connection" "pathnex_peering" {
  vpc_id      = aws_vpc.pathnex_vpc.id
  peer_vpc_id = aws_vpc.peer_vpc.id
}

resource "aws_route" "pathnex_route" {
  route_table_id         = aws_route_table.pathnex_rt.id
  destination_cidr_block = "10.0.0.0/16"
  vpc_peering_connection_id = aws_vpc_peering_connection.pathnex_peering.id
}
🔹 Kubernetes — Multi-Cluster VPC Peering and Network Policy
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: pathnex-vpc-policy
spec:
  podSelector:
    matchLabels:
      app: pathnex-app
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: pathnex-db
