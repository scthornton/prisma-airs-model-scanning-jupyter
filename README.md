# Prisma AIRS Model Security - Demo & Examples

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.12+](https://img.shields.io/badge/python-3.12+-blue.svg)](https://www.python.org/downloads/)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

Demonstration repository for scanning machine learning models for security vulnerabilities using Prisma AIRS Model Security.

## Overview

This repository provides working examples and a comprehensive Jupyter notebook demonstrating how to:

- Scan HuggingFace models for security threats
- Detect malicious code, backdoors, and supply chain attacks
- Integrate model scanning into CI/CD pipelines
- Analyze scan results and take action
- Configure security policies for different model sources

## Features

✅ **Interactive Jupyter Notebook** - Complete demo with step-by-step explanations
✅ **Python Examples** - Ready-to-run scripts for common tasks
✅ **Production-Ready** - CI/CD integration patterns included
✅ **Comprehensive Documentation** - Setup guides and troubleshooting
✅ **Security Best Practices** - Credential management and secure workflows

## Quick Start

### Prerequisites

- Python 3.12 or higher
- Prisma AIRS Model Security account ([sign up](https://www.paloaltonetworks.com/prisma/cloud))
- Service account credentials (Client ID, Client Secret, TSG ID)

### Installation

1. **Clone the repository:**
   ```bash
   git clone <repository-url>
   cd model-scanning
   ```

2. **Create virtual environment:**
   ```bash
   python3 -m venv .venv
   source .venv/bin/activate  # On Windows: .venv\Scripts\activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configure credentials:**

   Set environment variables (recommended):
   ```bash
   export MODEL_SECURITY_CLIENT_ID="AIRS@your-tsg-id.iam.panserviceaccount.com"
   export MODEL_SECURITY_CLIENT_SECRET="your-client-secret-uuid"
   export TSG_ID="your-tsg-id"
   ```

   Or add to `~/.bashrc` / `~/.zshrc` for persistence.

### Getting Your Credentials

1. **Log in to Strata Cloud Manager:** https://strata.paloaltonetworks.com
2. **Create Service Account:**
   - Navigate to: **Settings → Identity & Access → Service Accounts**
   - Click **Add Service Account**
   - Name: `Prisma AIRS Model Security`
   - Role: Select appropriate permissions
   - Save and copy the **Client ID** and **Client Secret** (shown once!)
3. **Get TSG ID:**
   - Navigate to: **Tenant Management**
   - Copy your **Tenant Service Group (TSG) ID**

### Run the Jupyter Notebook

```bash
# Start Jupyter
jupyter notebook

# Open: notebooks/model_security_working_demo.ipynb
# Run all cells or step through individually
```

### Run Example Scripts

```bash
# List available security groups
python examples/list_security_groups.py

# Scan HuggingFace models
python examples/scan_huggingface_model.py
```

## Repository Structure

```
model-scanning/
├── notebooks/
│   ├── model_security_working_demo.ipynb  # Main demo notebook
│   └── README.md                           # Notebook documentation
├── examples/
│   ├── list_security_groups.py             # List security groups
│   └── scan_huggingface_model.py           # Scan HuggingFace models
├── requirements.txt                        # Python dependencies
├── requirements-minimal.txt                # Minimal dependencies
├── .gitignore                              # Git ignore patterns
├── README.md                               # This file
├── SECURITY.md                             # Security policy
├── CONTRIBUTING.md                         # Contribution guidelines
├── CODE_OF_CONDUCT.md                      # Code of conduct
└── LICENSE                                 # MIT License
```

## What Gets Scanned?

Prisma AIRS Model Security detects:

### 🔴 Critical Threats
- **Malicious Code Execution** - Pickle deserialization attacks, arbitrary code execution
- **Supply Chain Attacks** - Compromised dependencies, poisoned models
- **Neural Backdoors** - Hidden triggers that cause misclassification
- **Data Exfiltration** - Models designed to leak training data

### 🟡 Policy Violations
- **Unapproved Licenses** - GPL, AGPL, or custom licenses violating policy
- **Unsafe Formats** - Pickle, Keras (H5) files that allow code execution
- **Unverified Publishers** - Models from untrusted organizations

### 🟢 Best Practices
- **Recommended Formats** - SafeTensors, ONNX, TensorFlow SavedModel
- **Approved Sources** - Verified organizations and publishers
- **Security Metadata** - Model cards, license files, provenance

## Usage Examples

### Basic Model Scan

```python
from model_security_client.api import ModelSecurityAPIClient
from uuid import UUID

# Initialize client
client = ModelSecurityAPIClient(
    base_url="https://api.sase.paloaltonetworks.com/aims"
)

# Scan a model
result = client.scan(
    security_group_uuid=UUID("your-security-group-uuid"),
    model_uri="https://huggingface.co/microsoft/DialoGPT-medium"
)

# Check result
if result.eval_outcome == "ALLOWED":
    print("✅ Model is safe to deploy")
else:
    print(f"❌ Model blocked: {result.eval_summary.rules_failed} rules failed")
```

### CI/CD Integration

```python
def validate_model_before_deployment(model_uri, security_group_uuid):
    """
    Gate deployment based on security scan results.
    Returns True if safe to deploy, False otherwise.
    """
    result = client.scan(
        security_group_uuid=security_group_uuid,
        model_uri=model_uri
    )

    is_safe = result.eval_outcome == "ALLOWED"

    if not is_safe:
        print(f"DEPLOYMENT BLOCKED: {result.eval_summary.rules_failed} security violations")
        # Fail the CI/CD pipeline
        sys.exit(1)

    return is_safe
```

### Batch Scanning

```python
models_to_scan = [
    "https://huggingface.co/openai-community/gpt2",
    "https://huggingface.co/google-bert/bert-base-uncased",
    "https://huggingface.co/microsoft/DialoGPT-medium"
]

for model_uri in models_to_scan:
    result = client.scan(
        security_group_uuid=security_group_uuid,
        model_uri=model_uri
    )
    print(f"{model_uri}: {result.eval_outcome}")
```

## Viewing Detailed Results

The SDK returns summary data. For detailed findings:

1. Go to: https://strata.paloaltonetworks.com
2. Navigate to: **Insights → Prisma AIRS → Model Security → Scans**
3. Click on your Scan ID
4. View:
   - Specific rule failures
   - Threat descriptions
   - Remediation steps
   - File-level findings

## Security

**⚠️ Important:** Never commit credentials to version control.

- Use environment variables for credentials
- Add `.env` files to `.gitignore`
- Use service accounts with minimum required permissions
- Rotate credentials regularly
- See [SECURITY.md](SECURITY.md) for detailed security guidelines

## Troubleshooting

### ValidationError: Model URI format

**Error:**
```
ValidationError: Model URI 'https://huggingface.co/gpt2' does not follow expected format
```

**Fix:** HuggingFace URLs must include author/organization:
```python
# ❌ Wrong
model_uri = "https://huggingface.co/gpt2"

# ✅ Correct
model_uri = "https://huggingface.co/openai-community/gpt2"
```

### Authentication Errors

**Error:** `401 Unauthorized` or `403 Forbidden`

**Fix:**
1. Verify credentials are set correctly
2. Check TSG_ID matches your tenant
3. Confirm service account has required permissions
4. Ensure credentials haven't expired

### Module Not Found

**Error:** `ModuleNotFoundError: No module named 'model_security_client'`

**Fix:**
```bash
# Ensure virtual environment is activated
source .venv/bin/activate

# Reinstall dependencies
pip install -r requirements.txt
```

## Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

For questions, issues, or feature requests:

- **Documentation:** https://docs.paloaltonetworks.com/prisma/prisma-cloud/prisma-cloud-admin/prisma-cloud-airs
- **GitHub Issues:** [Create an issue](<repository-url>/issues)
- **Community:** Join the Prisma Cloud community forums

## Acknowledgments

- Built with [Prisma AIRS Model Security SDK](https://pypi.org/project/model-security-client/)
- Powered by [Palo Alto Networks Prisma Cloud](https://www.paloaltonetworks.com/prisma/cloud)

---

**Note:** This is a demonstration repository. Always follow your organization's security policies when handling ML models and credentials.
