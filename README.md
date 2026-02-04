# Docker GPU Nvidia Test

Docker setup to test and validate Nvidia GPU access within containers.

## Description

Test environment to verify that Docker can properly access Nvidia GPUs using the Nvidia Container Toolkit.

## Prerequisites

- Docker
- Docker Compose
- Nvidia GPU
- Nvidia drivers installed on host
- [Nvidia Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)

## Installation

### 1. Install Nvidia Container Toolkit

```bash
# Ubuntu/Debian
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
    sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
sudo systemctl restart docker
```

### 2. Test GPU Access

```bash
docker-compose up
```

## Usage

### Quick Test

```bash
# Run nvidia-smi in container
docker run --rm --gpus all nvidia/cuda:12.3.1-base-ubuntu20.04 nvidia-smi
```

### Verify GPU Allocation

```bash
# Check GPU is visible
docker-compose run test nvidia-smi
```

Expected output:
```
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 525.xx.xx    Driver Version: 525.xx.xx    CUDA Version: 12.3     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
|...
```

## Configuration

### docker-compose.yml Example

```yaml
services:
  test:
    image: nvidia/cuda:12.3.1-base-ubuntu20.04
    command: nvidia-smi
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
```

### Specific GPU Selection

```yaml
# Use only GPU 0
devices:
  - driver: nvidia
    device_ids: ['0']
    capabilities: [gpu]
```

## Tests Included

1. **nvidia-smi** - GPU detection
2. **CUDA samples** - Compute capability test
3. **Memory test** - GPU memory allocation
4. **Compute test** - Simple GPU calculation

## Troubleshooting

### GPU Not Detected

```bash
# Verify host can see GPU
nvidia-smi

# Check docker runtime
docker info | grep -i runtime

# Restart docker daemon
sudo systemctl restart docker
```

### Permission Denied

```bash
# Add user to docker group
sudo usermod -aG docker $USER
newgrp docker
```

### Driver Version Mismatch

Ensure CUDA version in container is compatible with host driver:
- Check host driver: `nvidia-smi`
- Use compatible CUDA image

## Common Use Cases

- Validate GPU passthrough
- Test CUDA applications
- Benchmark GPU performance
- Debug GPU access issues
- CI/CD GPU testing

## Resources

- [Nvidia Container Toolkit](https://github.com/NVIDIA/nvidia-docker)
- [CUDA Docker Images](https://hub.docker.com/r/nvidia/cuda)
- [GPU Support in Docker](https://docs.docker.com/config/containers/resource_constraints/#gpu)

## License

Personal project - Test use
