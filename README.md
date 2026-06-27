⌨️ Keystroke Dynamics Authentication
Invisible Security. Zero Friction. Powered by Deep Learning.
PythonTensorFlowFlask
License
 
Status


Keystroke Dynamics is a behavioral biometric that authenticates users based on how they type — not just what they type. Even if an attacker steals your password, they cannot replicate your unique typing rhythm.


📖 Table of Contents
The Concept
How It Works
Architecture
Tech Stack
Results & Performance
Installation
Usage
Project Structure
Compatibility
Contributing
License
💡 The Concept
Traditional passwords verify what you know. OTPs verify what you have. Keystroke Dynamics verifies who you are — passively, invisibly, and with zero extra effort from the user.

Every person has a unique neuro-muscular typing pattern baked into their muscle memory:

Metric	Description	Example
Dwell Time	How long a key is held down	A heavy typist holds keys for ~130ms
Flight Time	Gap between releasing one key and pressing the next	Hesitation before capital letters
Digraph Latency	Time between pressing consecutive keys	Fast typists have near-zero latency
These three signals form an invisible biometric fingerprint that is unique to each individual — even for the exact same password string.

🧬 How It Works

User types password
        │
        ▼
┌─────────────────────┐
│  JavaScript Frontend │  ← Captures keydown/keyup with performance.now()
│  (microsecond prec.) │    Uses e.code to handle Shift-key edge cases
└────────┬────────────┘
         │  JSON (key, code, type, timestamp)
         ▼
┌─────────────────────┐
│   Flask Backend      │
│   parse_events()     │  ← Pairs keydown↔keyup by physical key code
└────────┬────────────┘
         │  press_times[], release_times[]
         ▼
┌─────────────────────┐
│  Feature Extraction  │  ← Extracts Dwell, Flight, Digraph Latency
│  Z-Score Normalize   │  ← Centers and scales to [0, 1]
└────────┬────────────┘
         │  Feature vector (dim = 3 × (N−1))
         ▼
┌─────────────────────┐
│  Deep Neural Network │  ← Personalized MLP per user
│  (TensorFlow/Keras)  │    Binary: Genuine (1) vs Impostor (0)
└────────┬────────────┘
         │  Confidence Score ∈ [0.0, 1.0]
         ▼
┌─────────────────────┐
│  EER Threshold       │  ← ACCEPTED if score ≥ threshold
│  Comparison          │    REJECTED otherwise
└─────────────────────┘
Solving the Small-Data Problem
Training a neural network normally requires thousands of samples. We only ask users to type their password 5 times. The backend solves this by:

Computing μ (mean) and σ (std deviation) of the 5 samples per key.
Generating 500 synthetic genuine samples using np.random.normal(μ, σ).
Generating 500 synthetic impostor samples using population-level human typing distributions.
Training the DNN on this balanced synthetic dataset — all in under 2 seconds.
🏗️ Architecture
Neural Network Topology (per user)

Input Layer     → dim = 3 × (password_length − 1)
                       [Dwell, Flight, Latency per key pair]
Hidden Layer 1  → Dense(128, activation='relu')
                → BatchNormalization()
                → Dropout(0.3)
Hidden Layer 2  → Dense(64, activation='relu')
                → BatchNormalization()
                → Dropout(0.3)
Hidden Layer 3  → Dense(32, activation='relu')
                → BatchNormalization()
                → Dropout(0.3)
Output Layer    → Dense(1, activation='sigmoid')
                → Score ∈ [0.0, 1.0]
Loss: Binary Crossentropy
Optimizer: Adam (lr = 0.001)
Stopping: EarlyStopping on val_loss, patience = 10
Threshold: Automatically computed via Equal Error Rate (EER) on validation split

🛠️ Tech Stack
Layer	Technology	Purpose
Frontend	Vanilla JavaScript, HTML5, CSS3	Real-time key event capture via performance.now()
Backend	Python, Flask	REST API server, data routing
Deep Learning	TensorFlow 2.x, Keras	Neural network definition, training, inference
Data Processing	NumPy	Gaussian synthesis, matrix operations
Evaluation	scikit-learn	ROC curve, AUC, EER computation
Visualization	Matplotlib, Seaborn	ROC plots, training history charts
Storage	JSON (profiles) + .keras (models)	Lightweight on-disk persistence
📊 Results & Performance
Metric	Value
Model Training Time	< 2 seconds (CPU)
Equal Error Rate (EER)	< 1.0% on synthetic validation
False Acceptance Rate	Near 0% (genuine vs impostor)
Authentication Latency	< 300ms (including model inference)
Enrollment Samples Required	Only 5 password repetitions
Supported Password Length	4+ characters, any complexity
The system uses dynamic thresholding — each user gets a personalized EER-optimal threshold calibrated to their own typing consistency. Consistent typists get tight thresholds; erratic typists get wider margins.

