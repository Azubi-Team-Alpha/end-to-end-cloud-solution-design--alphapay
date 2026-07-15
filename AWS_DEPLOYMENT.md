# AlphaPay – AWS S3 Deployment Guide

## Overview

AlphaPay is deployed as a **fully static website** on **Amazon S3**, distributed globally via **Amazon CloudFront** CDN, secured with **ACM SSL/TLS**, and updated via **GitHub Actions CI/CD**.

---

## Architecture

```
User → CloudFront (CDN + SSL) → S3 Bucket (static files)
           ↑
     GitHub Actions (CI/CD)
     builds & deploys on push to main
```

---

## Prerequisites

| Tool | Version |
|------|---------|
| AWS CLI | v2+ |
| Node.js | 20+ |
| npm | 10+ |

---

## Step 1: Create S3 Bucket

```bash
# Create bucket (replace region as needed)
aws s3api create-bucket \
  --bucket alphapay-africa-static \
  --region us-east-1

# Enable static website hosting
aws s3 website s3://alphapay-africa-static \
  --index-document index.html \
  --error-document 404.html

# Set public read policy (only needed if NOT using CloudFront OAC)
aws s3api put-bucket-policy \
  --bucket alphapay-africa-static \
  --policy '{
    "Version": "2012-10-17",
    "Statement": [{
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::alphapay-africa-static/*"
    }]
  }'
```

> **Best Practice**: Use **CloudFront + Origin Access Control (OAC)** to keep the S3 bucket private. The bucket policy should only allow CloudFront, not the public internet.

---

## Step 2: Build the Site Locally

```bash
npm install
npm run build
# Output is in dist/
```

---

## Step 3: Manual Deploy (first time)

```bash
aws s3 sync dist/ s3://alphapay-africa-static --delete
```

---

## Step 4: Setup CloudFront Distribution

1. Go to **AWS CloudFront Console** → Create Distribution
2. **Origin Domain**: Select your S3 bucket
3. **Origin Access**: Use **Origin Access Control (OAC)** ← Recommended
4. **Default Root Object**: `index.html`
5. **Custom Error Pages**:
   - 403 → `/404.html` → 404
   - 404 → `/404.html` → 404
6. **SSL Certificate**: Request via **ACM** (us-east-1 region required for CloudFront)
7. **Alternate Domain Names**: `alphapay.africa`, `www.alphapay.africa`

---

## Step 5: Setup GitHub Actions CI/CD

Add these secrets to your GitHub repository (`Settings → Secrets → Actions`):

| Secret | Value |
|--------|-------|
| `AWS_ACCESS_KEY_ID` | IAM user access key |
| `AWS_SECRET_ACCESS_KEY` | IAM user secret key |
| `CLOUDFRONT_DISTRIBUTION_ID` | Your CloudFront distribution ID |

The workflow file is at `.github/workflows/deploy-s3.yml` and runs automatically on every push to `main`.

---

## Step 6: IAM Policy (Least Privilege)

Create an IAM user with **only** these permissions:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "s3:DeleteObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::alphapay-africa-static",
        "arn:aws:s3:::alphapay-africa-static/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": ["cloudfront:CreateInvalidation"],
      "Resource": "arn:aws:cloudfront::ACCOUNT_ID:distribution/DISTRIBUTION_ID"
    }
  ]
}
```

---

## Caching Strategy

| File type | Cache-Control |
|-----------|---------------|
| `*.html`, `*.json` | `public, max-age=0, must-revalidate` |
| `_astro/*` (hashed assets) | `public, max-age=31536000, immutable` |
| Images, fonts | `public, max-age=86400` |

---

## Custom Domain Setup (Route 53)

```bash
# Create A record (alias) pointing to CloudFront
aws route53 change-resource-record-sets \
  --hosted-zone-id YOUR_ZONE_ID \
  --change-batch '{
    "Changes": [{
      "Action": "UPSERT",
      "ResourceRecordSet": {
        "Name": "alphapay.africa",
        "Type": "A",
        "AliasTarget": {
          "HostedZoneId": "Z2FDTNDATAQYW2",
          "DNSName": "YOUR_CLOUDFRONT_DOMAIN.cloudfront.net.",
          "EvaluateTargetHealth": false
        }
      }
    }]
  }'
```

---

## Verify Deployment

```bash
# Check files in S3
aws s3 ls s3://alphapay-africa-static --recursive | head -20

# Test CloudFront
curl -I https://alphapay.africa
```

---

## Cost Estimate (monthly, ~10k visitors)

| Service | Estimated Cost |
|---------|---------------|
| S3 (storage + requests) | ~$0.05 |
| CloudFront (transfer) | ~$0.50 |
| Route 53 | ~$0.50 |
| ACM SSL | Free |
| **Total** | **~$1.05/month** |

---

*Built by Team Alpha — Azubi Africa Internship Programme, Project 1: End-to-End Cloud Solution Design & Deployment*
