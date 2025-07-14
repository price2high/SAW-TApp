# The Revamp
---
We realized that it has to be more than one sentence for it to analyze the different emotions.
Then we had the idea to take this a step further. 
What if we could make an emotional tracker that stores all of your feelings with the analysis tool? It will learn what a positive, neutral or negative sentiment is to you. This tool could act as an online journal and diary from someone who likes to keep track of their feelings and events that happen to them through out their day/week..

![](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-12%20144945.png)

---
First we installed ``` pip install flask_sqlalchemy ``` in our terminals
# The updated code:

## app.py
```
from flask import Flask, render_template, request, redirect
from flask_sqlalchemy import SQLAlchemy
from textblob import TextBlob
from datetime import datetime


app = Flask(__name__)


# Set up database
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///entries.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)


# Thought model
class Thought(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    content = db.Column(db.Text, nullable=False)  # Full journal
    sentiment = db.Column(db.String(20), nullable=False)  # Overall mood
    breakdown = db.Column(db.Text, nullable=False)  # Per sentence results
    date = db.Column(db.DateTime, default=datetime.utcnow)


# Homepage - show all past entries
@app.route('/')
def home():
    entries = Thought.query.order_by(Thought.date.desc()).all()
    return render_template('index.html', entries=entries)


# Analyze and store journal entry
@app.route('/analyze', methods=['POST'])
def analyze():
    text = request.form['text']
    blob = TextBlob(text)


    sentence_results = []
    total_polarity = 0


    for sentence in blob.sentences:
        polarity = sentence.sentiment.polarity
        total_polarity += polarity


        if polarity > 0:
            sentiment = "Positive 😊"
        elif polarity < 0:
            sentiment = "Negative 😡"
        else:
            sentiment = "Neutral 😐"


        sentence_results.append(f"{sentence} → {sentiment}")


    avg_polarity = total_polarity / len(blob.sentences)


    if avg_polarity > 0:
        overall_sentiment = "Positive 😊"
    elif avg_polarity < 0:
        overall_sentiment = "Negative 😡"
    else:
        overall_sentiment = "Neutral 😐"


    # Save entry with breakdown joined as one string
    new_thought = Thought(
        content=text,
        sentiment=overall_sentiment,
        breakdown='\n'.join(sentence_results)
    )
    db.session.add(new_thought)
    db.session.commit()


    return redirect('/')


    return render_template('result.html', text=text, results=sentence_results, overall=overall_sentiment)


# Auto-create database on first run
if __name__ == '__main__':
    with app.app_context():
        db.create_all()
    app.run(debug=True)
```
---
## results.html
```
<!DOCTYPE html>
<html>
<head>
  <title>Sentiment Result</title>
</head>
<body>
  <h2>Your Original Text:</h2>
  <p>{{ text }}</p>


  <h2>Overall Sentiment:</h2>
  <p><strong>{{ overall }}</strong></p>


  <h2>Sentence-by-Sentence Analysis:</h2>
  <ul>
    {% for item in results %}
      <li>{{ item }}</li>
    {% endfor %}
  </ul>


  <a href="/">Back to tracker</a>
</body>
</html>
```
---
## index.html
to give the web tool more sstructure: 
```
<!DOCTYPE html>
<html>
<head>
  <title>Emotional Feed</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
      background: #f7f7f7;
    }
    h1, h2 {
      color: #444;
    }
    form textarea {
      width: 100%;
      height: 100px;
      font-size: 16px;
      padding: 10px;
    }
    form button {
      padding: 10px 20px;
      font-size: 16px;
      margin-top: 10px;
    }
    .entry {
      background: white;
      padding: 15px;
      border-radius: 10px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
      margin-bottom: 20px;
    }
    .entry time {
      font-size: 0.9em;
      color: #666;
    }
    .entry pre {
      background: #f0f0f0;
      padding: 10px;
      border-radius: 5px;
      overflow-x: auto;
    }
  </style>
</head>
<body>


  <h1>How Are You Feeling?</h1>


  <form method="POST" action="/analyze">
    <textarea name="text" placeholder="Write your thoughts here..."></textarea><br>
    <button type="submit">Analyze</button>
  </form>


  <h2>🧠 Your Emotional Feed</h2>


  {% for entry in entries %}
    <div class="entry">
      <time>{{ entry.date.strftime('%Y-%m-%d %I:%M %p') }}</time>
      <p><strong>Overall Sentiment:</strong> {{ entry.sentiment }}</p>
      <p><strong>Your Entry:</strong><br>{{ entry.content }}</p>
      <p><strong>Breakdown:</strong></p>
      <pre>{{ entry.breakdown }}</pre>
    </div>
  {% else %}
    <p>No entries yet.</p>
  {% endfor %}


</body>
</html>
```
After spending the day marveling at our glorious Analysis Tool, We thought “How can we take this a step further?” We decided to add/update the code to include a floating scoreboard.
---
# The Floating Scoreboard
## updated app.py code: 
```
from flask import Flask, render_template, request, redirect
from flask_sqlalchemy import SQLAlchemy
from textblob import TextBlob
from datetime import datetime

app = Flask(__name__)

# Set up database
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///entries.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)

# Thought model
class Thought(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    content = db.Column(db.Text, nullable=False)  # Full journal
    sentiment = db.Column(db.String(20), nullable=False)  # Overall mood
    breakdown = db.Column(db.Text, nullable=False)  # Per sentence results
    date = db.Column(db.DateTime, default=datetime.utcnow)

# Homepage - show all past entries
@app.route('/')
def home():
    entries = Thought.query.order_by(Thought.date.desc()).all()

    # Count sentiment types and make sure they default to 0 if None
    total_positive = Thought.query.filter(Thought.sentiment.like('%Positive%')).count() or 0
    total_negative = Thought.query.filter(Thought.sentiment.like('%Negative%')).count() or 0
    total_neutral  = Thought.query.filter(Thought.sentiment.like('%Neutral%')).count() or 0

    return render_template('index.html', 
                           entries=entries,
                           total_positive=total_positive,
                           total_negative=total_negative,
                           total_neutral=total_neutral)



# Analyze and store journal entry
@app.route('/analyze', methods=['POST'])
def analyze():
    text = request.form['text']
    blob = TextBlob(text)

    sentence_results = []
    total_polarity = 0

    for sentence in blob.sentences:
        polarity = sentence.sentiment.polarity
        total_polarity += polarity

        if polarity > 0:
            sentiment = "Positive 😊"
        elif polarity < 0:
            sentiment = "Negative 😡"
        else:
            sentiment = "Neutral 😐"

        sentence_results.append(f"{sentence} → {sentiment}")

    avg_polarity = total_polarity / len(blob.sentences)

    if avg_polarity > 0:
        overall_sentiment = "Positive 😊"
    elif avg_polarity < 0:
        overall_sentiment = "Negative 😡"
    else:
        overall_sentiment = "Neutral 😐"

    # Save entry with breakdown joined as one string
    new_thought = Thought(
        content=text,
        sentiment=overall_sentiment,
        breakdown='\n'.join(sentence_results)
    )
    db.session.add(new_thought)
    db.session.commit()

    return redirect('/')

    return render_template('result.html', text=text, results=sentence_results, overall=overall_sentiment)

# Auto-create database on first run
if __name__ == '__main__':
    with app.app_context():
        db.create_all()
    app.run(debug=True)
```
---
## updated index.html file:
```
<!DOCTYPE html>
<html>
<head>
  <title>Emotional Feed</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
      background: #f7f7f7;
    }
    h1, h2 {
      color: #444;
    }
    form textarea {
      width: 100%;
      height: 100px;
      font-size: 16px;
      padding: 10px;
    }
    form button {
      padding: 10px 20px;
      font-size: 16px;
      margin-top: 10px;
    }
    .entry {
      background: white;
      padding: 15px;
      border-radius: 10px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
      margin-bottom: 20px;
    }
    .entry time {
      font-size: 0.9em;
      color: #666;
    }
    .entry pre {
      background: #f0f0f0;
      padding: 10px;
      border-radius: 5px;
      overflow-x: auto;
    }
    .scoreboard {
      position: fixed;
      top: 20px;
      right: 20px;
      background-color: #fff;
      border: 1px solid #ddd;
      border-radius: 10px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
      padding: 15px;
      z-index: 9999;
      font-family: Arial, sans-serif;
      text-align: center;
    }
    .scoreboard h4 {
      margin-top: 0;
      margin-bottom: 10px;
    }
    .scoreboard p {
      margin: 5px 0;
    }
  </style>
</head>
<body>

  <!-- Floating Scoreboard -->
  <div class="scoreboard">
    <h4>📊 Mood Scoreboard</h4>
    <canvas id="sentimentChart" width="200" height="200"></canvas>
  </div>

  <h1>How Are You Feeling?</h1>

  <form method="POST" action="/analyze">
    <textarea name="text" placeholder="Write your thoughts here..."></textarea><br>
    <button type="submit">Analyze</button>
  </form>

  <h2>🧠 Your Emotional Feed</h2>

  {% for entry in entries %}
    <div class="entry">
      <time>{{ entry.date.strftime('%Y-%m-%d %I:%M %p') }}</time>
      <p><strong>Overall Sentiment:</strong> {{ entry.sentiment }}</p>
      <p><strong>Your Entry:</strong><br>{{ entry.content }}</p>
      <p><strong>Breakdown:</strong></p>
      <pre>{{ entry.breakdown }}</pre>
    </div>
  {% else %}
    <p>No entries yet.</p>
  {% endfor %}

  <script>
    const ctx = document.getElementById('sentimentChart').getContext('2d');
    const sentimentChart = new Chart(ctx, {
      type: 'doughnut',
      data: {
        labels: ['Positive 😊', 'Neutral 😐', 'Negative 😡'],
        datasets: [{
          label: 'Sentiments',
          data: [{{ total_positive }}, {{ total_neutral }}, {{ total_negative }}],
          backgroundColor: ['#4CAF50', '#FFC107', '#F44336'],
          borderColor: ['#388E3C', '#FFA000', '#D32F2F'],
          borderWidth: 1
        }]
      },
      options: {
        responsive: false,
        plugins: {
          legend: {
            position: 'bottom'
          }
        }
      }
    });
    </script>
</body>
</html>
```
---
# The results: 
~[](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-13%20221957.png)
