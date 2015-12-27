### Creating a Background Service

:eyes: http://developer.android.com/intl/ja/training/run-background-service/create-service.html

IntentServiceは1つのスレッドで1つのバックグラウンドジョブを行うときに使いやすい構成をしているクラス。
このクラスで処理を行うことでUIの応答に影響を与えずに処理を行える。

また、IntentServiceはActivityなどのUIのインスタンスのライフサイクルイベントに影響を受けない。
なので、AsyncTaskが終了させられてしまうような局面でも使うことが出来る

IntentServiceの持っている制限は

- 直接UIとやりとりすることはできない。UIと結果をやり取りしたい場合は、結果をActivityに送る必要がある
  - どうやって??
- 仕事のリクエストは順次なされる。つまり、1つめのリクエストが終るまで、次のリクエストを送っていたとしても、そのリクエストは開始されない
- IntentServiceの中で実行されている仕事は中断できない

ほとんどのケースでは、単一のバックグラウンドジョブを行うにはIntentServiceは良い方法である

レッスンでは、下記の事項を扱う。

- IntentServiceのサブクラスを作る
- どのようにコールバックメソッド(onHandleIntent())を作るか
- どのようにManifestファイルにIntentServiceを宣言するか

#### Create an IntentService

IntentServiceを作るには、IntentServiceを継承して、その中で`onHandleIntent()`メソッドをオーバーライドする。

```java
public class RSSPullService extends IntentService {
  @Override
  protected void onHandleIntent(Intent workIntent) {
    // Gets data from the incoming point
    String dataString = workIntent.getDataString();
    ...
    // Do work here, based on the contents of dataString
    ...
  }
}
```

注意点として、通常のServiceコンポーネントとしての他のコールバック--例えば`onStartCommand()`--はIntentServiceによって自動的によきに呼び出されるので、IntentServiceを利用する時はこれらをオーバーライドしてはいけない。

#### Define the IntentService in the Manifest

IntentServiceはManifestファイルの中にエントリを必要とする。Manifestファイル内に<application>要素の子要素として、<service>要素として記述する。

```xml
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name">
        ...
        <!--
            Because android:exported is set to "false",
            the service is only available to this app.
        -->
        <service
            android:name=".RSSPullService"
            android:exported="false"/>
        ...
    <application/>
```

`android:name`に書くクラス名は`IntentService`のサブクラスのものにすること。

また、service要素はIntentFilterを含まないことに注意すること。つまり、ServiceにActivityから仕事をリクエストするときは、明示的Intentを使うので、IntentFilterは必要ないのである。

これはまた、service要素はそのアプリケーションのアプリケーションIDを知っているアプリか、そのアプリからしかserviceにアクセスさせないようにする、ということでもある。

さて、これでIntentを使って仕事のリクエストが送れるIntentServiceクラスが出来た。Intentをどう作って送るかは次のレッスンだ。
