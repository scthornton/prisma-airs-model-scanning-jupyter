# Prisma AIRS Installation Guide

## Current Issue

The `requirements.txt` file includes proprietary packages that aren't available on public PyPI:

- `airs-schemas==0.1.1` - Proprietary Palo Alto Networks package
- `model-security-client==0.1.1` - Prisma AIRS SDK (proprietary)

## Your Options

### Option 1: Get Prisma AIRS Access (Full Functionality)

**If you need the actual Prisma AIRS SDK:**

1. **Request access** from Palo Alto Networks:
   - Visit: https://www.paloaltonetworks.com/prisma/prisma-ai-runtime-security#demo
   - Request a demo account
   - OR login to existing account: https://stratacloudmanager.paloaltonetworks.com

2. **Receive SDK installation instructions** - They will provide:
   - Private PyPI repository URL, or
   - Wheel files (.whl) to install directly, or
   - Alternative installation method

3. **Configure credentials:**
   ```bash
   export MODEL_SECURITY_CLIENT_ID="AIRS@your-tsg-id.iam.panserviceaccount.com"
   export MODEL_SECURITY_CLIENT_SECRET="your-client-secret-uuid"
   export TSG_ID="your-tsg-id"
   ```

### Option 2: Work Without Prisma AIRS SDK (Current Setup)

**What's installed:**
- ✅ Jupyter Notebook environment
- ✅ Data analysis libraries (pandas, numpy, matplotlib, seaborn)
- ✅ IPython kernel for running notebooks

**What's NOT available:**
- ❌ Prisma AIRS SDK (`model-security-client`)
- ❌ Prisma AIRS schemas (`airs-schemas`)

**What you CAN do:**
- View and explore the notebook structure
- Modify the notebook to use open-source alternatives
- Learn about the Prisma AIRS API structure
- Create mock implementations for testing

**What you CANNOT do:**
- Actually scan models using Prisma AIRS
- Connect to Palo Alto Networks services
- Run the demo as-is without modifications

### Option 3: Use Open Source Model Scanning

**Python Version Compatibility Issue:**
You're running Python 3.13.5, but `modelscan` (the open-source alternative) requires Python < 3.13.

**Solutions:**

**A. Create Python 3.12 environment:**
```bash
# Install Python 3.12 via pyenv or your package manager
# Then create a virtual environment
python3.12 -m venv .venv-py312
source .venv-py312/bin/activate
pip install modelscan modelscan-pai jupyter notebook pandas matplotlib seaborn
```

**B. Use Docker:**
```bash
# Create Dockerfile with Python 3.12
docker run -it --rm -v $(pwd):/work -w /work python:3.12 bash
pip install modelscan jupyter notebook
```

## Current Installation Status

✅ **Installed successfully:**
```
jupyter==1.1.1
notebook==7.4.7
ipykernel==7.0.1
pandas (latest)
matplotlib (latest)
seaborn==0.13.2
numpy (latest)
```

## Quick Start (Current Setup)

**Launch Jupyter Notebook:**
```bash
jupyter notebook
```

**Open the demo notebook:**
```
notebooks/prisma-airs-interactive-model-security.ipynb
```

**Note:** Cells that use `model-security-client` will fail until you:
1. Get Prisma AIRS access, OR
2. Modify the notebook to use open-source alternatives, OR
3. Comment out those cells and use it as reference documentation

## Alternative: Open Source Model Scanning

If you want to scan models without Prisma AIRS, use `modelscan`:

```python
from modelscan.modelscan import ModelScan

# Scan a HuggingFace model
ms = ModelScan()
results = ms.scan("path/to/model.pkl")

if results["issues"]:
    print(f"Found {len(results['issues'])} security issues")
else:
    print("Model is clean")
```

**Note:** Requires Python 3.10-3.12, not 3.13

## Recommendations

**For Learning/Demo Purposes:**
- Current setup is fine
- View the notebook structure
- Read the documentation
- Understand the API patterns

**For Production Use:**
- Get Prisma AIRS access from Palo Alto Networks
- Follow their installation instructions
- Configure credentials properly
- Use Python 3.12 (the version specified in README)

## Next Steps

1. **Decide which path:** Prisma AIRS access vs. open-source alternatives
2. **Launch Jupyter:** `jupyter notebook` (already works with current setup)
3. **Explore notebooks:** View structure even without SDK
4. **Request access:** If you need Prisma AIRS functionality

## Support

- **Prisma AIRS Documentation:** https://docs.paloaltonetworks.com/prisma/prisma-cloud
- **Request Demo:** https://www.paloaltonetworks.com/prisma/prisma-ai-runtime-security#demo
- **Open Source Alternative:** https://github.com/protectai/modelscan
