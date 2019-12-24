# Stream を学習する

## Stream とは

Stream については[この記事](https://qiita.com/kabochapo/items/8738223894fb74f952d3)を見てもらうとわかりやすいです．
基本的には，ざっくり以下のような感じが掴めていれば，理解できると思います．

- sink というものがあって
- sink に何かしらを入れて(add)
- sink に入れたものを加工して(transform)
- それをstreamから取り出して(listen)，処理する

## Stream について，コードを書きながら学ぶ

### 基本的な流れを学ぶ

- [DartPad](https://dartpad.dartlang.org/)を使います

- コードを全て削除します
- sink に add します

```dart
import 'dart:async';

class Cake {

}
class Order {
    String type;
    Order(this.type);
}

void main() {
    final controller = new StreamController();

    final order = new Order('banana');
    // sinkに追加
    controller.sink.add(order);
}
```

- StreamTransformer を追加します
  - StreamTransformer は sink に流れてきたデータを加工するためのものです．
- 以下で使用している map は何をしているでしょうか?調べてみましょう．

```dart
void main() {
    final controller = new StreamController();

    final order = new Order('banana');
    // データ加工の内容
    final baker = new StreamTransformer.fromHandlers(
        handleData: (cakeType, sink) {
            if (cakeType == 'chocolate') {
                sink.add(new Cake());
            } else {
                sink.addError('I cant bake that type!!');
            }
        }
    );
    controller.sink.add(order);
    // 実際にtransformerを適用
    controller.stream
        .map((order) => order.type)
        .transform(baker)
}
```

- listen することで，データに応じて print していきます

```dart
void main() {
    final controller = new StreamController();

    final order = new Order('banana');
    // データ加工の内容
    final baker = new StreamTransformer.fromHandlers(
        handleData: (cakeType, sink) {
            if (cakeType == 'chocolate') {
                sink.add(new Cake());
            } else {
                sink.addError('I cant bake that type!!');
            }
        }
    );
    controller.sink.add(order);

    controller.stream
        .map((order) => order.type)
        .transform(baker)
        //listenします
        .listen(
            (cake) => print('Heres your cake $cake'),
            onError: (err) => print(err)
        );
}
```

- RUN してみましょう
- その後，order のコンストラクタ引数を`chocolate`に変えてみましょう

### 実際に Stream を利用してプチゲームを作る

- まず HTML を少しだけ書きます

```html
<button>Click Me!</button>
```

- Dart に戻って書いていきます
- Dart は js のように使うこともできます
- なぜonClickでlistenを使うことができるのでしょう？

```dart
import 'dart:html';

void main() {
    final ButtonElement button = querySelector('button');

    button.onClick
        .timeout(
            new Duration(seconds: 1),
            onTimeout: (sink) => sink.addError('You lost!!!')
        )
        .listen(
            (event) {},
            onError: (err) => print(err)
        );
}
```
次は134. Why Strems?からß
