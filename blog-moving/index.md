# 更换博客虚拟空间


## VPS 到期

原来的 VPS 到期了，呵呵，搬到个免费的虚拟空间上面，原来的有些忘了备份，有些附件丢失了。

免费的其实还挺稳定的，就是速度非常悲剧，LNMP 一键包下载安装花了快 1 个半小时。

太晚了，刚喝酒回来，头晕，手指也不知道什么时候被割伤了，不折腾了，先睡了。

等明天有空网上翻下找个自动备份脚本，暂时就这样吧。。。

* * *

## 接昨晚上继续

网上翻了几个自动备份到 dropbox 的脚本，貌似不成功。
直接自动备份到 ftp 空间，脚本一搜一大把。随便找了一个用上了。 (脚本在最下面，hostloc 猫版的，站貌似打不开了，我记录下。）设置定时执行就行。

## 自动备份数据上传至ftp空间脚本

```bash
    #!/bin/bash
    #你要修改的地方从这里开始
    MYSQL_USER=root                             #mysql用户名
    MYSQL_PASS=123456                      #mysql密码
    MAIL_TO=cat@hostloc.com                 #数据库发送到的邮箱
    FTP_USER=cat                              #ftp用户名
    FTP_PASS=123456                         #ftp密码
    FTP_IP=imcat.in                          #ftp地址
    FTP_backup=backup                          #ftp上存放备份文件的目录,这个要自己得ftp上面建的
    WEB_DATA=/home/www                          #要备份的网站数据
    #你要修改的地方从这里结束

    #定义数据库的名字和旧数据库的名字
    DataBakName=Data_$(date &#43;&#34;%Y%m%d&#34;).tar.gz
    WebBakName=Web_$(date &#43;%Y%m%d).tar.gz
    OldData=Data_$(date -d -5day &#43;&#34;%Y%m%d&#34;).tar.gz
    OldWeb=Web_$(date -d -5day &#43;&#34;%Y%m%d&#34;).tar.gz
    #删除本地3天前的数据
    rm -rf /home/backup/Data_$(date -d -3day &#43;&#34;%Y%m%d&#34;).tar.gz /home/backup/Web_$(date -d -3day &#43;&#34;%Y%m%d&#34;).tar.gz
    cd /home/backup
    #导出数据库,一个数据库一个压缩文件
    for db in `/usr/local/mysql/bin/mysql -u$MYSQL_USER -p$MYSQL_PASS -B -N -e &#39;SHOW DATABASES&#39; | xargs`; do
        (/usr/local/mysql/bin/mysqldump -u$MYSQL_USER -p$MYSQL_PASS ${db} | gzip -9 - &gt; ${db}.sql.gz)
    done
    #压缩数据库文件为一个文件
    tar zcf /home/backup/$DataBakName /home/backup/*.sql.gz
    rm -rf /home/backup/*.sql.gz
    #发送数据库到Email,如果数据库压缩后太大,请注释这行
    echo &#34;主题:数据库备份&#34; | mutt -a /home/backup/$DataBakName -s &#34;内容:数据库备份&#34; $MAIL_TO
    #压缩网站数据
    tar zcf /home/backup/$WebBakName $WEB_DATA
    #上传到FTP空间,删除FTP空间5天前的数据
    ftp -v -n $FTP_IP &lt;&lt; END
    user $FTP_USER $FTP_PASS
    type binary
    cd $FTP_backup
    delete $OldData
    delete $OldWeb
    put $DataBakName
    put $WebBakName
    bye
    END
```
完！

---

> 作者: [pagezen](http://clearsky.me/)  
> URL: https://clearsky.me/blog-moving/  