🚀 Installation
⚠️ Python Version Requirement
TensorFlow currently supports Python 3.10, 3.11, 3.12, and 3.13 only.
Python 3.14 is NOT supported. Install Python 3.12 from python.org/downloads if needed.

Option A — One-Click (Recommended)
Download the latest release zip from the 
Releases
 page.
Extract the zip (do not run from inside the zip preview).
Run the launcher for your OS:

Windows → Double-click START.bat
Mac/Linux → bash START.sh
The script will automatically:

✅ Check your Python version
✅ Create an isolated virtual environment
✅ Install all dependencies
✅ Create output directories
✅ Launch the web server
Then open http://localhost:5000 in your browser.

Option B — Manual Setup
bash

# Clone the repository
git clone https://github.com/yourusername/keystroke-dynamics.git
cd keystroke-dynamics
# Create and activate virtual environment
python -m venv venv
# Windows
venv\Scripts\activate
# Mac/Linux
source venv/bin/activate
# Install dependencies
pip install -r requirements.txt
# Run the application
python app.py
📋 Usage
Phase 1: Enrollment (Train your AI)
Open the Phase 1: Enrollment tab.
Enter your Username and define a Password (minimum 4 characters).
Click Start Enrollment and type your password exactly 5 times.
Watch the live logs — your personalized DNN trains in real-time!
On success, you'll see your Avg Dwell Time and Avg Flight Time — your unique typing fingerprint.
Phase 2: Authentication (Test the AI)
Switch to the Phase 2: Authentication tab.
Select your enrolled username from the dropdown.
Type your password naturally — the system authenticates you instantly.
🎭 The Impostor Test
This is the most compelling demo.
Tell a friend your exact password and ask them to type it.
They will be REJECTED — the neural network caught their different typing rhythm.

📁 Project Structure

keystroke_auth/
│
├── app.py                    # Flask server — routes, parse_events, inference
├── main.py                   # CLI demo (simulate logins from terminal)
├── requirements.txt          # All Python dependencies
├── START.bat                 # One-click Windows launcher
├── START.sh                  # One-click Mac/Linux launcher
│
├── src/
│   ├── __init__.py
│   ├── config.py             # Paths, hyperparameters, reproducibility seeds
│   ├── model.py              # Keras Sequential DNN builder
│   ├── feature_extractor.py  # Dwell, Flight, Latency extraction + Z-score normalization
│   ├── live_trainer.py       # On-the-fly DNN training from 5 live samples
│   ├── data_generator.py     # Synthetic data generator for batch pre-training
│   ├── trainer.py            # Batch training pipeline (for CLI demo)
│   ├── evaluator.py          # EER, FAR, FRR, ROC computation
│   ├── simulator.py          # Simulates login attempts for testing
│   └── report.py             # Generates PNG plots and text report
│
├── static/
│   ├── script.js             # Key event capture, REST API calls, UI logic
│   └── style.css             # Glassmorphism dark UI, responsive layout
│
├── templates/
│   └── index.html            # Main SPA template
│
└── outputs/                  # Auto-created on first run
    ├── saved_models/         # Per-user .keras model files
    ├── user_profiles/        # Per-user JSON profiles (threshold, norm stats)
    └── plots/                # ROC curves, training history PNGs
🖥️ Compatibility
OS	Support
Windows 10/11	✅ Full support
macOS 12+	✅ Full support
Ubuntu 20.04+	✅ Full support
Python Version	Support
3.10	✅
3.11	✅
3.12	✅ (Recommended)
3.13	✅
3.14+	❌ TensorFlow not yet compatible
Note: TensorFlow does not support native GPU acceleration on Windows for versions ≥ 2.11. CPU inference is used, which is sufficient for this application.

🤝 Contributing
Contributions are welcome! Here are some ideas for future improvements:

Continuous Authentication — Re-verify the user's typing rhythm during an active session
Adaptive Learning — Slowly update the model as the user's rhythm evolves over time
Multi-Factor Fusion — Combine with traditional password hashing for production use
Mobile Support — Adapt timing capture for touchscreen keyboards
bash

# Fork the repo and create a feature branch
git checkout -b feature/adaptive-learning
# Commit your changes
git commit -m "Add: adaptive model update on successful auth"
# Push and open a Pull Request
git push origin feature/adaptive-learning
📄 License
This project is licensed under the MIT License — see the 
LICENSE
 file for details.

Built with ❤️ using TensorFlow, Flask, and Behavioral Science

Every keystroke tells a story. This system reads it.
