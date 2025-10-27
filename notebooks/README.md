# Prisma AIRS Model Security - Working Demo Notebook

## Overview

This directory contains a fully functional Jupyter notebook demonstrating Prisma AIRS Model Security scanning capabilities.

## Files

- **`model_security_working_demo.ipynb`** - Complete working demo with all correct URLs and educational explanations

## What's Included

The notebook demonstrates:

1. ✅ **Setup & Authentication** - Configure credentials and initialize SDK
2. ✅ **First Model Scan** - Scan Microsoft DialoGPT model
3. ✅ **Multiple Model Testing** - Batch scan 3 different models
4. ✅ **Scan History & Analytics** - View past scans with visualizations
5. ✅ **Detailed Scan Information** - Retrieve comprehensive scan details
6. ✅ **Advanced Features** - File filtering and custom timeouts
7. ✅ **Production Integration** - CI/CD validation function example
8. ✅ **Export Results** - Save scan data to CSV/JSON

## Prerequisites

### 1. Virtual Environment

```bash
cd /path/to/model-scanning
source .venv/bin/activate
```

### 2. Credentials

Set these in `~/.zshrc` or directly in the notebook:

```bash
export MODEL_SECURITY_CLIENT_ID="AIRS@your-tsg-id.iam.panserviceaccount.com"
export MODEL_SECURITY_CLIENT_SECRET="your-client-secret-uuid"
export TSG_ID="your-tsg-id"
```

### 3. Security Group UUID

Get your security group UUID from:
- https://strata.paloaltonetworks.com
- Navigate to: **Insights → Prisma AIRS → Model Security → Security Groups**
- Copy the UUID and paste into the notebook (cell 4)

## Running the Notebook

1. **Start Jupyter:**
   ```bash
   jupyter notebook
   ```

2. **Open:** `model_security_working_demo.ipynb`

3. **Run All Cells:**
   - Kernel → Restart & Run All
   - Or run cells individually (Shift+Enter)

## Key Features

### All URLs Fixed ✅

All HuggingFace model URLs include the required author/organization:

| Model | Correct URL |
|-------|-------------|
| GPT-2 | `https://huggingface.co/openai-community/gpt2` |
| BERT Base | `https://huggingface.co/google-bert/bert-base-uncased` |
| DialoGPT | `https://huggingface.co/microsoft/DialoGPT-medium` |
| Test Model | `https://huggingface.co/ykilcher/totally-harmless-model` |

### Clean Execution

The notebook should execute from start to finish without errors (assuming valid credentials).

## Troubleshooting

### ValidationError on URLs

If you get: `Model URI does not follow expected HuggingFace URI format`

**Fix:** Ensure all URLs include author/organization:
- ❌ `https://huggingface.co/gpt2`
- ✅ `https://huggingface.co/openai-community/gpt2`

### Authentication Errors

If you get: `401 Unauthorized` or `403 Forbidden`

**Fix:**
1. Verify credentials are set correctly
2. Check TSG_ID matches your tenant
3. Confirm service account has proper permissions

### Import Errors

If you get: `ModuleNotFoundError: No module named 'model_security_client'`

**Fix:**
```bash
# Activate virtual environment
source /path/to/model-scanning/.venv/bin/activate

# Verify installation
pip list | grep model-security-client

# Reinstall if needed
pip install model-security-client
```

## View Detailed Scan Results

The SDK returns summary data only. For detailed violation information:

1. Go to: https://strata.paloaltonetworks.com
2. Navigate to: **Insights → Prisma AIRS → Model Security → Scans**
3. Click on your Scan ID (displayed in notebook output)
4. View:
   - Specific rule failures
   - Threat descriptions
   - Remediation steps
   - File-level findings

## Next Steps

After running this demo:

1. **Review scan details** in Strata Cloud Manager
2. **Adjust security group rules** for your requirements
3. **Scan your own models** (local files, S3, Azure)
4. **Integrate into CI/CD** using the validation function in Section 7
5. **Explore Red Teaming** for LLM security testing

## Questions?

See main documentation:
- `../README.md` - Project overview
- `../RESULTS.md` - Scan results analysis

---

**Status:** ✅ Fully functional and tested
**Last Updated:** 2025-10-24
