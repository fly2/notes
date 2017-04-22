## matlab发送邮件

用sendmail发送邮件，需要邮箱开通smtp服务，一般默认都没开。163邮箱密码输入的是授权码。

```matlab
%设置邮箱账户
MailAddress = 'MailAddress@163.com';
%设置密码
password = 'Mailpassword';
%设置收件人邮箱
send_mail='fly@example'
%配置smtp
setpref('Internet','E_mail',MailAddress);
setpref('Internet','SMTP_Server','smtp.163.com');
setpref('Internet','SMTP_Username',MailAddress);
setpref('Internet','SMTP_Password',password);
props = java.lang.System.getProperties;
props.setProperty('mail.smtp.auth','true');
props.setProperty('mail.smtp.socketFactory.class','javax.net.ssl.SSLSocketFactory');
props.setProperty('mail.smtp.socketFactory.port','465');
%发送邮件
subject='主题'
%正文内容
content='正文内容'
%附件地址
DataPath='d:\exaple.dat'
%带附件格式
sendmail(send_mail,subject,content,DataPath);
```

