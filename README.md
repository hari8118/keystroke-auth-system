Keystroke Dynamics Authentication System ⌨️🧠
Keystroke DynamicsDeep LearningPython

A modern, frictionless authentication system that verifies users not just by what they type (their password), but by how they type it. By analyzing the microscopic neural-muscular rhythms of a user's typing pattern, this system trains a personalized Deep Neural Network (DNN) to act as an invisible second layer of security.

Even if a hacker steals your password, they cannot replicate your exact typing rhythm.

🌟 Key Features
Zero-Friction 2FA: No phones, no OTPs, no hardware tokens. Security is verified passively as the user types naturally.
Deep Learning Powered: Uses TensorFlow & Keras to build and train a custom multi-layer perceptron (MLP) anomaly detector for every individual user.
Microsecond Precision: The frontend captures physical keydown and keyup events down to the microsecond, ensuring accurate timing even if modifier keys (like Shift) are used.
Synthetic Data Generation: Users only need to type their password 5 times to enroll. The backend mathematically simulates 500+ realistic variations using Gaussian distributions to solve the "small data" problem for neural network training.
Dynamic EER Thresholding: Automatically calculates the Equal Error Rate (EER) to assign a personalized acceptance threshold balancing False Acceptances and False Rejections.
🧬 How It Works
The system extracts two primary behavioral metrics:

Dwell Time: The amount of time a specific key is held down (KeyUp - KeyDown).
Flight Time: The amount of time between releasing one key and pressing the next (Next_KeyDown - Current_KeyUp).
These timing arrays are normalized using Z-Scores and fed into the Neural Network. If the typing rhythm falls within the user's learned threshold, they are ACCEPTED. If the rhythm belongs to an impostor, they are REJECTED.

🚀 One-Click Installation
This project is built to be 100% portable. You don't need to manually configure environments or install libraries.

Prerequisites
Python 3.10 or higher installed.
Windows: Ensure Python is added to your PATH.
Linux/Mac: Ensure python3-venv is installed.
Launching the App
Windows: Double-click the START.bat file.
Mac/Linux: Open a terminal in the project folder and run bash START.sh.
What the script does: It will automatically create an isolated Virtual Environment, install TensorFlow, Flask, and all required machine learning libraries, create the necessary output folders, and launch the web server.

Accessing the Web UI
Once the script finishes, open your browser and navigate to: http://localhost:5000

💻 Usage Guide
Phase 1: Enrollment (Training your AI)
Open the Phase 1: Enrollment tab.
Enter your Name and a secure Password.
Type the password exactly 5 times.
Behind the scenes: The system captures your rhythm, generates 500+ synthetic samples, trains your personalized Deep Neural Network, and saves the .keras model to the database.
Phase 2: Authentication (Testing the AI)
Switch to the Phase 2: Authentication tab.
Select your enrolled username from the dropdown.
Type your password normally. You will be authenticated and shown your confidence score!
The Impostor Test: Ask a friend to sit at your keyboard. Tell them your exact password and have them type it. The system will reject them because their muscle memory and rhythm differ from yours.
🛠️ Technology Stack
Frontend: HTML5, CSS3 (Glassmorphism UI), Vanilla JavaScript (performance.now() API).
Backend: Python, Flask.
Machine Learning: TensorFlow, Keras, NumPy (Gaussian synthesis), Scikit-Learn (ROC evaluation).
📄 License
This project is open-source and available under the MIT License. Feel free to fork, modify, and integrate into your own security architectures!
