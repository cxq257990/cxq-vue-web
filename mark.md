## ��Ҫ��¼
* �ӿڲ�����Ҫ��¼���ص�һϵ�����ݣ����token,session�ȵȣ���BaseRunner
* ����setUpClass���ݸ������ӿ�

```
def get_session():
    req = requests.session()
    url = ""
    data = {}
    req.post(url, data, verify=False)
    return req


class ParametrizedTestCase(unittest.TestCase):
  

    def __init__(self, methodName='runTest', param=None):
        super(ParametrizedTestCase, self).__init__(methodName)

    @classmethod
    def setUpClass(cls):
        cls.rq = get_session() # ��¼���session
        pass
```


* ��TestCasesĿ¼�е�Api.py�н��ܵ�¼���session,������BaseReq.py�н����޸�

```

class ApiTest(ParametrizedTestCase):
    def test_api(self):
		ls = read_excel(Element.API_FILE)
		# self.req ���ǵõ��˵�¼�����Ϣsession 
	    Config().config_req(ls��self.req)

    @classmethod
    def setUpClass(cls):
        super(ApiTest, cls).setUpClass()
        

```


## pict���ɵ��쳣����

* ��BaseParams.py�е�__param_format���������쳣������������Ҫ������չ

```
 def __param_format(self, key):
        param_type = {
            str: lambda: str(uuid.uuid1()),
            list: lambda: [],
            dict: lambda: {}
        }
        return param_type[type(key)]()
```

## ����˵��
* ������pict��ģ�����Ժ󣬲���Լ�����м��
* ����֧�ֵļ���Ϊ��
	* ```{"id":11,"code":2222}```  �����������Ϊ��``` {"code":22}```
	*  ```{"code":1,data:[{"id": "123"}, {}]}```  �����������Ϊ��```{"id":123}```
* ������㲻���ÿ���������չ����BaseReq.py�е�__check������

```
    def __check(self, hope, res):
        hope = json.loads(hope)
        fact = json.loads(res)
        for items in fact:
            if type(fact[items]) == list:
                for item in fact[items]:
                    for k in hope:
                        if item.get(k, "") == hope[k]:
                            return "ͨ��"
            if type(fact[items]) == dict:
                for k in hope:
                    if fact[items].get(k, "") == hope[k]:
                        return "ͨ��"
            for k in hope:
                if fact.get(k, "") == hope[k]:
                    return "ͨ��"
        return "ʧ��"
```