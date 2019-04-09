# gRPC 入門

- [参考資料1](https://grpc.io/docs/quickstart/go.html)
- [参考資料2](https://qiita.com/yasuno0327/items/625c18de44152d6bfc1b)


# 手順

## セットアップ

- gRPC プラグインをインストール

`$ go get -u google.golang.org/grpc`

- protoc プラグインをインストール

`$ go get -u github.com/golang/protobuf/protoc-gen-go`

- Mac に protoc をインストール
このprotocコマンドを叩くことで↑で入れた protoc-gen-go を使用してコンパイルを行う
`$ brew install protobuf && brew upgrade  protobuf`


## サンプルプログラムを動かす

1. サンプルディレクトリに移動

   `$ cd $GOPATH/src/google.golang.org/grpc/examples/helloworld`

1. Server起動

   `go run greeter_server/main.go`
    
1. Client起動

   Serverを起動したターミナルとは別のターミナルを使用する

   `go run greeter_client/main.go`

   実行時に下記のとおり表示されればOK
    
   ```
   2019/04/09 21:40:36 Greeting: Hello world
   ```

## サンプルプログラムの動き解説

1. Serviceに定義追加

   下記のとおり、 Greeter Service に `SayHelloAgain()` を定義
   
   ```proto
   service Greeter {
     // Sends a greeting
     rpc SayHello (HelloRequest) returns (HelloReply) {}
     // Sends another greeting
     rpc SayHelloAgain (HelloRequest) returns (HelloReply) {}
   }
   ```
   
1. gRPCのコードを生成

   ``
