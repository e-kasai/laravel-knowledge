

---
#### $this->post(...)

- HTTP POSTリクエストをテスト環境で実行するメソッド。
- 実際にブラウザから送信するのと同じ挙動をシミュレートできるの。

戻り値は TestResponse オブジェクトで、$response->assertStatus(200) とか、レスポンスを検証できる

