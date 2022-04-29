# Instalação das Dependências

# Kind

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.12.0/kind-linux-amd64
chmod +x ./kind
mkdir -p ${HOME}/bin
mv ./kind ${HOME}/bin/kind
export PATH=${PATH}:${HOME}/bin
```

## kubectl

```bash
KUBERNETES_DESIRED_VERSION='1.21' && \
KUBERNETES_VERSION="$(apt-cache madison kubeadm | grep ${KUBERNETES_DESIRED_VERSION} | head -1 | awk '{ print $3 }')" && \
KUBERNETES_IMAGE_VERSION="${KUBERNETES_VERSION%-*}" && \
clear && \
echo "" && \
echo "KUBERNETES_DESIRED_VERSION.: ${KUBERNETES_DESIRED_VERSION}" && \
echo "KUBERNETES_VERSION.........: ${KUBERNETES_VERSION}" && \
echo "KUBERNETES_IMAGE_VERSION...: ${KUBERNETES_IMAGE_VERSION}" && \
echo ""

sudo apt-get install \
  --allow-downgrades -qq \
  kubectl="${KUBERNETES_VERSION?}" && \
sudo apt-mark hold \
  kubectl
```

## Helm 3

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```
