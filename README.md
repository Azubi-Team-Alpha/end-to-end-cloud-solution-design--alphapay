# AlphaPay — African Cross-Border Payments Platform

> **Azubi Africa Internship Programme** — Capstone Project 1: End-to-End Cloud Solution Design & Deployment  
> **Team:** Team Alpha | **Cohort:** 2024

---

## Overview

**AlphaPay** is a fintech platform for instant, zero-fee cross-border money transfers across Africa. Users can send money to any bank or mobile money wallet in 15+ African countries in under 2 seconds, generate virtual Visa cards for international payments, and businesses can use the full payments API for bulk payroll and merchant collections.

The platform is built as a static **Astro** site (marketing + docs) and deployed on a fully cloud-native **AWS** infrastructure provisioned with **Terraform** and automated via **GitHub Actions CI/CD**.

---

## Live Site

🌐 **[alphapay.africa](https://alphapay.africa)**

---

## Team Alpha

| Name | Role |
|---|---|
| Mustapha Haadi | Team Lead |
| David Yirenkyi | Member |
| Emmanuel Yelisomah | Member |
| Daniel Hanson Reynolds | Member |
| Zakaria Adeeba | Member |
| Evame Cobblah | Member |

---

## Tech Stack

### Frontend
- **Astro 6** — Static site generation (SSG)
- **TailwindCSS v4** — Styling with dark mode
- **Preline v4** — UI components
- **Starlight** — Developer documentation

### Cloud Infrastructure (AWS)
| Service | Purpose |
|---|---|
| **Amazon EC2** | Application hosting (Auto Scaling Group) |
| **Application Load Balancer (ALB)** | Traffic distribution & health checks |
| **Amazon CloudFront** | CDN for global low-latency delivery |
| **AWS Certificate Manager (ACM)** | SSL/TLS certificate management |
| **Amazon S3** | Static asset storage (OAC-protected) |
| **AWS IAM** | Least-privilege access control |
| **Terraform** | Infrastructure as Code (IaC) |
| **GitHub Actions** | CI/CD pipeline automation |

---

## Getting Started (Local Development)

### Prerequisites
- Node.js ≥ 18
- npm, yarn, pnpm, or bun

### Install & Run

```bash
# Clone the repository
git clone https://github.com/Mustapha-Haadi/internship-cloud-solution-design.git
cd internship-cloud-solution-design

# Install dependencies
npm install

# Start development server
npm run dev
```

The dev server runs at `http://localhost:4321`.

### Build for Production

```bash
npm run build
```

This runs `astro check` (TypeScript validation), `astro build` (static site generation), and post-processes HTML for optimisation.

---

## Project Structure

```
src/
├── pages/          # Astro pages (index, services, products, contact, docs)
├── content/        # Markdown/MDX content (products, blog, docs)
├── components/     # Reusable UI components
├── data_files/     # Site-wide data (constants, FAQs, pricing, features)
├── images/         # Local image assets
└── layouts/        # Page layout shells
```

---

## Deployment Architecture

```
User → CloudFront CDN → ALB → EC2 Instances (ASG)
                     ↓
                S3 (static assets, OAC-only access)
                ACM (SSL certificate)
                IAM (least-privilege roles)
```

All infrastructure is defined in Terraform and deployed automatically on push to `main` via GitHub Actions.

---

## Security Status

| Control | Status |
|---|---|
| HTTPS (ACM SSL) | ✅ Active |
| ALB Health Checks | ✅ Healthy |
| CloudFront CDN | ✅ Enabled |
| CI/CD Pipeline | ✅ Live |
| IAM Least Privilege | ✅ Applied |
| S3 Origin Access | ✅ OAC Only |

---

## License

MIT — see [LICENSE](./LICENSE)

---

## Acknowledgements

Built on top of the [ScrewFast](https://github.com/mearashadowfax/ScrewFast) open-source Astro template by Emil Gulamov, distributed by [ThemeWagon](https://themewagon.com). Adapted and extended by Team Alpha for the AlphaPay fintech use case.
