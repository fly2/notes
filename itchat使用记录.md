## itchat使用记录

[github](https://github.com/littlecodersh/ItChat)

[文档](https://itchat.readthedocs.io/zh/latest/)

### 使用技巧

#### 下载文件自定义路径

```python
#文件下载使用
@itchat.msg_register([PICTURE, RECORDING, ATTACHMENT, VIDEO])
def download_files(msg):
    msg.download('wechat/'+msg.fileName)
    #此处download中的参数是存储文件的相对路径。msg.fileName为文件名称
    
    
    
@itchat.msg_register(['Picture', 'Recording', 'Attachment', 'Video'])
def download_files(msg):
    msg['Text'](msg['FileName'])
    #此处括号中的msg['FikeName']为文件名，即存放在当前文件夹下，可以自行定义路径
    itchat.send('@%s@%s'%('img' if msg['Type'] == 'Picture' else 'fil', msg['FileName']), msg['FromUserName'])
    return '%s received'%msg['Type']
```

#### 群体发送消息

```python
import itchat, time
from itchat.content import *
import re

message='你好，朋友'
#登录
itchat.auto_login()
#得到好友列表
friends_dict=itchat.get_friends()
#发送给有备注的人
def to_special(friends_dict):
	for person in friends_dict:
    	if person.RemarkName is not '':
            itchat.send_msg(msg=message,toUserName=person.UserName)
```

