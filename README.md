# About Manifest File

## What should we declare in Manifest file?

### アプリコンポーネント
&nbsp;&nbsp;&nbsp; マニフェストファイルで記述するアプリコンポーネントは、クラスの名前などの基本プロパティを宣言する必要がある。必要であれば、そのコンポーネントが処理できるデバイス構成などの機能や、インテントフィルターも宣言できる。まず、アプリコンポーネントについて、マニフェストファイル内で、以下のタグを用いて宣言する。

- `<activity>` Activityのサブクラス
- `<service>` Serviceのサブクラス
- `<receiver>` BroadcastReceiverのサブクラス
- `<provider>` ContentProviderのサブクラス

&nbsp;&nbsp;&nbsp; 例えば、アクティビティのサブクラスである`MainActivity`は以下のように宣言される。基本、`name`プロパティでは、完全なパッケージ名（例：`com.example.myapp.MainActivity`）が必要になる。しかし、`name`プロパティの値がピリオドから始まる場合、`com.example.myapp`は自動で補完される。
```
<manifest ... >
    <application ... >
        <activity android:name=".MainActivity" ... >
            ...
        </activity>
    </application>
</manifest>
```

&nbsp;&nbsp;&nbsp; `name`プロパティ以外にも、インテントフィルターやアイコンとラベルも宣言できる。アイコンとラベルは、`icon`プロパティと`label`プロパティで設定される。これは、コンポーネントがインテントを満たすたびに、アプリアイコンとアプリの名前として、ユーザーに表示されるものである。

### 権限
&nbsp;&nbsp;&nbsp; アプリが要求する可能性のある権限は、以下のように`<uses-permission>`タグに権限を追加する。
```
<manifest ...>
    <uses-permission android:name="android.permission.CAMERA"/>
    <application ...>
        ...
    </application>
</manifest>
```
[このリンク](https://developer.android.com/reference/android/Manifest.permission)で、マニフェストファイルで宣言できる権限の一覧を確認できる。<br>

&nbsp;&nbsp;&nbsp; 上記のカメラの権限だけでは、カメラを搭載しているデバイスしかアプリのインストールが許可されない。このように[ハードウェアを使用する権限](https://developer.android.com/guide/topics/manifest/uses-feature-element#permissions)を追加する場合は、そのハードウェアの有無をオプションとして指定することで、そのハードウェアがなくてもアプリをインストールできるようにする。以下のように、`android:required`をfalseに設定することで、権限で必要なハードウェアをオプションに設定できる。
```
<manifest ...>
    <application>
        ...
    </application>
    <uses-feature android:name="android.hardware.camera"
                  android:required="false" />
<manifest>
```

&nbsp;&nbsp;&nbsp; 上記のようなハードウェア権限をオプションとした場合、そのハードウェアが搭載されているか、以下のように確認する必要がある。
```
if (applicationContext.packageManager.hasSystemFeature(
        PackageManager.FEATURE_CAMERA_FRONT)) {
} else {
}
```

&nbsp;&nbsp;&nbsp; ちなみに、`WRITE_EXTERNAL_STORAGE`のように、特定のAPIレベル以降では権限の要求が不要になることがあるので、その場合は以下のように宣言できる。
```
<uses-permission
android:name="android.permission.WRITE_EXTERNAL_STORAGE"
     android:maxSdkVersion="18" />
```

### デバイス互換性
&nbsp;&nbsp;&nbsp; 先ほど述べたように、ハードウェアに関する権限の場合は、`android:required`をfalseに設定することが推奨されている。

## File convensions
&nbsp;&nbsp;&nbsp; ここでは、マニフェストファイルの要素と属性に適用される慣習や規則について記す。

### 要素
&nbsp;&nbsp;&nbsp; 必須の要素は、`<manifest>`と`<application>`で、マニフェストファイルで1回しか使えない。[それ以外の要素](https://developer.android.com/guide/topics/manifest/manifest-intro#reference)は、マニフェストファイルで何回でも使えるし、使わなくてもいい。基本的に、要素は任意の順序で配置できる。例外として2つある。
- `<activity-alias>`要素は、エイリアスとなる`<activity>`の後に続ける必要がある。`<activity-alias>`要素は、同じアクティビティに期間別のアイコンを設定したい時に使う。
- `<application>`要素は、`<manifest>`要素のなかで、最後に設置しなければならない。

### 属性
&nbsp;&nbsp;&nbsp; 属性は基本的にオプションである。[以下のリンク](https://developer.android.com/guide/topics/manifest/manifest-element?hl=ja)で、マニフェスト要素の属性を、[以下のリンク](https://developer.android.com/guide/topics/manifest/activity-element?hl=ja)で、アクティビティ要素の属性を、[以下のリンク](https://developer.android.com/guide/topics/manifest/application-element?hl=ja)で、アプリケーション要素の属性を確認できる。

### 複数の値
&nbsp;&nbsp;&nbsp; 要素を複数追加できる場合、1つの要素にリスト化するのではなく、以下のように要素を必ず繰り返す必要がある。
```
<intent-filter ... >
    <action android:name="android.intent.action.EDIT" />
    <action android:name="android.intent.action.INSERT" />
    <action android:name="android.intent.action.DELETE" />
    ...
</intent-filter>
```

### リソース
&nbsp;&nbsp;&nbsp; リソースをマニフェストファイルで指定する際には、`"@[package:]type/name"`というように記述する必要がある。packageについては、アプリから提供されているものであれば省略可能である。例えば、drawableリソースを参照する際には、以下のように記述する。
```
<activity android:icon="@drawable/smallPic" ... >
```

## Reference
https://developer.android.com/guide/topics/manifest/manifest-intro