import subprocess
import time
import requests
import json

def start_server():
    return subprocess.Popen(["python", "server.py"], stdout=subprocess.PIPE, stderr=subprocess.PIPE)

def test_calc_endpoint():
    server = start_server()
    time.sleep(1)  # Даем серверу время запуститься
    
    url = "http://localhost:8000/calc"
    headers = {"Content-Type": "application/json"}
    data = json.dumps("2+2")
    
    try:
        response = requests.post(url, headers=headers, data=data)
        assert response.status_code == 200, f"Unexpected status code: {response.status_code}"
        assert response.json() == "request recieved", f"Unexpected response: {response.json()}"
    finally:
        server.terminate()
        server.wait()

if __name__ == "__main__":
    test_calc_endpoint()
    print("Test passed!")
