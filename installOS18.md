hp ZBook studio g4 ubuntu16.04.5インストール (Windows とデュアルブート)
===


## Ubuntu18.04.5 Live media 作成　(できればUSB3.0が欲しい 2GBくらい) [windows]

1. BitTorrent.exe インストール https://www.bittorrent.com/lang/ja/　

1. ubuntu-16.04.5-desktop-amd64.iso.torrent のダウンロード　https://www.ubuntu.com/download/alternative-downloads　

1. LTS版　Ubuntu Desktop(64bit) ダウンロード

1. .torrent ファイルから.isoファイルをダウンロード (←ディスクイメージ)  
    (やり方　https://torrentnavi.com/?p=33)

1. Livemedia作成　https://kledgeb.blogspot.com/2014/04/ubuntu-1404-4-windowsusb.html　



## パーティション割り当て [windows]

1. Windowsボタン右クリック

1. [ディスクの管理]

1. C:ドライブ部分を右クリックしてボリュームの縮小

⁂ もっと減らしたかったらhttp://matasuke.asablo.jp/blog/2015/12/18/7952311


## BIOS設定 (作成したUSBを挿す)  

1. リブート

1. Boot画面でF10

1. セキュアブート/CSMサポート設定 (どこかにあるはず)

    |セキュアブート|CSMサポート||
    |-:-|-:-|-:-|
    |有効|有効|💀  死ぬ|
    |無効|有効|👼 おすすめ|
    |無効|無効|👼 大丈夫|

1. レガシーサポート有効化 (どっちでもいい)

1. 高速起動　無効化

    [詳細設定]タブ -> [ブートオプション] -> 高速起動

1. ブート順序設定

    [詳細設定]タブ -> [UFEIブート順序] -> USBを最上位に持ってくる

1. 設定を保存して終了



## Ubuntu　インストール

1. 起動したらubuntu installを選択  

1. 指示に従ってインストール

    1. 英語版にしておくとCUIコンソールで文字化けしない

    1. partisionの手動管理
        windows デュアルブートの場合は,nftsシステムパーティションがあるため,nfsシステムパーティションの設定をしなくていい
        Ubuntuのみの場合は、nfsシステムパーティションを戦闘に追加（128MBor256MB）
        /パーティションは50GB
        /homeパーティションは幾らでもOK

    1. その他の設定は自由 (最小構成でダウンロードOK, ドライバ関連はインストールしたほうがいい)


## hp ZBook  CUDAインストール [ubuntu]

### モノの入手

1. Cuda

    打つのしんどいからbashで保存→CUIログイン時に実行

1. Nouveau ドライバの停止　($ lsmod  でnouveauが出てきたら)

    1. テキストエディタでコレを作成。`/etc/modprobe.d/blacklist-nouveau.conf`

        ```
        $ blacklist nouveau
        $ blacklist lbm-nouveau
        $ options nouveau modeset=0
        $ alias nouveau off
        $ alias lbm-nouveau off
        ```

    1. nouveau関連のファイルを削除　($ dpkg -l | grep nouveau　で出てきたやつ)
    	yaranakute iikamo

            sudo apt purge xserver-xorg-video-nouveau

    1. Initlamfsを再構築して再起動

        ```bash
        $ sudo update-initramfs –u
        $ sudo reboot
        ```

    1. `$ lsmod` でnouveau が出てこないことを確認


## Cuda インストール & Nvidia driver アップデート


1. display managerを停止(Ubuntu18: gdm, Ubuntu16, centOS...: lightdm)
    ```bash
    $ sudo service gdm stop
    ```

1. Ctrl+Alt+F1 → Alt+F6でアカウント名とパスワード入力しCUIでログイン

1. install CUDA

    bash /path/to/install_cuda.sh

1. Cudnn実行

    ```bash
    $ sudo apt update

    # cuDNN　インストール
    $ sudo dpkg -i libcudnn7*入れたやつ全部*.deb

    # CUDA Profile tools　インストール
    $ sudo apt install cuda-command-line-tools
    ```

1. 環境変数の追加

    /home/user/.bashrc 最下行に以下を追加
    ```
    $ export PATH=${PATH}:/usr/local/cuda-9.0/bin
    ```
1. reboot

    ```bash
    $ sudo reboot
    ```

1. NVIDIAドライバの確認

    ```bash
    $ nvidia-smi
    $ nvcc –V
    ```


1. CUDA サンプル実行

    ```bash
    $ cuda-install-samples-9.0.sh ~/path/to/anywhere
    $ cd ~/path/to/anywhere/NVIDIA_CUDA-9.0_Samples/
    $ sudo make
    $ cd 2_Graphics/volumeRender
    $ 2_Graphics/volumeRender
    ```

Ubuntuインストール(デュアルブート)　http://joichi.dip.jp/wordpress/?p=1562

Ubuntuインストール(基本)　 https://kledgeb.blogspot.com/2016/04/ubuntu-1604-1-ubuntu-1604ubuntuubuntu.html

基本的なインストール方法　https://qiita.com/tanakatsu1080/items/c97c4ea3b1d349e2f718

GUIログインできなくなる事案　https://qiita.com/isishizuka/items/5420af0e8f6fca7aa1da

ドライバインストールが詳しい　https://qiita.com/lekto/items/e30ae79ccfb0ade84a3e

CUDA cuDNNインストール https://qiita.com/JeJeNeNo/items/b30597918db3781e20cf  

   https://gist.github.com/ashokpant/5c4e9481615f54af4025ab2085f85869  

https://qiita.com/kazetof/items/c2f8b2751d3ed9600a57


## Tensorflow

1. インストール

    ```bash
    $ pip –V
    $ pip3 -V
    $ pip install tensorflow-gpu
    $ pip3 install tensorflow-gpu
    ```
1. 動作確認

    ```bash
    $ python3

    >>> import tensorflow as tf

    >>> print(tf.__version__)

    >>>  

    >>> tf.test.is_gpu_available(

    cuda_only=False,
        min_cuda_compute_capability=None
    )

    >>> True
    ```

## Ros

http://wiki.ros.org/ja/kinetic/Installation/Ubuntu  

## Autoware

https://github.com/CPFL/Autoware/wiki/Source-Build  

## Opencv (やらなくていい。rosとは別でopenCVを使いたい場合のみ)

一応Ros インストール時にopencvもインストールされるが，python3系では使えないので，使えるように設定も含めてインストール

http://cyaninfinite.com/tutorials/installing-opencv-in-ubuntu-for-python-3/  

ここでRosインストール時に$PATHをいじっているので，python3系ではimportできない

.bashrcでPYTHONPASSにpython3.5のdist-packageのpassを，Rosインストール時に追加したPASSより下に追加して使えるようにしたい．

    $ Vi ~/.bashrc

で$PASSの編集（これやるとcatkin_init_workspaceでROSの環境構築できなくなります）vi使い方


## ROS(kinetic)の環境構築

1. yp-spur と　yp-spur-rosのクローン

    ```bash
    # ROSとは独立した，機械側の制御パッケージ
    $ git clone https://github.com/openspur/yp-spur
    # ROSと機械をつなぐパッケージ
    $ git clone https://github.com/openspur/ypspur_ros
    ```

1. catkinで workspaceの作成

    ```bash
    $ mkdir –p ~/catkin_ws/src
    $ cd ~/catkin_ws/src
    $ catkin_init_workspace
    $ cd ..
    $ catkin_make
    ```
　
1. PATHの設定

    ```bash
    $ echo “source ~/catkin_ws/devel/setup.bash” >>  ~/.bashrc
    # ROSのPATHが追加されているか確認
    $ echo $ROS_PACKAGE_PATH
    ```
動作確認

    ```bash
    $ roscore
    ```

## install WPS softs

1. サイトからamd64 linux版ダウンロード

    ```bash
    cd /path/to/wps_deb_file.deb
    sudo dpkg -i pkg.deb
    ```
1. フォント設定

    ```bash
    mkdir /path/to/fonts/
    git clone https://github.com/iamdh4/ttf-wps-fonts.git
    sudo fc-cache -vfs ttf-wps-fonts
    ```

## Atom setup for latex

1. latex ビルドパッケージインストール
    ```bash
    sudo apt install latexmk　texlive-latex-extra texlive-lang-japanese
    ```
1. atom　のパッケージインストールにて以下をインストール

    1. language-latex
    2. latex

        セットアップ
        * Engine - uplatex
        * Enable - SyncTex
        * Enable - Extended Build Mode
        * Output Format - pdf
        * PDF Producer - dvipdfmx

    3. latexer
    4. pdf-view


## Google drive

google-drive-ocamlfuse を使う。

```bash
# install
sudo add-apt-repository ppa:alessandro-strada/ppa
sudo apt-get update
sudo apt-get install google-drive-ocamlfuse

# create mount point
mkdir /home/user/mount/point
google-drive-ocamlfuse /home/user/mount/point

# use multiple account
google-drive-ocamlfuse -label [user2] /home/user/mount/point

# unmount
fusermount -u /home/user/mount/point
```

# setting ibus-mozc

```
sudo apt install ibus-mozc
```
1. reboot
1. set imput method to ibus
1. custom key binding
    composition - activate IME -> ctrl space
    precomposition - deactivate IME -> ctrl space
    composition - deactivate IME -> ctrl space
    compatible - deactivate IME -> ctrl space
