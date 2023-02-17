# Clash-for-ubuntu

A clash application for the ubuntu system.

Linux系统上挂代理（高手可绕道～，不喜误喷谢谢）

一共分为以下十一步：

1.下载Clash的Linux版本，网址：https://github.com/Dreamacro/clash/releases（或者直接下载该仓库，里面包含目前2022年12月份最新的Releases发行版本：clash-linux-amd64-v1.13.0.gz）

  打开网址会发现有一个386的版本，一个V3-的版本，其中386可以看作是x86位。
  
  或者通过terminal终端利用wget链接下载：wget https://github.com/Dreamacro/clash/releases/download/v1.13.0/clash-linux-amd64-v1.13.0.gz
  
2.在Dowload文件夹中找到该文件并进行解压，这里推荐用终端中的gzip -d 命令进行解压，不推荐直接右键click Extract Here

  gzip -d clash-linux-amd64-v1.13.0.gz
  
  继续在终端输入chmod +x clash-linux-amd64-v1.13.0对该文件赋予可被执行的权限，否则在后面move的过程中可能会报错... 
  
  PS：双击或右键解压文件名可能为clash-linux-amd64
  
3.使用命令移动文件到全局访问的目录：sudo mv clash-linux-amd64-v1.13.0 /usr/local/bin,方便进行全局访问

  在这里需要注意：更改文件名为clash（第4步要说到）
  
4.在终端执行clash -v查看clash是否下好，可以运行：

  ![image](https://user-images.githubusercontent.com/72930251/219655815-9cf496c8-d65c-4c9c-8bd0-567134bf46c8.png)
  
  如上图，才为正常显示
  
5.clash能够运行，并且可以访问google的前提是需要相应的.yaml文件，一般默认读取该位置：$HOME/.config/clash/config.yaml

  利用gedit命令或者vim命令，修改.yaml文件的内容，一般形式如下：
  
  ![image](https://user-images.githubusercontent.com/72930251/219657512-89c62543-3c8e-44ff-a544-c05a7629cbf5.png)
  
  当然，在终端直接运行clash的时候，第一次会默认生成一个.yaml文件模板，然后在网络通畅时还会下载Country,mmdb文件（该文件是一个二进制文件，用于存储IPv4和IPv6的数据信息的数据库）
  
6.在使用VPN下载.yaml文件并替换初始的.yaml模板之后，在终端运行：clash(没权限就加sudo)，这样会弹出：

  ![image](https://user-images.githubusercontent.com/72930251/219658307-421a703a-fdd0-4deb-b47d-2a1757371564.png)
  
  （这里有Eror是因为我已经设置开机自启动clash了，后面会讲到）
  
7.打开设置--->网络--->网络代理，切换为手动Manual，填入：

  ![image](https://user-images.githubusercontent.com/72930251/219659001-e708578e-f87c-421e-aaa6-2b9d38d1b7c6.png)
  
8.打开clash网页控制台，长这样：

  ![image](https://user-images.githubusercontent.com/72930251/219658560-d83b2fbd-ebb5-4f68-8b3c-56b49446b295.png)
  
  点击设置，打开允许来自局域网的连接，这时就可以访问Google
  
9.开机自启动：

在/etc/systemd/system下新建clash.service文件：sudo gedit /etc/systemd/system/clash.service，填入：

[Unit]

Description=Clash - A rule-based tunnel in Go

Documentation=https://github.com/Dreamacro/clash/wiki

[Service]

OOMScoreAdjust=-1000

ExecStart=/usr/local/bin/clash -f /root/.config/clash/config.yaml

Restart=on-failure

RestartSec=5

[Install]

WantedBy=multi-user.target

在这里可以将.config/clash/config.yaml路经改为clash下的位置：/usr/local/bin/config.yaml（前提是该位置下要有这个.yaml文件）

10.最后输入：

sudo systemctl enable clash
sudo systemctl start clash
sudo systemctl status clash

完成开机自启

11.采用以下命令设置并将其写入 .bashrc 文件中，使得每次开启终端都可以生效：

echo -e "export http_proxy=http://127.0.0.1:7890\nexport https_proxy=http://127.0.0.1:7890" >> ~/.bashrc

Plus 1+（可选）：
代理链Proxychains安装配置：

One. 使用 apt 进行安装：sudo apt-get install proxychains

Two. 打开 /etc/proxychains.conf 文件：sudo gedit /etc/proxychains.conf，在文件最后改成相应的代理方式、地址和端口，配置代理：http	127.0.0.1 7890

Three. 测试是否成功：proxychains curl -kIsS https://www.google.com
