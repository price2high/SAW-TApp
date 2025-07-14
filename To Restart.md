# 🔁 To restart your Flask app:
1. Start the EC2 instance in the AWS Console:
  - Go to EC2 > Instances
  - Select your stopped instance
  - Click Start Instance
---
## ⚠️ Important: Check your Public IP
When you restart an EC2 instance, the public IP usually changes (unless you're using an Elastic IP).
So you'll need to:
  - Go to the **AWS Console**, select your instance, and note the **new Public IPv4 address**
Use the new IP to SSH and to visit the site in your browser:
 ``` ssh -i /path/to/your-key.pem ubuntu@<new-public-ip>```
---
## 🚀 Then start your app again:

Once you're SSHed in:
```
cd ~/SAW-Tapp
source venv/bin/activate
export FLASK_APP=app.py
flask run --host=0.0.0.0 --port=5000
```
Or:
```python app.py```

(if you're using app.run(...) inside app.py)
---
## 🌐 Then in your browser:

Visit: http://<new-public-ip>:5000
---
## 🛠 Optional: Reserve a static public IP

If you don't want the IP to change every time, you can assign an Elastic IP in AWS and attach it to your instance. That way, your site will always use the same IP.

