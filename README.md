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

このprotocコマンドを叩くことで、先程入れた protoc-gen-go を使ってコンパイルを行う

`$ brew install protobuf && brew upgrade  protobuf`


## サンプルプログラムを動かす

ディレクトリ構成は下記のとおり

```
├── README.md
├── greeter_client
│   └── main.go
├── greeter_server
│   └── main.go
└── helloworld
    ├── helloworld.pb.go
    └── helloworld.proto
```

1. サンプルをワークディレクトリにコピー

   `$GOPATH/src/google.golang.org/grpc/examples/helloworld`
   
   の内容を自分のワークディレクトリにコピーしてきて作業するために
   
   下記コマンドでサンプルを持ってくる
   
   `$ cp -r $GOPATH/src/google.golang.org/grpc/examples/helloworld $GOPATH/src/grpc-tutorial`

1. 参照する `.pb.go` ファイルを変更

   サンプルの `helloworld.pb.gp` を見に行くようになっているのを
   
   ワークディレクトリ内の `helloworld.pb.gp` を見に行くように修正
   
   ```go:greeter_server/main.go
   // greeter_server/main.go
   
   import (
      "context"
      "log"
      "net"

      "google.golang.org/grpc"
      pb "../helloworld"
   )
   ```

   ```go:greeter_client/main.go
   // greeter_client/main.go
   
   import (
      "context"
      "log"
      "os"
      "time"
      
      "google.golang.org/grpc"
      pb "../helloworld"
   )
   ```
   

1. Server起動

   `$ go run greeter_server/main.go`
    
1. Client起動

   Serverを起動したターミナルとは別のターミナルを使用する

   `$ go run greeter_client/main.go`

   実行時に下記のとおり表示されればOK
    
   ```
   2019/04/09 21:40:36 Greeting: Hello world
   ```

## 新しい Service を追加

Service：REST APIにおけるエンドポイントに似たもの

1. Serviceに定義追加

   下記のとおり、 Greeter Service に `SayHelloAgain()` を定義
   
   ```proto:helloworld.proto
   // helloworld/helloworld.proto
   
   service Greeter {
     // Sends a greeting
     rpc SayHello (HelloRequest) returns (HelloReply) {}
     // Sends another greeting
     rpc SayHelloAgain (HelloRequest) returns (HelloReply) {}
   }
   ```
   
1. gRPCのコードを生成

   下記コマンドを実行することで `helloworld.pb.go` が生成される

   `$ protoc -I helloworld/ helloworld/helloworld.proto --go_out=plugins=grpc:helloworld`
   
   各オプションの意味は下記のとおりであるため、

   ```
   ・Iオプションで.protoファイルのimportで参照するルートディレクトリを指定する
   ・go_out=オプションで出力先を変更する
   ```
   [参照](https://qiita.com/lufia/items/bcdb5081ddc10af50d8a#protoc-gen-go%E3%81%AE%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3)

   `$ protoc helloworld/helloworld.proto --go_out=plugins=grpc:.`

   でも同じ結果を得ることができる

1. `SayHelloAgain()`の処理実装

   ```go:greeter_server/main.go
   // greeter_server/main.go
   
   // SayHelloAgain implements helloworld.GreeterServer
   func (s *server) SayHelloAgain(ctx context.Context, in *pb.HelloRequest) (*pb.HelloReply, error) {
       return &pb.HelloReply{Message: "Hello again " + in.Name}, nil
   }
   ```
   
1. Clientからのリクエスト追加

   ```go:greeter_client/main.go
   // greeter_client/main.go
   
   r, err = c.SayHelloAgain(ctx, &pb.HelloRequest{Name: name})
   if err != nil {
       log.Fatalf("could not greet again: %v", err)
   }
   log.Printf("Again Greeting: %s", r.Message)
   ```
1. 実行

   `$ go run greeter_server/main.go`

   `$ go run greeter_client/main.go`
   
   実行して下記の通り表示されればOK
   
   ```
   2019/04/09 22:25:30 Greeting: Hello world
   2019/04/09 22:25:30 Again Greeting: Hello again world
   ```
