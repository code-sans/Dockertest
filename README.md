# Dockertest

A comprehensive Docker testing framework for validating containerized applications and Docker environments.

## Overview

Dockertest is a testing toolkit designed to help developers and DevOps engineers validate Docker containers, images, and multi-container applications. It provides utilities for testing container behavior, networking, resource usage, and deployment scenarios.

## Features

- 🐳 **Container Testing**: Validate container startup, health checks, and runtime behavior
- 🔗 **Network Testing**: Test container networking, port mappings, and service discovery
- 📊 **Resource Monitoring**: Monitor CPU, memory, and disk usage during tests
- 🚀 **Multi-Container Support**: Test complex applications with multiple containers
- 🔄 **CI/CD Integration**: Easy integration with popular CI/CD pipelines
- 📝 **Detailed Reporting**: Comprehensive test reports and logs

## Quick Start

### Prerequisites

- Docker Engine 20.10+ 
- Docker Compose 2.0+
- Python 3.8+ (for test scripts)
- Make (optional, for convenience commands)

### Installation

1. Clone the repository:
```bash
git clone https://github.com/code-sans/Dockertest.git
cd Dockertest
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. Verify Docker installation:
```bash
docker --version
docker-compose --version
```

## Usage

### Basic Container Testing

```bash
# Test a single container
python -m dockertest test --image nginx:latest --port 80

# Test with custom configuration
python -m dockertest test --config tests/nginx-config.yaml
```

### Multi-Container Testing

```bash
# Test using docker-compose
python -m dockertest compose --file docker-compose.test.yml

# Run specific test suite
python -m dockertest suite --name integration-tests
```

### Network Testing

```bash
# Test container networking
python -m dockertest network --container web --target db:5432

# Test load balancing
python -m dockertest loadbalance --service web --replicas 3
```

## Test Configuration

Create test configuration files in YAML format:

```yaml
# tests/example-test.yaml
test:
  name: "Web Service Test"
  container:
    image: "nginx:latest"
    ports:
      - "8080:80"
    environment:
      - "ENV=test"
  
  checks:
    - type: "http"
      url: "http://localhost:8080"
      expected_status: 200
    
    - type: "resource"
      max_memory: "128MB"
      max_cpu: "50%"
    
    - type: "logs"
      contains: "server started"
```

## Available Commands

| Command | Description | Example |
|---------|-------------|---------|
| `test` | Run single container tests | `dockertest test --image alpine` |
| `compose` | Test multi-container setups | `dockertest compose --file app.yml` |
| `network` | Test networking scenarios | `dockertest network --ping db` |
| `performance` | Run performance tests | `dockertest performance --duration 5m` |
| `security` | Security vulnerability scans | `dockertest security --scan-image` |
| `cleanup` | Clean up test resources | `dockertest cleanup --all` |

## Test Examples

### Example 1: Web Application Test

```bash
# Start test environment
docker-compose -f tests/webapp/docker-compose.test.yml up -d

# Run tests
python -m dockertest test \
  --target http://localhost:3000 \
  --health-check /health \
  --timeout 30s

# Cleanup
docker-compose -f tests/webapp/docker-compose.test.yml down
```

### Example 2: Database Connection Test

```bash
# Test database connectivity
python -m dockertest database \
  --image postgres:13 \
  --env POSTGRES_PASSWORD=testpass \
  --test-connection \
  --port 5432
```

### Example 3: Load Testing

```bash
# Run load tests
python -m dockertest load \
  --target http://localhost:8080 \
  --users 100 \
  --duration 60s \
  --ramp-up 10s
```

## Directory Structure

```
Dockertest/
├── README.md                 # This file
├── requirements.txt          # Python dependencies
├── setup.py                 # Package setup
├── dockertest/              # Main package
│   ├── __init__.py
│   ├── core/               # Core testing modules
│   ├── utils/              # Utility functions
│   └── tests/              # Test implementations
├── tests/                   # Test configurations and examples
│   ├── examples/           # Example test cases
│   ├── fixtures/           # Test fixtures
│   └── configs/            # Test configurations
├── docs/                    # Documentation
└── scripts/                # Helper scripts
```

## CI/CD Integration

### GitHub Actions

```yaml
name: Docker Tests
on: [push, pull_request]

jobs:
  docker-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.9'
      
      - name: Install dependencies
        run: |
          pip install -r requirements.txt
      
      - name: Run Docker tests
        run: |
          python -m dockertest suite --name ci-tests
```

### Jenkins Pipeline

```groovy
pipeline {
    agent any
    stages {
        stage('Docker Tests') {
            steps {
                sh 'python -m dockertest test --config tests/jenkins.yaml'
            }
        }
    }
    post {
        always {
            sh 'dockertest cleanup --all'
        }
    }
}
```

## Configuration Options

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `DOCKERTEST_TIMEOUT` | Default test timeout | `30s` |
| `DOCKERTEST_LOG_LEVEL` | Logging level | `INFO` |
| `DOCKERTEST_PARALLEL` | Run tests in parallel | `false` |
| `DOCKERTEST_CLEANUP` | Auto cleanup after tests | `true` |

### Global Configuration

Create a `.dockertest.yaml` file in your project root:

```yaml
global:
  timeout: 60s
  parallel: true
  cleanup: true
  log_level: DEBUG
  
networks:
  default: dockertest-network
  
reporting:
  format: json
  output: test-results.json
```

## Troubleshooting

### Common Issues

**1. Container fails to start**
```bash
# Check container logs
docker logs <container_id>

# Verify image exists
docker images | grep <image_name>
```

**2. Port binding conflicts**
```bash
# Check port usage
netstat -tulpn | grep <port>

# Use different ports in test config
```

**3. Network connectivity issues**
```bash
# Test container networking
docker network ls
docker network inspect <network_name>
```

**4. Permission denied errors**
```bash
# Ensure Docker daemon is running
sudo systemctl status docker

# Add user to docker group
sudo usermod -aG docker $USER
```

### Debug Mode

Enable debug mode for detailed logging:

```bash
export DOCKERTEST_LOG_LEVEL=DEBUG
python -m dockertest test --debug --verbose
```

## Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details.

### Development Setup

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Install development dependencies: `pip install -r requirements-dev.txt`
4. Run tests: `python -m pytest`
5. Commit changes: `git commit -m 'Add amazing feature'`
6. Push to branch: `git push origin feature/amazing-feature`
7. Submit a Pull Request

### Running Tests

```bash
# Run all tests
python -m pytest

# Run with coverage
python -m pytest --cov=dockertest

# Run specific test category
python -m pytest tests/unit/
python -m pytest tests/integration/
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

- 📧 **Email**: support@dockertest.io
- 💬 **Discord**: [Join our community](https://discord.gg/dockertest)
- 🐛 **Issues**: [GitHub Issues](https://github.com/code-sans/Dockertest/issues)
- 📖 **Documentation**: [Full Documentation](https://docs.dockertest.io)

## Acknowledgments

- Docker team for the excellent containerization platform
- Contributors and community members
- Open source projects that inspired this tool

---

**Happy Testing! 🐳**