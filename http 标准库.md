# Client
```go
type Client struct {
    Transport RoundTripper

    CheckRedirect func(req *Request, via []*Request) error

    Jar CookieJar

    Timeout time.Duration
}
```


```go
type RoundTripper interface {
	RoundTrip(*Request) (*Response, error)
}
```

`RoundTripper`接口代表一个http事务，可以理解为在http发送和接受后的一个回调的一个基本接口，常规http的实现，就是可以通过实现roundtrip接口来实现http协议的解析，Request和Response本身只是结构，协议无关。标准库的实现为`http.Transport`

```go
type CookieJar interface {
    SetCookies(u *url.URL, cookies []*Cookie)

    Cookies(u *url.URL) []*Cookie

}
```

`CookieJar`是http中关于cookie的实现