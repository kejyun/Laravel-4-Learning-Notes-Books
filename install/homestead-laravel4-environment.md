# Homestead 測試環境

常常我我們需要開發Web Application時候，都需要花很長的時間把環境建置起來，而Homestead以讓我們透過簡單的設定檔，輕鬆的建置好整個執行環境，讓我們能夠更專注於在開發上

> Ubuntu 14.04 64 bit

> Homestead 2.0

> Laravel 4.2

> Vagrant 1.7.1

> Virtualbox 4.3

# 安裝 VirtualBox

從[VirtualBox官方網站](https://www.virtualbox.org/wiki/Linux_Downloads)下載並安裝VirtualBox

```shell
$ wget http://download.virtualbox.org/virtualbox/4.3.20/virtualbox-4.3_4.3.20-96996~Ubuntu~raring_amd64.deb
$ sudo dpkg -i  virtualbox-4.3_4.3.20-96996~Ubuntu~raring_amd64.deb
```

> 64位元請下載amd64版本，32位元請下載i386版本

![下載Virtualbox](http://3.bp.blogspot.com/-duCO50hJkII/VKFI2hGBLGI/AAAAAAAADdU/ICI8n6Nkgx8/s1600/Homestead-install-virtualbox.png)

# 安裝 Vagrant

從[Vagrant官方網站](http://www.vagrantup.com/downloads.html)下載並安裝

```shell
$ wget https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.1_x86_64.deb
$ sudo dpkg -i vagrant_1.7.1_x86_64.deb
```

# 使用Git複製Laravel homestead並建立Homestead設定檔
```shell
$ git clone https://github.com/laravel/homestead.git Homestead
$ cd Homestead
~/Homestead$ bash init.sh
```
> Ubuntu目前(2014/12/29)尚未支援使用Composer下載並安裝Homestead的方式，所以只能先用Git下載的方式去建立Homestead

# 建立ssh key
```shell
$ ssh-keygen -t rsa -C "kejyun@homestead"
```

# 編輯Homestead設定檔
```shell
$ vim  ~/.homestead/Homestead.yaml
```
確認設定檔中ssh key有對應到正確的路徑
```
authorize: ~/.ssh/id_rsa.pub
keys:
    - ~/.ssh/id_rsa
```

## 設定本機程式碼路徑對應到測試環境的路徑

> map：本機路徑

> to：Vagrant測試機路徑


### 程式路徑

```
folders:
    - map: ~/Code
      to: /home/vagrant/Code
    - map: ~/laravel42
      to: /home/vagrant/laravel42
```

### 網站路徑

```
sites:
    - map: homestead.app
      to: /home/vagrant/Code/Laravel/public
    - map: kejyun.app
      to: /home/vagrant/laravel42/public
```

![Homestead設定檔](http://3.bp.blogspot.com/-AaT61pAN6d4/VKFUCA6N6PI/AAAAAAAADdk/xBV-hIObVDM/s1600/Homestead-setting.png)


# 設定hosts對應本機網址到Vagrant

```shell
$ sudo vim /etc/hosts
```
加入下列設定
```
192.168.10.10   homestead.app
192.168.10.10   kejyun.app
```

> IP位址為Homestead.yaml設定中的ip設定值，homestead.app及kejyun.app則是參照設定檔中的sites map設定有哪些則加進去hosts設定檔中

# 啟動Vagrant

```shell
~/Homestead$vagrant up
```

> 在剛剛使用Git複製下來的homestead資料夾中執行vagrant up指令，會自動下載virtualbox相關的虛擬主機設定，並依照設定檔設置虛擬主機。

![啟動Homestead Vagrant](http://3.bp.blogspot.com/-kEQkCQ-A-fM/VKFinb4BidI/AAAAAAAADd0/okz4l-O8XGs/s1600/Homestead-up.png)


設定完成後可以開啟瀏覽器到[http://homestead.app](http://homestead.app)及[http://kejyun.app](http://kejyun.app)看看Homestead是否有正常啟動，若看到下列畫面表示可以正常運作了！！！

![Homestead測試](http://3.bp.blogspot.com/-YJqaIj2I9nk/VKFjhkkpcuI/AAAAAAAADd8/FGK0oxXc5WA/s1600/Homestead-test.png)


# 備註
若要關閉Homestead虛擬機器，則可以使用下列指令關閉

```shell
$ vagrant halt
```

若設定檔有修改要重新讀取，則可以使用下列指令重新讀取設定

```shell
$ vagrant provision
```

## 參考資料
* [在 Ubuntu 14.04 使用 Homestead 建置 Laravel 4 開發環境](http://blog.kejyun.com/2014/12/Laravel-4-environment-with-Homestead-on-Ubuntu14.04.html)
* [Say Hello to Laravel Homestead 2.0](https://laracasts.com/lessons/say-hello-to-laravel-homestead-two)
* [Laravel - Homestead](http://laravel.com/docs/4.2/homestead)
* [Virtualbox](https://www.blogger.com/)
* [Vagrant](http://www.vagrantup.com/downloads.html)
* [Vagrant 常用指令](http://blog.kejyun.com/2014/10/vagrant-command.html)
