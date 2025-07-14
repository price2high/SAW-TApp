# The First Draft of the Program
![](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-12%20132007.png)

We figured out that the program based the analysis on the adjective. 

![](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-12%20133541.png)

We also found out that if you enter a long statement it will only analyze the first adjective it came across. 
Limitations within the app. It looks for the first adjective and bases the sentiment on that to decide if it's a positive or negative statement.
![](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-14%20115845.png)

After realizing how the tool only processes one emotion. We edited the code so that it can process more than one emotion in a statement. 

---
### The updated app.py file
(to allow it to take in more than one statement and analysis more than one sentiment) :

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

    sentence_results = []

    for sentence in blob.sentences:
        polarity = sentence.sentiment.polarity

        if polarity > 0:
            sentiment = "Positive 😊"
        elif polarity < 0:
            sentiment = "Negative 😡"
        else:
            sentiment = "Neutral 😐"


        sentence_results.append(f"“{sentence}” → {sentiment}")

    return render_template('result.html', text=text, results=sentence_results)
if __name__ == '__main__':
    app.run(debug=True)
```

---

### The updated result.html file 
```
<!DOCTYPE html>
<html>
<head>
  <title>Sentiment Result</title>
</head>
<body>
  <h2>Your Original Text:</h2>
  <p>{{ text }}</p>


  <h2>Sentiment Analysis:</h2>
  <ul>
    {% for item in results %}
      <li>{{ item }}</li>
    {% endfor %}
  </ul>


  <a href="/">Try another</a>
</body>
</html>
```
---
# The updated results
![](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-12%20140006.png)
