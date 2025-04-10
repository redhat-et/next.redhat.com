# Model authenticity and transparency with Sigstore

# Setup

## Install the `model_signing` package
```bash
pip install model_signing
```

# Obtain an ML model
```bash
rm -rf granite-3.2-2b-instruct
git clone https://huggingface.co/ibm-granite/granite-3.2-2b-instruct
```

## Remove .git directory to avoid including it in the signature
```bash
rm -rf granite-3.2-2b-instruct/.git
ls -lh granite-3.2-2b-instruct/
```

## Check entire size of model
```bash
du -sh granite-3.2-2b-instruct
```

# Sign the model

This uses the Sigstore signing method by default. When prompted, follow the link and select your preferred identity provider for authentication.

```bash
model_signing sign granite-3.2-2b-instruct
```

# Verify the model

```bash
IDENTITY="foo@example.com"
IDENTITY_PROVIDER="https://github.com/login/oauth"
model_signing verify sigstore --signature model.sig --identity "${IDENTITY}" --identity_provider "${IDENTITY_PROVIDER}" granite-3.2-2b-instruct
Verification succeeded
```

# Alter the model
```bash
rm -f granite-3.2-2b-instruct/config.json
```

# Verify the altered model again
```bash
model_signing verify sigstore --signature model.sig --identity "${IDENTITY}" --identity_provider "${IDENTITY_PROVIDER}" granite-3.2-2b-instruct
Verification failed with error: Signature mismatch
```

# Inspect detached signature

The signature was saved in `model.sig` by default with the `sign` command. Let's take a look:

```bash
ls -l model.sig
cat model.sig | jq .
cat model.sig | jq .dsseEnvelope.payload -r | base64 -d | jq .
```