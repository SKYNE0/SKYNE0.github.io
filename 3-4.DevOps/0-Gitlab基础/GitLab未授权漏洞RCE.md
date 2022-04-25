## GitLab未授权漏洞RCE


### 1.背景
- 公司的 `GitLab` 部署在阿里云，开放公网
- 因版本老旧，准备升级版本，由`9.2`升级至`12.2`
- 升级后阿里云安全中心发现`DDOS木马病毒`
- 查阅后发现是 `GitLab` 官方已知的漏洞

```
该告警由如下引擎检测发现：
文件路径: /var/opt/gitlab/gitlab-workhorse/FBI.x86 (deleted)
恶意文件md5: d03cef3b4d35f7165caaecd6a93d2574
扫描来源方式: 进程启动扫描
检测方式: 云查杀
进程id: 2712
进程命令行:
文件创建用户: N/A
文件创建时间: N/A
文件修改时间: N/A
描述: 黑客在入侵系统后植入的恶意程序，会占用您的带宽攻击其他服务器，同时可能影响自身业务的正常运行，危害较大。 此类恶意程序可能还存在自删除行为，或伪装成系统程序以躲避检测。如果发现该文件不存在，请检查是否存在可疑进程、定时任务或启动项。帮助文档：https://help.aliyun.com/knowledge_detail/36279.html
```

### 2.影响版本

```
11.9 <= Gitlab CE/EE < 13.8.8
13.9 <= Gitlab CE/EE < 13.9.6
13.10 <= Gitlab CE/EE < 13.10.3
```

### 3.排查过程
- `FBI.x86`使用`strings`打开后，可以看到使用了`UPX`加壳压缩体积

```
$Info: This file is packed with the UPX executable packer http://upx.sf.net $
$Id: UPX 3.94 Copyright (C) 1996-2017 the UPX Team. All Rights Reserved. $
PROT_EXEC|PROT_WRITE failed.
NxUP Z
```

