✅app.py Code:
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "🚀 Deployed via Jenkins CI/CD Pipeline!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
🧠 Line-by-Line Explanation:
🔹 from flask import Flask
	• “Import the Flask class from the Flask library.”
	• Flask is a Python web framework — it helps us create web apps easily.
	• Like: instead of writing server code from scratch, Flask gives us shortcuts.
🔹 app = Flask(__name__)
	• Creates a new web app using Flask.
	• __name__ tells Flask where to find your code.
	• app is now your web application object — like a server that's ready to respond to web requests.
🔹 @app.route('/')
This is called a route decorator.
	• It tells Flask:
“When someone opens the root URL (/), run the function below.”
	• In other words, when you go to http://<ec2-ip>:5000/ → this function will be called.
🔹 def home():
	• This is just a regular Python function named home().
	• It’s connected to the / route above.
🔹 return "🚀 Deployed via Jenkins CI/CD Pipeline!"
	• This line sends a response back to the browser.
	• When someone visits your website, they will see this message on the page.
🔹 if __name__ == '__main__':
This is a standard Python line.
	• It means:
“Only run the below code if this file is being run directly, not imported from another file.”
This is useful when you want to make sure the web server starts only when you run this file directly.
🔹 app.run(host='0.0.0.0', port=5000)
This line starts the Flask server:
	• host='0.0.0.0' → allows access from outside (like your browser using EC2 public IP)
	• port=5000 → your app will be available at http://<your-ip>:5000
📦 What Happens When You Run It:
	• Your EC2 instance becomes a mini web server
	• The app listens on port 5000
	• When someone visits http://<your-ec2-ip>:5000/ → it shows:
🚀 Deployed via Jenkins CI/CD Pipeline!


✅requirements.txt
	• requirements.txt is a text file used in Python projects.
	• It lists all the Python libraries your app needs to work.
	• When someone installs your app, they can install everything by running:
Because your app uses Flask, a Python web framework. So you need to tell Python:
	“Hey, please make sure Flask is installed for this project.”

✅Dockerfile:
FROM python:3.9
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
🧠 Line-by-Line Explanation:
 🔹 FROM python:3.9
	• What it does:
This sets the base image for your Docker container.
	• It says:
"Start from a Docker image that already has Python 3.9 installed."
🔹WORKDIR /app
	• What it does:
Sets the working directory inside the container to /app.
📌 It’s like saying:
	“From now on, assume you're inside the /app folder.”
So all following commands (COPY, RUN, etc.) will happen inside /app.
🔹COPY . .
	• What it does:
Copies everything from your current directory on your EC2 machine into the /app folder inside the container.
📌 In simple terms:
	“Copy all my app files into the container.”
🔹RUN pip install -r requirements.txt
	• What it does:
Installs the required Python libraries inside the container, using the requirements.txt file.
📌 It's like saying:
	“Install Flask and other needed packages inside the container.”
🔹CMD ["python", "app.py"]
	• What it does:
Tells Docker:
“When someone runs this container, start the app by running python app.py.”
📌 So when you start the container, your Flask app will automatically launch!
📦What This Dockerfile Does in Simple Steps:
	1. Starts with a Python 3.9 base
	2. Creates a work folder /app
	3. Copies your Flask app into /app
	4. Installs Flask (and any other Python packages)
	5. Starts your Flask app when the container runs

![image](https://github.com/user-attachments/assets/b7ebf50c-c473-4198-9641-8ad290d1b3e6)
