IMPORT LIBRARIES 
 import cv2
 import numpy as np
 import matplotlib.pyplot as plt 
from tensorflow.keras.applications  import MobileNetV2
 from tensorflow.keras.applications.mobilenet_v2  import preprocess_input 
from tensorflow.keras.models  import Model 
from Crypto.Cipher  import AES 
from Crypto.Util.Padding import pad, unpad
 from hashlib import sha256
 import base64 
from sklearn.decomposition import PCA
 from flask import Flask, request, jsonify, render_template 
import io 
from PIL import Image

app.py
# Imports
from flask import Flask, request, jsonify, render_template
import cv2
import numpy as np
from preprocess import preprocess_image, extract_features
from encrypt_decrypt import aes_encrypt, generate_biometric_key

# Flask app
app = Flask(__name__)

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/upload', methods=['POST'])
def upload_images():
    # Read uploaded images
    image1 = request.files['image1']
    image2 = request.files['image2']

    img1 = cv2.imdecode(np.frombuffer(image1.read(), np.uint8), cv2.IMREAD_COLOR)
    img2 = cv2.imdecode(np.frombuffer(image2.read(), np.uint8), cv2.IMREAD_COLOR)

    # Preprocess and extract features
    img1_features = extract_features(img1)
    img2_features = extract_features(img2)

    # Combine features and generate a biometric key
    combined_features = np.concatenate((img1_features, img2_features))
    biometric_key = generate_biometric_key(combined_features)

    # Encrypt the combined features
    combined_features_str = ','.join(map(str, combined_features))
    iv, encrypted_data = aes_encrypt(combined_features_str, biometric_key)

    return jsonify({
        "encrypted_data": encrypted_data,
        "iv": iv
    })

if __name__ == '__main__':
    app.run(debug=True)

capture.py
# Imports
import cv2

# Capture image
def capture_image(window_name="Capture Image"):
    cap = cv2.VideoCapture(0)
    print(f"Press 'c' to capture the image for {window_name}.")
    while True:
        ret, frame = cap.read()
        if not ret:
            print("Failed to capture video frame.")
            break

        cv2.imshow(window_name, frame)
        key = cv2.waitKey(1) & 0xFF
        if key == ord('c'):
            cap.release()
            cv2.destroyAllWindows()
            return frame
        elif key == ord('q'):
            print("Camera feed closed.")
            cap.release()
            cv2.destroyAllWindows()
            return None

preprocess.py
# Imports
import cv2
import numpy as np
from tensorflow.keras.applications import MobileNetV2
from tensorflow.keras.applications.mobilenet_v2 import preprocess_input
from tensorflow.keras.models import Model

# Initialize the feature extractor
base_model = MobileNetV2(weights="imagenet", include_top=False)
feature_extractor = Model(inputs=base_model.input, outputs=base_model.output)

# Image preprocessing
def preprocess_image(image, target_size=(224, 224)):
    image = cv2.resize(image, target_size)
    image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
    image = preprocess_input(image.astype(np.float32))
    return np.expand_dims(image, axis=0)

# Extract features
def extract_features(image):
    preprocessed_image = preprocess_image(image)
    features = feature_extractor.predict(preprocessed_image)
    return features.flatten()

encrypt_decrypt.py
# Imports
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
from hashlib import sha256
import base64

# Generate biometric key
def generate_biometric_key(features):
    features_str = ','.join(map(str, features))
    hash_key = sha256(features_str.encode()).digest()
    return hash_key[:16]  # 16-byte AES key

# AES encryption
def aes_encrypt(data, key):
    cipher = AES.new(key, AES.MODE_CBC)
    ct_bytes = cipher.encrypt(pad(data.encode(), AES.block_size))
    iv = base64.b64encode(cipher.iv).decode('utf-8')
    ct = base64.b64encode(ct_bytes).decode('utf-8')
    return iv, ct

# AES decryption
def aes_decrypt(iv, ciphertext, key):
    iv = base64.b64decode(iv)
    ciphertext = base64.b64decode(ciphertext)
    cipher = AES.new(key, AES.MODE_CBC, iv)
    decrypted_data = unpad(cipher.decrypt(ciphertext), AES.block_size)
    return decrypted_data.decode('utf-8')

test.py
# Imports
import unittest
from encrypt_decrypt import generate_biometric_key, aes_encrypt, aes_decrypt
import numpy as np

# Unit tests
class TestBiometricSystem(unittest.TestCase):
    def test_encryption_decryption(self):
        features = np.random.rand(100)
        key = generate_biometric_key(features)
        data = "test data"

        iv, encrypted_data = aes_encrypt(data, key)
        decrypted_data = aes_decrypt(iv, encrypted_data, key)

        self.assertEqual(data, decrypted_data)

if __name__ == "__main__":
    unittest.main()

templates/index.html
html
<!DOCTYPE html>
<html>
<head>
    <title>Biometric System</title>
    <link rel="stylesheet" type="text/css" href="/static/styles.css">
</head>
<body>
    <h1>Upload Biometric Images</h1>
    <form action="/upload" method="post" enctype="multipart/form-data">
        <label for="image1">Face Image:</label>
        <input type="file" id="image1" name="image1" accept="image/*" required><br><br>
        <label for="image2">Iris Image:</label>
        <input type="file" id="image2" name="image2" accept="image/*" required><br><br>
        <button type="submit">Submit</button>
    </form>
</body>
</html>
styles.css
css
body {
    font-family: Arial, sans-serif;
    text-align: center;
    margin-top: 50px;
}


h1 {
    color: #333;
}

form {
    margin: auto;
    display: inline-block;
}

button {
    padding: 10px 20px;
    background-color: #007BFF;
    color: white;
    border: none;
    cursor: pointer;
}

button:hover {
    background-color: #0056b3;
}
requirements.txt
Flask
tensorflow
opencv-python

