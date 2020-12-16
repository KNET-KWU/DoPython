# request 모듈이 ip 주소를 확인하는 방법

## 목차

* 설명

    * `inet_aton` 메서드는 무엇인가?

* request 모듈에서 ip 주소를 다루는 방식 (다른 메서드)

* 정리

## 설명

파이썬의 내장 모듈인 [ipaddress](https://docs.python.org/3/library/ipaddress.html)에
대해서 쓰다 문득 request 네트워크 모듈에는 ipaddress 모듈이 쓰일까 궁금해서 찾아봤다.

파이썬을 쓰다보면 반드시 한번은 쓰게되는 request 모듈이라 ipaddress 모듈이 당연히 쓰일 줄 알았다.

Github에 있는 [request](https://github.com/psf/requests)에서 "import ipaddress"를 찾아본 결과 쓰고 있지 않는 것을 볼 수 있었다.

아래는 request 모듈에서 네트워크 요청을 만들때 문자열로 대입할 때 유효한 ip 주소인지 확인하는 메서드다.

[requests/requests/utils.py - line 652](https://github.com/psf/requests/blob/589c4547338b592b1fb77c65663d8aa6fbb7e38b/requests/utils.py#L652)

```python
def is_ipv4_address(string_ip):
    """
    :rtype: bool
    """
    try:
        socket.inet_aton(string_ip)
    except socket.error:
        return False
    return True
```

볼 수 있듯이 바로 socket 클래스에서 `inet_aton` 메서드를 호출하는 것을 볼 수 있다.

<br>

### `inet_aton` 메서드는 무엇인가?

inet_aton 함수는 C 네트워크 모듈인 arpa/inet.h 헤더 파일에 정의된 함수로 **문자열을 16진수를 변환하는 함수**다.

inet_**ntoa** 함수는 반대로 **문자열을 16진수로 변환하는 함수**다.

함수 이름에서 볼 수 있듯이

n to a - Number to Ascii

a to n - Ascii to Number

라는 단축어로 함수의 역할과 그 의미를 알 수 있다.

<br>

## request 모듈에서 ip 주소를 다루는 방식 (다른 메서드)

인자로 주어진 아이피 주소가 네트워크에 속해있는 지 확인하는 메서드에서도 이와 같은 확인 방법을 알 수 있다.

[requests/requests/utils.py - line 626](https://github.com/psf/requests/blob/589c4547338b592b1fb77c65663d8aa6fbb7e38b/requests/utils.py#L626)

```python
def address_in_network(ip, net):
    """This function allows you to check if an IP belongs to a network subnet
    Example: returns True if ip = 192.168.1.1 and net = 192.168.1.0/24
             returns False if ip = 192.168.1.1 and net = 192.168.100.0/24
    :rtype: bool
    """
    ipaddr = struct.unpack('=L', socket.inet_aton(ip))[0]
    netaddr, bits = net.split('/')
    netmask = struct.unpack('=L', socket.inet_aton(dotted_netmask(int(bits))))[0]
    network = struct.unpack('=L', socket.inet_aton(netaddr))[0] & netmask
    return (ipaddr & netmask) == (network & netmask)
```

ip 주소 자체가 유효한지 여부를 따지지 않고 바로 숫자 타입으로 변환한뒤 구조체(struct)에 비트 논리곱(&) 연산자를 사용하고 있다.

<br>

## 정리

그래서 request 모듈에서는 ip주소를 문자열 그 자체로 다루는 것을 볼 수 있다.

ipaddres 모듈의 `ip_address` 클래스를 사용해도 `__repr__` 정의가 잘 정의되어 있기 때문에<br>
문자열처럼 사용할 수 있음에도 불구하고 사용하지 않는 것을 확인할 수 있다.

```python
import ipaddress

s = ipaddress.IPv4Address('192.1.1.1')
print(s)    # 192.1.1.1

ss = ipaddress.IPv6Address('2001:db00::0')
print(ss)   # 2001:db00::
```

그냥 출력해도 `__repr__`이 정의되어 있기 때문에 문자열처럼 편리하게 사용할 수 있다.

...

그럼에도 왜 request 모듈은 ipaddres를 사용하지 않을까?

ipaddress 모듈은 파이썬 버전 3.3에 나오고, request 모듈은 python2 시절 만들어졌기 때문에

처음 만들어지던 시절 존재하지 않았고, 지금와서 딱히 추가해야할 필요를 못 느껴서 추가하지 않은 것이라고 추측한다.

request이라는 모듈이 Network Layer 중 Application 단계를 지원하는 모듈이고,

파이썬의 다른 내장 모듈인 [urllib](https://docs.python.org/3/library/urllib.request.html#module-urllib.request)
에서 조차 더 추상화된 request 모듈을 사용하라고 권하고,

request 모듈 관리자 또한 그 점을 인식하고 있을테니 ip 주소를 다룰 인터페이스를 더 보완할 필요가 없다고 느끼는 것 같다.

### 진짜 정리

파이썬의 내장 모듈인 ipaddress에 대해서 글을 쓰려다 문득 request 모듈은 이걸 쓰는지 궁금해서 찾아봤고,

왜 안썼을까? 라는 의문에 '추상 레이어를 집중적으로 지원하는 모듈이기 때문에 추가할 필요가 없다고 판단했을 것'라는 결론을 도출했다.