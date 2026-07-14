# MyCloudApp — Team Alpha
**Azubi Africa Internship | Project 1: End-to-End Cloud Solution Design & Deployment**

---

## What This Is
A simple, responsive web application deployed on AWS demonstrating:
- EC2 hosting behind an Application Load Balancer
- HTTPS via ACM SSL Certificate
- Static asset delivery via Amazon S3
- Global CDN distribution via Amazon CloudFront
- CI/CD pipeline via GitHub Actions

---

## Files
| File | Purpose |
|---|---|
| `index.html` | Main application page |
| `style.css` | All styles |
| `app.js` | Navigation and interactions |
| `health.html` | ALB health check endpoint |

---

## How to Deploy on EC2

SSH into your EC2 instance:
```bash
ssh -i internship-capstone-key.pem ec2-user@YOUR-EC2-PUBLIC-IP
```

Install and start Apache:
```bash
sudo yum update -y
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd
```

Copy files to the web root:
```bash
sudo cp index.html style.css app.js health.html /var/www/html/
```

Confirm the app is live:
```
http://YOUR-EC2-PUBLIC-IP
```

---

## Team
| Name | Role |
|---|---|
| Mustapha Haadi | Team Lead |
| David Yirenkyi | Member |
| Emmanuel Yelisomah | Member |
| Daniel Hanson Reynolds | Member |
| Zakaria Adeeba | Member |
| Evame Cobblah | Member |

---

## AWS Architecture
```
Users → CloudFront CDN → ALB → EC2 (Apache + HTML/CSS/JS)
                                  ↓
                              S3 (static assets)
```
