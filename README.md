# MongoDB Setup Guide for AWS EC2
A comprehensive guide to setting up a MongoDB server on AWS EC2 with security best practices.

## Prerequisites
- AWS account with EC2 access
- Basic understanding of AWS EC2 and Linux commands
- SSH client installed on your local machine

## Step 1: Launch EC2 Instance
1. Launch an EC2 instance (Ubuntu recommended)
2. Configure storage (minimum 10GB for free tier)
3. Configure security group:
   - Allow SSH (Port 22)
   - Allow MongoDB (Port 27017)
   - Restrict IP ranges for better security

## Step 2: Connect to VM
```bash
ssh -i "your-key-file.pem" ubuntu@your-ec2-public-dns
```

## Step 3: Update System Packages
```bash
sudo apt update && sudo apt upgrade -y
```

## Step 4: Import MongoDB GPG Key
```bash
curl -fsSL https://www.mongodb.org/static/pgp/server-6.0.asc | \
sudo gpg --dearmor -o /usr/share/keyrings/mongodb-archive-keyring.gpg
```

## Step 5: Add MongoDB Repository
```bash
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-archive-keyring.gpg ] https://repo.mongodb.org/apt/ubuntu $(lsb_release -cs)/mongodb-org/6.0 multiverse" | \
sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```

## Step 6: Update Package List
```bash
sudo apt update
```

## Step 7: Install MongoDB
```bash
sudo apt install -y mongodb-org
```

## Step 8: Start MongoDB Service
```bash
sudo systemctl start mongod
```

## Step 9: Configure MongoDB
Edit the MongoDB configuration file:
```bash
sudo vim /etc/mongod.conf
```

Make these changes:
```yaml
# Network Configuration
net:
  port: 27017
  bindIp: 0.0.0.0    # BE CAREFUL: This allows connections from anywhere

# Security Configuration
security:
  authorization: enabled
```

## Step 10: Create Admin User
Start the MongoDB shell:
```bash
mongosh
```

Create admin user:
```javascript
use admin
db.createUser({
  user: "admin",
  pwd: "your_secure_password",
  roles: [{ role: "root", db: "admin" }]
})
```

Exit the MongoDB shell:
```bash
exit
```

## Step 11: Restart MongoDB
```bash
sudo systemctl restart mongod
```

## Step 12: Enable MongoDB on Startup
```bash
sudo systemctl enable mongod
```

## Step 13: Verify MongoDB Status
```bash
sudo systemctl status mongod
```

## Step 14: Connect to MongoDB
Connection URI format:
```
mongodb://admin:your_secure_password@your-ec2-public-ip
```

## Security Best Practices
1. Always use strong passwords
2. Restrict security group access to specific IP ranges
3. Regularly update MongoDB and system packages
4. Enable SSL/TLS for production environments
5. Regularly backup your database
6. Monitor logs for suspicious activities

## Troubleshooting
If MongoDB fails to start:
1. Check logs: `sudo tail -f /var/log/mongodb/mongod.log`
2. Verify permissions: `sudo chown -R mongodb:mongodb /var/lib/mongodb`
3. Check configuration: `sudo cat /etc/mongod.conf`

## Additional Commands
- Stop MongoDB: `sudo systemctl stop mongod`
- Check MongoDB version: `mongod --version`
- Access MongoDB logs: `sudo tail -f /var/log/mongodb/mongod.log`

Remember to replace placeholder values like `your_secure_password` and `your-ec2-public-ip` with your actual values.
