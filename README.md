import unittest
import json
import threading
import time
import requests
from http.server import HTTPServer
from calc_server import CalcHandler  # Импортируем обработчик из твоего кода

class TestCalcServerIntegration(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        cls.server_address = ('localhost', 8000)
        cls.httpd = HTTPServer(cls.server_address, CalcHandler)
        cls.server_thread = threading.Thread(target=cls.httpd.serve_forever)
        cls.server_thread.daemon = True
        cls.server_thread.start()
        time.sleep(1)  # Даем серверу время на запуск

    @classmethod
    def tearDownClass(cls):
        cls.httpd.shutdown()
        cls.server_thread.join()

    def test_valid_request(self):
        url = "http://localhost:8000/calc"
        headers = {'Content-Type': 'application/json'}
        data = json.dumps("2+2")
        response = requests.post(url, data=data, headers=headers)
        self.assertEqual(response.status_code, 200)
        self.assertEqual(response.json(), "request recieved")

    def test_invalid_json(self):
        url = "http://localhost:8000/calc"
        headers = {'Content-Type': 'application/json'}
        data = "{invalid_json}"  # Некорректный JSON
        response = requests.post(url, data=data, headers=headers)
        self.assertEqual(response.status_code, 400)
        self.assertIn("error", response.json())
        self.assertEqual(response.json()["error"], "Invalid JSON")

    def test_invalid_path(self):
        url = "http://localhost:8000/invalid"
        response = requests.post(url)
        self.assertEqual(response.status_code, 404)

if __name__ == "__main__":
    unittest.main()
