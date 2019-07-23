# config 配置组件
根据`NODE_ENV`从`config`目录里加载`.json`配置文件
* `default.json`默认加载
* `production.json`当`NODE_ENV`为`production`时
# awilix 依赖注入
# jest 测试框架

# 装饰器 Decorator语法
装饰器函数内`this`为修饰的对象
本质上利用了`Object.defineProperty`
### target 目标对象
* 作用在函数上，为修饰类的prototype
* 作用在类上，为类本身
### name 属性名称
修饰属性的名称
### descriptor 属性描述符
`configurable`
当且仅当该属性的 configurable 为 true 时，该属性描述符才能够被改变，同时该属性也能从对应的对象上被删除。默认为 false。

`enumerable`
当且仅当该属性的enumerable为true时，该属性才能够出现在对象的枚举属性中。默认为 false。

数据描述符同时具有以下可选键值：

`value`
该属性对应的值。可以是任何有效的 JavaScript 值（数值，对象，函数等）。默认为 undefined。

`writable`
当且仅当该属性的writable为true时，value才能被赋值运算符改变。默认为 false。
存取描述符同时具有以下可选键值：

`get`
一个给属性提供 getter 的方法，如果没有 getter 则为 undefined。该方法返回值被用作属性值。默认为 undefined。

`set`
一个给属性提供 setter 的方法，如果没有 setter 则为 undefined。该方法将接受唯一参数，并将该参数的新值分配给该属性。默认为 undefined。
# grpc
Google出品的rpc框架和库，client/stub与server之间数据，默认为[ProtoBuf](https://github.com/google/protobuf)(推荐使用[proto3](https://developers.google.com/protocol-buffers/docs/proto3))格式
```
service 服务名称 {
  rpc 函数名 (stream 请求类型) returns (stream 返回类型);
}
```
`stream`表示为流数据类型，如不为流数据类型，则不出现在结构定义中
### 加载proto
```
var PROTO_PATH = __dirname + '/../../protos/helloworld.proto';

var grpc = require('grpc');
var hello_proto = grpc.load(PROTO_PATH).helloworld;
```
### stub构建
Base case - No encryption/authentication
```
var stub = new hello_proto.Greeter('localhost:50051', grpc.credentials.createInsecure());
```
With server authentication SSL/TLS
```
var ssl_creds = grpc.credentials.createSsl(root_certs);
var stub = new hello_proto.Greeter('myservice.example.com', ssl_creds);
```
Authenticate with Google
```
// Authenticating with Google
var GoogleAuth = require('google-auth-library'); // from https://www.npmjs.com/package/google-auth-library
...
var ssl_creds = grpc.credentials.createSsl(root_certs);
(new GoogleAuth()).getApplicationDefault(function(err, auth) {
  var call_creds = grpc.credentials.createFromGoogleCredential(auth);
  var combined_creds = grpc.credentials.combineChannelCredentials(ssl_creds, call_creds);
  var stub = new hello_proto.Greeter('greeter.googleapis.com', combined_credentials);
});
```
Authenticate with Google using Oauth2 token (legacy approach)
```
var GoogleAuth = require('google-auth-library'); // from https://www.npmjs.com/package/google-auth-library
var ssl_creds = grpc.Credentials.createSsl(root_certs); // load_certs typically loads a CA roots file
var scope = 'https://www.googleapis.com/auth/grpc-testing';
(new GoogleAuth()).getApplicationDefault(function(err, auth) {
  if (auth.createScopeRequired()) {
    auth = auth.createScoped(scope);
  }
  var call_creds = grpc.credentials.createFromGoogleCredential(auth);
  var combined_creds = grpc.credentials.combineChannelCredentials(ssl_creds, call_creds);
  var stub = new hello_proto.Greeter('greeter.googleapis.com', combined_credentials);
});
```
### Proto3语法
```
syntax = "proto3";

message 消息名称 {
  字段约束 字段类型 字段名称 = 唯一编号;
}
```
字段约束
* `singular`0或1个数据
* `repeated`多个数据，包含0个

字段类型：
* double
* float
* int32
* int64
* uint32
* uint64
* sint32
* sint64
* fixed32
* fixed64
* sfixed32
* sfixed64
* bool
* string
* bytes

唯一编号：消息数据内，字段唯一编号，不重复，不复用
```
message Request {
  repeated SearchRequest requests = 1;
}

message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  repeated int32 result_per_page = 3;
}

```
导入proto语法
```
import "myproject/other_protos.proto";
```
枚举别名语法
```
enum EnumAllowingAlias {
  option allow_alias = true;
  UNKNOWN = 0;
  STARTED = 1;
  RUNNING = 1;
}

enum EnumNotAllowingAlias {
  UNKNOWN = 0;
  STARTED = 1;
  // RUNNING = 1;  // Uncommenting this line will cause a compile error inside Google and a warning message outside.
}
```
枚举保留语法
```
enum Foo {
  reserved 2, 15, 9 to 11, 40 to max;
  reserved "FOO", "BAR";
}
```
Options 可选项
* `java_package`指定生成java包的类
```
option java_package = "com.example.foo";
```
* `java_multiple_files`将每个类单独生成结构文件
```
option java_multiple_files = true;
```
* `java_outer_classname`指定外层java类名称(默认foo_bar.proto 会是 FooBar.java)
```
option java_outer_classname = "Ponycopter";
```
* `optimize_for`c++,java生成优化方式
  * `SPEED`
  * `CODE_SIZE`
  * `LITE_RUNTIME` 使用libprotobuf-lite轻量级库代替libprotobuf生成，适用于移动端
```
option optimize_for = CODE_SIZE;
```
* `cc_enable_arenas`c++生成启用Arena内存分配
* `objc_class_prefix`设置Objective-C生成类前缀
* `deprecated`弃用属性
```
int32 old_field = 6 [deprecated=true];
```
### 使用protoc生成
```
protoc --js_out=import_style=commonjs,binary:. messages.proto base.proto
```
源码地址：https://github.com/google/protobuf 
发布包地址：https://github.com/google/protobuf/releases
### api文档
* [nodejs](https://grpc.io/grpc/node/index.html)
* [golang](https://godoc.org/google.golang.org/grpc)
