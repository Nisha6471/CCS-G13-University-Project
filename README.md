[9:21 am, 26/4/2025] Shivam@pres: from flask import Flask, request, render_template
import numpy as np
import pandas as pd
import subprocess
import os
import warnings
import pickle
from feature import FeatureExtraction

warnings.filterwarnings('ignore')

file = open("C:/Users/Shivam/Documents/final year project/nisha\Phishing-URL-Detection/Phishing-URL-Detection/pickle/model.pkl", "rb")
gbc = pickle.load(file)
file.close()

app = Flask(_name_)

@app.route("/", methods=["GET", "POST"])
def index():
    if request.method == "POST":
        mode = request.form["mode"]
        input_data = request.form["input"]

        if mode == "url":
            obj = FeatureExtraction(input_data)
            x = np.array(obj.getFeaturesList()).reshape(1, 30)
            y_pred = gbc.predict(x)[0]
          …
[9:21 am, 26/4/2025] Shivam@pres: app.py
[9:21 am, 26/4/2025] Shivam@pres: import ipaddress
import re
import urllib.request
from bs4 import BeautifulSoup
import socket
import requests
from googlesearch import search
import whois
from datetime import date, datetime
import time
from dateutil.parser import parse as date_parse
from urllib.parse import urlparse

class FeatureExtraction:
    features = []
    def _init_(self,url):
        self.features = []
        self.url = url
        self.domain = ""
        self.whois_response = ""
        self.urlparse = ""
        self.response = ""
        self.soup = ""

        try:
            self.response = requests.get(url)
            self.soup = BeautifulSoup(response.text, 'html.parser')
        except:
            pass

        try:
            self.urlparse = urlparse(url)
            …
[9:22 am, 26/4/2025] Shivam@pres: feature.py
[9:22 am, 26/4/2025] Shivam@pres: /* Cyber Security Theme */
:root {
  --cyber-dark: #0a0a16;
  --cyber-darker: #050510;
  --cyber-blue: #00f0ff;
  --cyber-blue-dark: #0066ff;
  --cyber-green: #00ff88;
  --cyber-red: #ff003c;
  --cyber-purple: #bd00ff;
  --cyber-gray: #1a1a2e;
  --cyber-light: #e0e0e8;
}

body {
  margin: 0;
  padding: 0;
  font-family: 'Share Tech Mono', monospace;
  background-color: var(--cyber-darker);
  color: var(--cyber-light);
  height: 100vh;
  overflow: hidden;
}

.cyber-container {
  display: flex;
  height: 100vh;
}

/* Left Panel - Input Section */
.cyber-input-panel {
  width: 40%;
  background-color: var(--cyber-dark);
  padding: 2rem;
  border-right: 1px solid rgba(0, 240, 255, 0.1);
  display: flex;
  flex-direction: column;
  position: relative;
  overflow:…
[9:22 am, 26/4/2025] Shivam@pres: style.css
[9:23 am, 26/4/2025] Shivam@pres: <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Advanced cyber security tool for detecting malicious URLs and PE files">
    <meta name="keywords" content="phishing url,phishing,cyber security,machine learning,classifier,python,malware detection">
    <meta name="author" content="VAIBHAV BICHAVE">

    <!-- BootStrap -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
        integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
    
    <!-- Font Awesome for icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    
    <!-- Cyber-style Google Font -->
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Share+Tech+Mono&display=swap" rel="stylesheet">
    
    <link href="static/styles.css" rel="stylesheet">
    <title>CYBER THREAT DETECTOR</title>
</head>

<body>
    <div class="cyber-container">
        <!-- Left Panel - Input Section -->
        <div class="cyber-input-panel">
            <div class="cyber-header">
                <h1><i class="fas fa-shield-virus"></i> CYBER THREAT DETECTOR</h1>
                <p class="cyber-subtitle">ANALYZE URLS & EXECUTABLES FOR MALICIOUS CONTENT</p>
            </div>
            
            <div class="cyber-form-container">
                <form action="/" method="post">
                    <div class="cyber-form-group">
                        <label for="mode"><i class="fas fa-cogs"></i> SCAN MODE</label>
                        <select name="mode" id="mode" class="cyber-select" required>
                            <option value="url">URL ANALYSIS</option>
                            <option value="pe">PE FILE SCAN</option>
                        </select>
                    </div>
                    
                    <div class="cyber-form-group">
                        <label for="input"><i class="fas fa-terminal"></i> TARGET INPUT</label>
                        <input type="text" name="input" id="input" class="cyber-input" placeholder="Enter URL or file path..." required>
                        <div class="cyber-input-border"></div>
                    </div>
                    
                    <button type="submit" class="cyber-scan-button">
                        <i class="fas fa-search"></i> INITIATE SCAN
                        <div class="cyber-button-lights">
                            <span></span>
                            <span></span>
                            <span></span>
                        </div>
                    </button>
                </form>
            </div>
            
            <div class="cyber-footer">
                <p>SYSTEM STATUS: <span class="cyber-status">OPERATIONAL</span></p>
                <div class="cyber-pulse"></div>
            </div>
        </div>
        
        <!-- Right Panel - Results Section -->
        <div class="cyber-results-panel">
            <div class="cyber-results-header">
                <h2><i class="fas fa-clipboard-list"></i> SCAN RESULTS</h2>
                <div class="cyber-scan-animation">
                    <div class="cyber-scan-line"></div>
                </div>
            </div>
            
            <div class="cyber-results-content">
                <h3 id="prediction">READY TO SCAN</h3>
                
                <div class="cyber-results-actions">
                    <button class="cyber-proceed-button" id="button1" role="button" onclick="window.open('{{url}}')" target="_blank">
                    
