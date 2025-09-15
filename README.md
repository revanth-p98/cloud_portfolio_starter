# Cloud Portfolio Starter (S3 + optional CloudFront)

This repo helps you deploy a simple static website to **AWS S3** (and optionally put it behind **CloudFront**).
It also includes **GitHub Actions** for CI/CD: every push to `main` will upload your site to S3 and (optionally) invalidate CloudFront.

---

## 0) Prereqs (one-time)
- GitHub account + a repo (name it anything, e.g. `cloud-portfolio-starter`).
- AWS account (Free Tier is fine).
- Install: Git, AWS CLI (configured with an **IAM user**), Node.js (optional, for local dev servers).
- Create an S3 bucket in your preferred region (example: `us-east-1`). Bucket name must be globally unique (e.g., `rp-portfolio-12345`).

### S3 quick setup (simple/public for learning)
1. Create bucket → **Block Public Access OFF** (for this learning setup).
2. Enable **Static website hosting** → Index document: `index.html`, Error document: `404.html` (optional).
3. Upload `index.html`, `style.css` (and assets).
4. Add a **bucket policy** that allows public read (console has a sample; only for learning).

> ⚠️ For production, prefer **CloudFront with OAC** and keep the bucket private. That path is more secure.

---

## 1) Configure GitHub Secrets
In your GitHub repo, go to **Settings → Secrets and variables → Actions → New repository secret** and add:

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_REGION` (e.g., `us-east-1`)
- `S3_BUCKET` (your bucket name)
- (Optional) `CLOUDFRONT_DIST_ID` (if you create a CloudFront distribution)

---

## 2) CI/CD
- On push to `main`, the workflow at `.github/workflows/deploy.yml` will:
  - Configure AWS credentials
  - Sync your repo files (except what's ignored) to S3
  - If `CLOUDFRONT_DIST_ID` is present, create a cache invalidation so changes go live fast

---

## 3) Local development
- Edit `index.html` and `style.css` and commit/push.
- Optional: `npx serve` (or any simple static server) to preview locally.

---

## 4) Optional: CloudFront (HTTPS + CDN)
- Create a **CloudFront distribution** with origin = your S3 bucket (or the S3 static website endpoint).
- If using the **OAC (Origin Access Control)** approach (recommended):
  - Keep bucket **private**, attach OAC to origin, and let CloudFront update the bucket policy.
- Add `CLOUDFRONT_DIST_ID` as a GitHub secret to enable automatic invalidations.
- For a **custom domain**, use Route 53 + ACM certificate in `us-east-1` and attach to CloudFront.

---

## 5) Cleanup (to avoid charges)
- If you used only S3 static hosting, charges are usually negligible in Free Tier, but delete the bucket when done.
- If you created CloudFront, delete the distribution too (it takes time to disable and delete).

---

## 6) Commands (quick reference)

```bash
# Clone and push
git clone https://github.com/your-username/cloud-portfolio-starter.git
cd cloud-portfolio-starter
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/your-username/cloud-portfolio-starter.git
git push -u origin main
```

Happy shipping! 🚀
