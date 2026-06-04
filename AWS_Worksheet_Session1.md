# AWS WORKSHEET: SESSION 1
## Cloud Server + Static Website

---

## Before You Start
- [ ] AWS Console open: `console.aws.amazon.com`
- [ ] Region: **N. Virginia (us-east-1)** ← top-right corner

---

## STEP 1: Go to EC2
1. Click **Search bar** → Type `EC2` → Click **EC2**

---

## STEP 2: Launch Instance
Click orange **Launch Instance** button.

### 2a. Name
| Field | Enter |
|-------|-------|
| Name | `my-web-server` |

### 2b. AMI (Operating System)
| Field | Select |
|-------|--------|
| AMI | **Amazon Linux 2023** |

### 2c. Instance Type
| Field | Select |
|-------|--------|
| Instance type | `t2.micro` ← Must say "Free tier eligible" |

### 2d. Key Pair
1. Click **Create new key pair**
2. Name: `my-aws-key`
3. Type: **RSA**
4. Format: `.pem`
5. Click **Create key pair**

⚠️ **Save the downloaded file! You cannot download it again!**

### 2e. Security Group
Click **Edit** next to Network Settings:

| Field | Value |
|-------|-------|
| Security group name | `web-server-sg` |
| Description | `Allow HTTP and SSH` |

**Inbound Rules:**
| Type | Port | Source |
|------|------|--------|
| SSH | 22 | **My IP** |
| HTTP | 80 | **Anywhere (0.0.0.0/0)** |

### 2f. Storage
Keep default: **8 GB**

### 2g. Launch
1. Click **Launch Instance**
2. Click **View all instances**
3. Wait for State = **Running** (1-2 min)

---

## STEP 3: Connect to Server
1. Check box next to your instance
2. Click **Connect** (top)
3. Select **EC2 Instance Connect** tab
4. Click **Connect**

✅ Terminal opens in browser!

---

## STEP 4: Install Web Server
Type each command, press Enter:

```bash
sudo yum update -y
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd
echo "<h1>Hello from AWS! - YOUR_NAME</h1>" | sudo tee /var/www/html/index.html
```

---

## STEP 5: View Your Website
1. Go back to EC2 Console
2. Click your instance
3. Copy **Public IPv4 address**
4. Open new browser tab → Paste IP

🎉 **You should see your webpage!**

---

# 🚀 PART B: S3 Static Website (10 min)

## STEP 6: Create Website Bucket
1. Search → **S3**
2. Click **Create bucket**

| Field | Enter |
|-------|-------|
| Bucket name | `my-website-yourname-12345` (must be globally unique) |
| Region | US East (N. Virginia) |

3. **UNCHECK** "Block all public access"
4. ✅ Check "I acknowledge..." warning
5. Click **Create bucket**

## STEP 7: Enable Website Hosting
1. Click your bucket name
2. Go to **Properties** tab
3. Scroll to **Static website hosting** → Click **Edit**

| Setting | Value |
|---------|-------|
| Static website hosting | **Enable** |
| Index document | `index.html` |

4. Click **Save changes**
5. 📋 **Copy the "Bucket website endpoint"** (you'll need this!)

## STEP 8: Upload Website
1. Go to **Objects** tab
2. Click **Upload** → **Add files**
3. Create a file called `index.html` on your computer with:

```html
<!DOCTYPE html>
<html>
<head><title>My Cloud Website</title></head>
<body style="font-family:Arial; text-align:center; padding:50px;">
  <h1>🚀 Hello from the Cloud!</h1>
  <p>Created by: YOUR_NAME</p>
  <p>This website can handle MILLIONS of visitors!</p>
  <p>Cost: ~$0.02 per month</p>
</body>
</html>
```

4. Upload the file

## STEP 9: Make It Public (Bucket Policy)
1. Go to your bucket → **Permissions** tab
2. Scroll to **Bucket policy** → Click **Edit**
3. Paste this policy (replace `YOUR-BUCKET-NAME`):

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
        }
    ]
}
```

4. Click **Save changes**

## STEP 10: View Your Global Website! 🌍
1. Open the **Bucket website endpoint** URL you copied
2. Or find it: Properties → Static website hosting → Endpoint

🎉 **Your website is LIVE on the internet!**

```
╔════════════════════════════════════════════════════════════╗
║                     🤯 MIND BLOWN?                         ║
╠════════════════════════════════════════════════════════════╣
║                                                            ║
║  What you just did:                                        ║
║  • Deployed a website accessible from ANYWHERE             ║
║  • No server to manage (ever!)                             ║
║  • Auto-scales to millions of visitors                     ║
║  • Cost: ~$0.02/month for small sites                      ║
║  • 99.999999999% durability (11 nines!)                    ║
║                                                            ║
║  This is how Netflix serves images, Airbnb hosts assets,   ║
║  and startups deploy landing pages!                        ║
║                                                            ║
╚════════════════════════════════════════════════════════════╝
```

---

## CLEANUP (Important!)

**EC2 Instance:**
1. EC2 → Instances
2. Select your instance
3. **Instance state** → **Terminate instance**

**S3 Website Bucket:**
1. S3 → Select bucket
2. Click **Empty** → Confirm
3. Click **Delete** → Confirm

---

## Checklist
- [ ] Launched EC2 (t2.micro)
- [ ] Created key pair
- [ ] Configured security group
- [ ] Connected via Instance Connect
- [ ] Installed Apache
- [ ] Viewed EC2 website
- [ ] **🚀 Created S3 static website**
- [ ] **🚀 Accessed website from URL**
- [ ] Terminated EC2 instance
- [ ] Deleted S3 bucket

---

## Troubleshooting
| Problem | Fix |
|---------|-----|
| Can't connect to EC2 | Check SSH rule has "My IP" |
| EC2 website not loading | Check HTTP rule has "Anywhere" |
| Instance stuck pending | Wait 2-3 min, refresh |
| S3 website Access Denied | Check you unchecked "Block public access" |
| S3 website 403 error | Check bucket policy is applied correctly |

**Stuck? Raise your hand!** 🙋

---
*Session 1 Worksheet | Cloud Computing Workshop*
