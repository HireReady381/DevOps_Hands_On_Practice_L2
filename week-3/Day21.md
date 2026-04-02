Day 21 — Disaster Recovery and Backup Strategies
📄 week4/Day21.md
# Day 21 — Disaster Recovery and Backup Strategies

## 🔹 Ansible — Backup and Restore Files

```yaml
- name: Backup and Restore Files
  hosts: all
  become: yes
  tasks:
    - name: Create backup of /etc directory
      command: tar -czvf /tmp/etc-backup.tar.gz /etc
    - name: Copy backup to remote server
      copy:
        src: /tmp/etc-backup.tar.gz
        dest: /tmp/etc-backup.tar.gz
    - name: Restore backup from remote server
      command: tar -xzvf /tmp/etc-backup.tar.gz -C /
🔹 Terraform — Create S3 Bucket with Cross-Region Replication
resource "aws_s3_bucket" "HireReady_bucket" {
  bucket = "HireReady-backup-bucket"
  region = "us-east-1"

  versioning {
    enabled = true
  }
}

resource "aws_s3_bucket" "HireReady_backup_bucket_eu" {
  bucket = "HireReady-backup-bucket-eu"
  region = "eu-west-1"

  versioning {
    enabled = true
  }
}

resource "aws_s3_bucket_object" "HireReady_backup_file" {
  bucket = aws_s3_bucket.HireReady_bucket.bucket
  key    = "backup.tar.gz"
  source = "HireReady-backup.tar.gz"
}
🔹 Kubernetes — Disaster Recovery with Persistent Volumes
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: HireReady-statefulset
spec:
  serviceName: "HireReady"
  replicas: 3
  selector:
    matchLabels:
      app: HireReady
  template:
    metadata:
      labels:
        app: HireReady
    spec:
      containers:
        - name: nginx
          image: nginx
          volumeMounts:
            - mountPath: /usr/share/nginx/html
              name: web-content
  volumeClaimTemplates:
  - metadata:
      name: web-content
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 5Gi
🔹 Jenkinsfile — Backup Strategy for Jenkins Workspace
pipeline {
    agent any
    stages {
        stage('Backup') {
            steps {
                echo 'Backing up Jenkins workspace...'
                sh 'tar -czvf /tmp/jenkins-backup.tar.gz ${WORKSPACE}'
            }
        }
        stage('Restore') {
            steps {
                echo 'Restoring Jenkins workspace...'
                sh 'tar -xzvf /tmp/jenkins-backup.tar.gz -C ${WORKSPACE}'
            }
        }
    }
}
🔹 GitLab CI/CD — Backup and Restore Deployment Files
stages:
  - backup
  - restore

backup:
  stage: backup
  script:
    - tar -czvf /tmp/backup.tar.gz kubernetes/deployment.yaml

restore:
  stage: restore
  script:
    - tar -xzvf /tmp/backup.tar.gz -C kubernetes/