- 公开查阅资料后发现是已知的公开漏洞: 
  + [GitLab CE CVE-2021-22205](https://security.humanativaspa.it/gitlab-ce-cve-2021-22205-in-the-wild/)
  + [CVE-2021-22205](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-22205)
  + [action-needed-in-response-to-cve2021-22205](https://about.gitlab.com/blog/2021/11/04/action-needed-in-response-to-cve2021-22205/)
  + [how-to-determine-if-a-self-managed-instance-has-been-impacted](https://forum.gitlab.com/t/cve-2021-22205-how-to-determine-if-a-self-managed-instance-has-been-impacted/60918)

- 在`/var/log/gitlab/gitlab-workhorse/current` 日志中有大量境外IP恶意扫描

```
116678 {"correlation_id":"lJWame1BvG9","duration_ms":18,"host":"47.98.XXX.XXX","level":"info","method":"GET","msg":"access","proto":"HTTP/1.1","referrer":"http://47.98.XXX.XXX/","remote_addr":"5.8.71.65:0","remote_ip":"5.8.71.65","status":302,"system":"http","time":"2022-02-10T06:33:06+08:00","uri":"/","user_agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36","written_bytes":99}
116679 {"correlation_id":"yKstj8qcO99","duration_ms":31,"host":"47.98.XXX.XXX","level":"info","method":"GET","msg":"access","proto":"HTTP/1.1","referrer":"","remote_addr":"5.8.71.65:0","remote_ip":"5.8.71.65","status":302,"system":"http","time":"2022-02-10T06:33:06+08:00","uri":"/web-console/","user_agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36","written_bytes":99}
116680 {"correlation_id":"wgqQP1LSTH1","duration_ms":24,"host":"47.98.XXX.XXX","level":"info","method":"GET","msg":"access","proto":"HTTP/1.1","referrer":"","remote_addr":"5.8.71.65:0","remote_ip":"5.8.71.65","status":302,"system":"http","time":"2022-02-10T06:33:06+08:00","uri":"/dump.sql","user_agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36","written_bytes":99}
116681 {"correlation_id":"5AIICjMStB4","duration_ms":14,"host":"47.98.XXX.XXX","level":"info","method":"GET","msg":"access","proto":"HTTP/1.1","referrer":"","remote_addr":"5.8.71.65:0","remote_ip":"5.8.71.65","status":302,"system":"http","time":"2022-02-10T06:33:06+08:00","uri":"/?__debugger__=yes\u0026cmd=resource\u0026f=debugger.js","user_agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36","written_bytes":99}
116682 {"correlation_id":"lTaBAPVsa65","duration_ms":12,"host":"47.98.XXX.XXX","level":"info","method":"GET","msg":"access","proto":"HTTP/1.1","referrer":"\u003cinvalid URL\u003e","remote_addr":"5.8.71.65:0","remote_ip":"5.8.71.65","status":302,"system":"http","time":"2022-02-10T06:33:06+08:00","uri":"/admin.cgi","user_agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36","written_bytes":99}
116683 {"correlation_id":"54zKrzW6Mj3","duration_ms":19,"host":"47.98.XXX.XXX","level":"info","method":"GET","msg":"access","proto":"HTTP/1.1","referrer":"http://47.98.XXX.XXX/","remote_addr":"5.8.71.65:0","remote_ip":"5.8.71.65","status":302,"system":"http","time":"2022-02-10T06:33:06+08:00","uri":"/users/","user_agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36","written_bytes":99}
116684 {"correlation_id":"dwF3CWYFy56","duration_ms":35,"host":"47.98.XXX.XXX","level":"info","method":"GET","msg":"access","proto":"HTTP/1.1","referrer":"","remote_addr":"5.8.71.65:0","remote_ip":"5.8.71.65","status":302,"system":"http","time":"2022-02-10T06:33:06+08:00","uri":"/jmx-console/HtmlAdaptor?action=inspectMBean\u0026name=jboss.system:type%3DServerInfo","user_agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36","written_bytes":99}
116685 {"correlation_id":"KpWl8rifqv5","duration_ms":27,"host":"47.98.XXX.XXX","level":"info","method":"GET","msg":"access","proto":"HTTP/1.1","referrer":"","remote_addr":"5.8.71.65:0","remote_ip":"5.8.71.65","status":302,"system":"http","time":"2022-02-10T06:33:06+08:00","uri":"/dbdump.sql","user_agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36","written_bytes":99}
116686 {"correlation_id":"AgGLQ7EsNla","duration_ms":15,"host":"47.98.XXX.XXX","level":"info","method":"GET","msg":"access","proto":"HTTP/1.1","referrer":"http://47.98.XXX.XXX/","remote_addr":"5.8.71.65:0","remote_ip":"5.8.71.65","status":302,"system":"http","time":"2022-02-10T06:33:06+08:00","uri":"/users","user_agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36","written_bytes":99}
116687 {"correlation_id":"hf6gtvSk4va","duration_ms":29,"host":"47.98.XXX.XXX","level":"info","method":"GET","msg":"access","proto":"HTTP/1.1","referrer":"\u003cinvalid URL\u003e","remote_addr":"5.8.71.65:0","remote_ip":"5.8.71.65","status":302,"system":"http","time":"2022-02-10T06:33:06+08:00","uri":"/cgi-bin/admin.cgi","user_agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36","written_bytes":99}
```

### 4.修复
- 设置更加安全策略
- 启用`用户和IP频率限制`
- 升级至安全版本: [Update GitLab](https://about.gitlab.com/update/)
- 官方修复补丁: [Hotpatch](https://forum.gitlab.com/t/cve-2021-22205-how-to-determine-if-a-self-managed-instance-has-been-impacted/60918/2)

```bash
sudo su
cd ~
curl -JLO https://gitlab.com/gitlab-org/build/CNG/-/raw/master/gitlab-ruby/patches/allow-only-tiff-jpeg-exif-strip.patch
cd /opt/gitlab/embedded/lib/exiftool-perl
patch -p2 < ~/allow-only-tiff-jpeg-exif-strip.patch
```