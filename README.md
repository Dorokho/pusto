import unittest
import threading
import json
import time
from http.server import HTTPServer
from urllib.request import Request, urlopen
from urllib.error import HTTPError
from server import CalcHandler

class TestCalcServerIntegration(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        cls.httpd = HTTPServer(("localhost", 8000), CalcHandler)
        cls.server_thread = threading.Thread(target=cls.httpd.serve_forever)
        cls.server_thread.setDaemon(True)
        cls.server_thread.start()
        time.sleep(1)  # Даем серверу немного времени на запуск

    @classmethod
    def tearDownClass(cls):
        cls.httpd.shutdown()
        cls.httpd.server_close()
        cls.server_thread.join()

    def send_post_request(self, data):
        url = 'http://localhost:8000'
        request = Request(url, data=json.dumps(data).encode('utf-8'), method='POST')
        request.add_header('Content-Type', 'application/json')
        return urlopen(request)

    def test_successful_post_request(self):
        response = self.send_post_request({"message": "Hello"})
        self.assertEqual(response.getcode(), 200)
        response_data = json.loads(response.read().decode('utf-8'))
        self.assertEqual(response_data, "request recieved")

    def test_post_request_with_invalid_json(self):
        url = 'http://localhost:8000'
        request = Request(url, data=b'invalid_json', method='POST')
        request.add_header('Content-Type', 'application/json')

        with self.assertRaises(HTTPError) as context:
            urlopen(request)
        self.assertEqual(context.exception.code, 400)
        error_response = json.loads(context.exception.read().decode('utf-8'))
        self.assertIn("error", error_response)
    
    @classmethod
    def tearDownClass(cls):
        cls.httpd.shutdown()
        cls.httpd.server_close()
        cls.server_thread.join()

if __name__ == '__main__':
    unittest.main()
