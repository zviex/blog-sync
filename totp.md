# Time-Base One Time Password(totp) 基于时间的一次密码

这个技术在现在非常常见，比如steam的动态验证码，Microsoft Authenticator/Google Authenticator这类都是基于此技术实现的。

实现该技术的背景非常简单。主要是涉及到消息认证码形式的加密，比如hmac和时间，该算法实现公式如下。

涉及到概念
`chunk`: 时间块，即数字验证码的有效期范围。
`UnixTime`：计算机自1970年1月1日（UTC）以来所经历过的秒数
$$
CODE = HMAC(UnixTime/chunk,Key)
$$
这个公式得出的CODE，就是我们在认证器中看的一定时间内的验证码。

那么这个技术是如何做到离线状态下，仍然可以进行认证的呢？



