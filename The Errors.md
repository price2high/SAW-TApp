# The Errors
---
As you can imagine this project required alot of troubleshooting. This was something new for alot of us and we were running the programs on different computers. This page shows screenshots of the errors wer received and how we troubleshooted them. 

---


While doing the venv/Scripts/activating one error showed up (**PowerShell is blocking script** due to a security policy.)
![error with venv](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-14%20110415.png)

These commands were run on PS with the help of AI and errors were solved (**successfully changed the PowerShell execution policy** from Restricted to RemoteSigned)
![alt text](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-14%20110439.png)

---

In Step 5: Run the App 
When the web page was opened, an error occurred (The browser tried to reach a service running on **localhost** (own machine), but no server was listening on that port.)
![Alt text](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-14%20110458.png)

It may have been because the files were not saved. We went and saved each file Ctrl + S. We went to app.py and ran the command 
![](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-14%20110518.png)

Kayla- testing it out, i got the page, but not an answer

It takes it to an error page 
![](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-14%20110533.png)

Error: Ran python app.py, but nothing is running or listening — there’s no Flask server output in the terminal, which means the app didn’t start
![](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-14%20110547.png)

Error: The Flask cannot find the result.html file in the correct location.
![](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-14%20110609.png)

Flask app is working correctly. Everything is functioning as expected in this screenshot.
![](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-14%20110623.png)

The sentiment app now analyzes multiple sentences individually, not just as one combined input — and it’s doing it correctly.
![](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-14%20113054.png)

The evolved sentiment app now displaying enhanced sentiment results.
  - Timestamped emotional entries (2025-07-12 07:15 PM)
  - Overall Sentiment from the full text → Positive 😊
  - Original user input is preserved
  - Sentence-level breakdown is shown with:
    - Individual sentence
    - Its sentiment result and emoji

---
The inbound rules configuration for your AWS EC2 security group. It determines which traffic is allowed to reach your instance.
![](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-14%20111433.png)

The Sentiment App fully displays timestamped entries with sentiment breakdown.
For each user input, it’s showing:

- Timestamp
- Overall Sentiment (aggregated across all sentences)
- Original entry
- Per-sentence sentiment classification

![](https://github.com/price2high/Images-SAWTApp/blob/main/Screenshot%202025-07-14%20111448.png)

