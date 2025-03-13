import unittest
import threading
import json
from http.server import HTTPServer
from urllib.request import Request, urlopen
from urllib.error import HTTPError
from server import CalcHandler  # Предполагается, что код сохранен в server.py

class TestCalcHandler(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        cls.server_address = ('', 8000)
        cls.httpd = HTTPServer(cls.server_address, CalcHandler)
        cls.server_thread = threading.Thread(target=cls.httpd.serve_forever)
        cls.server_thread.setDaemon(True)
        cls.server_thread.start()

    @classmethod
    def tearDownClass(cls):
        cls.httpd.shutdown()
        cls.server_thread.join()

    def test_post_request(self):
        url = 'http://localhost:8000'
        data = json.dumps({"test": "data"}).encode('utf-8')
        request = Request(url, data=data, method='POST')
        request.add_header('Content-Type', 'application/json')

        with urlopen(request) as response:
            self.assertEqual(response.getcode(), 200)
            response_data = json.loads(response.read().decode('utf-8'))
            self.assertEqual(response_data, "request recieved")

    def test_post_request_error_handling(self):
        url = 'http://localhost:8000'
        data = b'invalid_json'  # Некорректные данные
        request = Request(url, data=data, method='POST')
        request.add_header('Content-Type', 'application/json')

        try:
            urlopen(request)
        except HTTPError as e:
            self.assertEqual(e.code, 500)
            error_response = json.loads(e.read().decode('utf-8'))
            self.assertIn("error", error_response)

if __name__ == '__main__':
    unittest.main()
