import unittest
import threading
import json
import requests
from http.server import HTTPServer
from server import CalcHandler  # Импортируем серверный обработчик

class TestCalcServer(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        """Запускаем сервер в отдельном потоке перед тестами."""
        cls.server_address = ('localhost', 8000)
        cls.httpd = HTTPServer(cls.server_address, CalcHandler)
        cls.server_thread = threading.Thread(target=cls.httpd.serve_forever)
        cls.server_thread.setDaemon(True)
        cls.server_thread.start()

    @classmethod
    def tearDownClass(cls):
        """Останавливаем сервер после тестов."""
        cls.httpd.shutdown()
        cls.httpd.server_close()

    def test_post_request(self):
        """Проверка POST-запроса."""
        url = "http://localhost:8000"
        response = requests.post(url)
        self.assertEqual(response.status_code, 200)  # Должен быть код 200
        self.assertEqual(response.headers["Content-Type"], "application/json")
        data = response.json()
        self.assertEqual(data, {"message": "request received"})  # Проверяем JSON-ответ

    def test_invalid_method(self):
        """Проверка запроса с неверным методом (GET вместо POST)."""
        url = "http://localhost:8000"
        response = requests.get(url)
        self.assertEqual(response.status_code, 405)  # Должен быть код 405 (Method Not Allowed)

if __name__ == "__main__":
    unittest.main()
