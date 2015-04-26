# wechat-python-sdk-extention
add post group message function

In the [wechat-python-sdk](https://github.com/doraemonext/wechat-python-sdk), add post group message method in ext.py file:

```
def send_news_toall(self, newsid):
        """
        群发消息
        :param newsid:图文id
        :return:null
        """

        url = 'https://mp.weixin.qq.com/cgi-bin/masssend?t=ajax-response&token={token_id}&lang=zh_CN'.format(
            token_id=self.__token
        )
        payload = {
            'f': 'json',
            'fileid': newsid,
            'type': 10,
            'token': self.__token,
            'error': 'false',
            'ajax': 1,
            'random': random.random(),
            'appmsgid': newsid,
            'sex': 0,                 // 选择年龄组  0为全部
            'synctxweibo' : 0,        // 是否同步到微博
            'city': '',               // 选择城市 空为全国
            'country':'',             // 选择国家 
        }

        headers = {
            'x-requested-with': 'XMLHttpRequest',
            'referer': 'https://mp.weixin.qq.com/cgi-bin/masssendpage?t=mass/send&type=10&appmsgid={appmsgid}&token={token_id}&lang=zh_CN'.format(
                token_id=self.__token,
                appmsgid=newsid,
            ),
            'cookie': self.__cookies,
        }

        r = requests.post(url, data=payload, headers=headers)

        try:
            message = json.loads(r.text)
        except ValueError:
            raise NeedLoginError(r.text)
        try:
            if message['base_resp']['ret'] == 10700 or message['base_resp']['ret'] == -21:
                raise ValueError('fake id not exist')
            if message['base_resp']['ret'] == 10705:
                raise ValueError('message id not exist')
            if message['base_resp']['ret'] != 0:
                raise NeedLoginError(r.text)
        except KeyError:
            raise NeedLoginError(r.text)
        return r
```
