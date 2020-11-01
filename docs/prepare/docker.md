# Dockerのインストール

本ドキュメントでは多くの場面でDockerを使った処理を多く行います。
そのため、事前にDocker及びdocker-composeを入れておく必要があります。
なお、Raspberry Piについては現時点ではDockerのみで行います。

## Ubuntu

apt-getコマンドでdockerをインストールし、サービスを起動します。

```bash
sudo apt-get install docker.io
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

最後にdocker-composeをインストールします。

```bash
sudo apt-get install curl
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

## Mac

[Docker desktop for Mac](https://www.docker.com/products/docker-desktop) をインストールします。

インストール後にターミナルでdockerが動くのを確認します。

```bash
docker run hello-world
```

docker-composeコマンドはDocker Desktopに含まれます。

## Raspberry Pi (Rasbian OS)

apt-getコマンドでdockerをインストールし、サービスを起動します。

```bash
sudo apt-get install docker.io
sudo service docker start
```

# ディスクサイズ対策

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
今回は `graph` という値を作成します。

```bash
sudo vim /etc/docker/daemon.json
cat /etc/docker/daemon.json
```

```json
{
  "graph": "/mnt/docker"
}
```

最後にDockerを再起動します。

```bash
sudo systemctl start docker
```
