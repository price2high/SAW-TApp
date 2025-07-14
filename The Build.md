## 🧰 What You'll Build:
A simple website where users type a sentence or paragraph and it tells them if their text is Positive, Negative, or Neutral.
---
## 📌 Tools You’ll Use:
- Python – the programming language
- Flask – to create the web app
- TextBlob – to analyze the sentiment
- HTML – for the webpage
- AWS EC2 (Free Tier) – to put your website online
---

# 🖥️ Part 1: Build the Sentiment App on Your Computer

---
## ✅ Step 1: Install Python and Set Up
If you don’t have Python installed:
- Go to https://python.org/downloads
- Download the version for your system (Windows/Mac/Linux)

Create a folder for your project:

``` mkdir sentiment-app ```

``` cd sentiment-app ```

Set up your virtual environment:

``` python -m venv venv ```
``` source venv/bin/activate ```  # Windows: ``` venv\Scripts\activate ```

---
## ✅ Step 2: Install Required Libraries
Install Flask and TextBlob:

``` pip install flask textblob ```

``` python -m textblob.download_corpora ```

---

## ✅ Step 3: Create the Python File (app.py)
### Create a file named app.py:

```
from flask import Flask, render_template, request
from textblob import TextBlob


app = Flask(__name__)


@app.route('/')
def home():
    return render_template('index.html')


@app.route('/analyze', methods=['POST'])
def analyze():
    text = request.form['text']
    blob = TextBlob(text)
    polarity = blob.sentiment.polarity


    if polarity > 0:
        result = "Positive 😊"
    elif polarity < 0:
        result = "Negative 😡"
    else:
        result = "Neutral 😐"


    return render_template('result.html', text=text, result=result)


if __name__ == '__main__':
    app.run(debug=True)
```

---
## ✅ Step 4: Create the Frontend (HTML Pages)

Create a folder called templates, then create two files inside it:
### templates/index.html:

```
<!DOCTYPE html>
<html>
<head>
  <title>Sentiment Analysis</title>
</head>
<body>
  <h1>Enter your text:</h1>
  <form action="/analyze" method="POST">
    <textarea name="text" rows="5" cols="40" placeholder="Type something..."></textarea><br><br>
    <button type="submit">Analyze</button>
  </form>
</body>
</html>
```

### templates/result.html:

```
<!DOCTYPE html>
<html>
<head>
  <title>Result</title>
</head>
<body>
  <h2>Your Text:</h2>
  <p>{{ text }}</p>


  <h2>Sentiment:</h2>
  <p>{{ result }}</p>


  <a href="/">Try Again</a>
</body>
</html>
```

---
## ✅ Step 5: Run the App
In your terminal, type:
``` python app.py ```

Go to http://127.0.0.1:5000 in your web browser. 🎉 You just made your own sentiment analysis tool!

---

# ☁️ Part 2: Host Your App on AWS Free Tier (EC2)
---

## ✅ Step 6: Set Up AWS EC2
### 1. Sign up for AWS:

Go to https://aws.amazon.com/free and create an account.
- An email address and your name, 
- Use Root, for Personal Use, It will ask for billing information ( needs actual card info for verification BUT IT WILL NOT CHARGE)
  
### 2. Go to EC2 service:
   
Search for EC2 in the AWS console search bar.

### 3. Launch a new instance:
- Name: SentimentApp
- OS: Ubuntu 22.04
- Instance type: t2.micro (Free Tier)
- Key pair: Create a new one →Use the same name as app → download the .pem file
- Firewall/Security Group:
  - Allow SSH (22) from your IP
  - Allow HTTP (80) from Anywhere
- Click Launch Instance
Then follow the steps below
Click **Security Groups** > **Inbound Rules** > **Edit Inbound Rules**

 Add a rule:
 
    - Type: Custom TCP
    - Port Range: 5000
    - Source: 0.0.0.0/0 (anyone can access it — just for testing)
---
## ✅ Step 7: Copy Your App Windows Files
**In PowerShell, run:**
We were able to copy our files from our Windows computers to our EC2 Instance using this code:
``` scp -i "C:\Users\TimorraRogo\Downloads\SAW-TAPP.pem" -r  "C:\Users\TimorraRogo\Downloads\SAW-Tapp" ec2-user@3.142.74.18:/home/ec2-user/ ```

 We had to do this outside the EC2 instance.
The screenshot below confirms that the files are being copied from the Windows machine to the Ubuntu machine in EC2.  
![Copying the app to EC2](https://github.com/price2high/SAW-TApp/blob/main/Screenshot%202025-07-12%20153733.png)
