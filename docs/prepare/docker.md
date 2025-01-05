# Dockerのインストール

本ドキュメントでは多くの場面でDockerを使った処理を多く行います。
そのため、事前にDocker及び Docker Compose V2 を入れておく必要があります。

## Ubuntu or Debian

aptコマンドでdockerをインストールし、サービスを起動します。

```bash
sudo apt install docker.io
sudo systemctl start docker
```

次にdocker自体が動作するかを確認します。

```bash
sudo docker run hello-world
```

次に一般ユーザで作業をするためにdockerグループにユーザを追加します。

```bash
sudo usermod -aG docker $USER
```

これで一旦リブートをして再度ログインして、一般ユーザで動作するかを確認します。

```bash
docker run hello-world
```

最後に Docker Compose V2 をインストールします。

```bash
sudo apt install curl
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v2.32.0/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
```

## Mac

[Docker desktop for Mac](https://www.docker.com/products/docker-desktop) をインストールします。

インストール後にターミナルでdockerが動くのを確認します。

```bash
docker run hello-world
```

Docker Compose V2 プラグインはDocker Desktopに含まれます。

## [Raspberry Pi (Raspberry Pi OS)](https://www.raspberrypi.com/software/)

aptコマンドでdockerをインストールし、サービスを起動します。

```bash
sudo apt install docker.io
sudo service docker start
```

次にdocker自体が動作するかを確認します。

```bash
sudo docker run hello-world
```

## ディスクサイズ対策

クラウドなどの構成でrootデバイスに対して空き容量が足りない場合は、Dockerイメージを保存する場所を変更する必要があります。

まずはDockerを停止します。

```bash
sudo systemctl stop docker
```

今回は例として `/mnt` 上にDockerのスペースとなるディレクトリを作成します。

```bash
sudo mkdir /mnt/docker
```

次に、`/mnt/docker`にディスクをマウントしてフォーマットなどをします。

次に `/etc/docker/daemon.json` を作成します。
このファイルは基本となる設定から追加となる設定のみを記述します。
今回は `data-root` という値を作成します。

```bash
sudo vim /etc/docker/daemon.json
cat /etc/docker/daemon.json
```

```json
{
  "data-root": "/mnt/docker"
}
```

最後にDockerを再起動します。

```bash
sudo systemctl start docker
```
