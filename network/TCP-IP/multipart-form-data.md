# multipart/form-data 전송하기

`multipart/form-data`는 주로 웹에서 파일 업로드와 같은 다중 데이터를 HTTP 요청으로 전송할 때 사용되는 **MIME 타입이다.**

## 데이터 전송 예시

`multipart/form-data` 는 각 파트가 헤더, 본문, 구분자(바운더리) 로 나뉘어져 있다. 해당 방식으로 데이터가 전송되면 하나의 http 요청 안에 여러개의 부분이 포함되며 각 부분은 독립적인 내용을 담고 있고 각 파트마다 구분자가 붙는다.

```
POST /upload HTTP/1.1
Host: example.com
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="file"; filename="image.jpg"
Content-Type: image/jpeg

[이미지 바이너리 데이터]

------WebKitFormBoundary7MA4YWxkTrZu0gW--
```

### Window Size(윈도우 사이즈)

- TCP 윈도우 사이즈는 송신자가 전송할 수 있는 데이터 양을 제한한다. 이를 통해 송신자와 수신자 간의 흐름을 제어한다. `multipart/form-data` 방식으로 데이터를 전송할 때도 해당 흐름 제어가 적용된다
- `multipart/form-data` 요청에서 파일이나 데이터를 여러 파트로 나누어 보내더라도 송신자는 수신자가 한 번에 처리할 수 있는 데이터를 고려하여 보내고 수신자는 처리할 수 있는 데이터 크기를 초과하지 않도록 윈도우 크기를 알린다
  - **전송시 흐름 제어?**
    - **파일 크기**가 큰 경우, 여러 파트로 나누어 전송하는 동안 **TCP 윈도우 크기**에 의해 전송되는 데이터 양이 제한된다. 수신자가 한 번에 받을 수 있는 데이터 양이 10KB로 설정되어 있다면 송신자는 한 번에 10KB만큼 전송하고 수신자가 데이터를 처리하면 윈도우 크기를 업데이트하여 추가 데이터를 받을 수 있게 된다

### Sequence Number(일련 번호)

- 패킷 순서 보장
  - TCP는 연결 지향적이고 신뢰성 있는 전송을 보장한다. 데이터가 여러 개의 세그먼트로 나뉘어 전송 될 경우 각 세그만트에는 일련번호가 부여된다. 해당 번호가 각 패킷이 전송된 순서와 패킷 내의 바이트 오프셋..을 추적하기 위한 것이다
- 순서 재구성
  - 여러 개의 패킷이 네트워크를 통해 전송될 경우 패킷은 반드시 발송 순서대로 도착하지 않는다. 패킷이 경로에 따라 다르게 이동하거나 네트워크 지연이 있을 수 있기 때문이다. 이때 일련번호를 사용하여 수신자가 패킷을 정확한 순서대로 재조합할 수 있도록 한다

### 전체 전송 흐름

이미지 파일 전송시 파일의 바이너리 데이터는 순차적으로 전송된다. jpg나 Png 파일은 픽셀 단위로 데이터를 포함하고 있는데 각 픽셀은 색상값(RGB)등을 포함하는 정보다

1. 파일 분할
   1. 큰 파일은 네트워크의 크기 제한을 넘지 않도록 여러 청크로 나누어 전송 가능
2. 청크와 순서
   1. 이 청크들은 각기 다른 http 요청/응답의 일부로 전송되며 각 청크에는 순서 번호가 포함되어 있어 수신자가 각 청크를 정확한 순서대로 결합할 수 있도록 한다
3. 전송 완료 후 재구성
   1. 파일이 전송되는 모든 청크가 수신되면 서버에서는 이들을 하나의 파일로 재조합한다

이때 순서 번호나 시퀀스 번호가 중요한 역할을 한다. 여러 청크로 분할된 데이터를 올바른 순서대로 재조합 해야하기 때문이다

<aside>
💡

클라이언트에서 이미지 전송시, Http 요청은 이미지 파일을 한 번에 전송하는 것처럼 보이지만 실제로는 TCP 프로토콜을 사용하여 내부적으로 여러번 나누어 전송된다.

</aside>
