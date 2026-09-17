オリジナルのリポジトリ：https://github.com/aws-samples/sample-agentic-runtime-security-on-aws-with-vault

ハンズオン手順：https://catalog.us-east-1.prod.workshops.aws/workshops/9d6a0b3d-9ea2-47a2-8ca4-40168cadd531/en-US

## EC2で作業したい人向け用の手順

初回
```bash
ssh USER@HOST(EC2) 'bash -s' <<'EOF'
set -euo pipefail

# 自分のdoormat credentialに置き換え
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...

# プロキシ設定
export https_proxy=http://localhost:8888
export http_proxy=http://localhost:8888

ARCH=$(uname -m)
[ "$ARCH" = "aarch64" ] && DL_ARCH="arm64" || DL_ARCH="amd64"

sudo dnf update -y
sudo dnf install -y --allowerasing unzip tar jq docker

sudo systemctl enable --now docker
sudo usermod -aG docker "$(whoami)"

curl -fsSL "https://awscli.amazonaws.com/awscli-exe-linux-$([ "$ARCH" = "aarch64" ] && echo aarch64 || echo x86_64).zip" -o /tmp/awscliv2.zip
unzip -q -o /tmp/awscliv2.zip -d /tmp
sudo /tmp/aws/install --update
rm -rf /tmp/awscliv2.zip /tmp/aws

sudo dnf config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo
sudo dnf install -y terraform

KUBECTL_VERSION=$(curl -fsSL https://dl.k8s.io/release/stable.txt)
curl -fsSL "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/${DL_ARCH}/kubectl" -o /tmp/kubectl
sudo install -m 0755 /tmp/kubectl /usr/local/bin/kubectl
rm -f /tmp/kubectl

curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

aws --version
terraform -version
kubectl version --client
helm version
jq --version
docker --version
EOF
```

2回目以降
```bash
ssh USER@HOST(EC2) 'bash -s' <<'EOF'
set -euo pipefail

# 自分のdoormat credentialに置き換え
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...

# プロキシ設定
export https_proxy=http://localhost:8888
export http_proxy=http://localhost:8888
EOF
```
