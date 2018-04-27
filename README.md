# AndroidStudio3.1を認証ありproxy環境下で動かす方法
 *How to build Android Project with Android Studio 3.1 under proxy environment.*
 
 
今回の方法で無理だった場合は[Android Studio 3.1 : updating gradle file cause android studio to freeze](https://stackoverflow.com/questions/49516880/android-studio-3-1-updating-gradle-file-cause-android-studio-to-freeze)の解決策をやってみて（未確認）．


**動作確認環境**   
・windows10   
・AndroidStudio 3.1.2   
・gradle 4.4 (AndroidStudioのダウンロード時に落ちてきたバージョン)

# agenda
- [AndroidStudioのproxy設定](https://github.com/Veget-ables/proxy_setting_AndroidStudio3.1#androidstudio%E3%81%AEproxy%E8%A8%AD%E5%AE%9A)
- Gradleのproxy設定
   - [従来のやり方](https://github.com/Veget-ables/proxy_setting_AndroidStudio3.1#%E5%BE%93%E6%9D%A5%E3%81%AE%E3%82%84%E3%82%8A%E6%96%B9)
   - [AndroidStudio3.1以降のやり方](https://github.com/Veget-ables/proxy_setting_AndroidStudio3.1#androidstudio31%E4%BB%A5%E9%99%8D%E3%81%AE%E3%82%84%E3%82%8A%E6%96%B9)
- Extra
   - [proxyの設定を戻す](https://github.com/Veget-ables/proxy_setting_AndroidStudio3.1#proxy%E3%81%AE%E8%A8%AD%E5%AE%9A%E3%82%92%E6%88%BB%E3%81%99)
   - [AndroidStudioを最新にバージョンアップ](https://github.com/Veget-ables/proxy_setting_AndroidStudio3.1#androidstudio%E3%82%92%E6%9C%80%E6%96%B0%E3%81%AB%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3%E3%82%A2%E3%83%83%E3%83%97)

## AndroidStudioのproxy設定
これはAndroidStudioのバージョンに限らず共通の設定．
```File > Settigs > Appearance & Behavior > System Settings > HTTP Proxy```から
Manual proxy configuration > Proxy authenticationの順にチェックを入れ，画像の箇所を入力する．値は適宜環境に合わせて入力．

![AndroidStudioのproxy設定](https://github.com/Veget-ables/proxy_setting_AndroidStudio3.1/blob/master/screenshot/project_http_proxy.png "AndroidStudioのproxy設定")

入力部下の```Check connection```でAndroidStudioからHTTP通信が成功するとOK.

## Gradleのproxy設定
ビルドツールのGradle側でproxy設定が必要である．   
AndroidStudioのバージョン3.1では従来のproxy設定のままでは失敗してしまう．今回，従来とバージョン3.1のproxy設定の方法を記す．

### 従来のやり方
バージョン3.1以前でプロジェクトを認証ありproxy環境下でビルドする場合，プロジェクト直下のgradle.propertiesに画像のパラメータを追加．値は適宜環境に合わせて入力．


![プロジェクト直下のgradle.properties](https://github.com/Veget-ables/proxy_setting_AndroidStudio3.1/blob/master/screenshot/project_gradle.properties.png "プロジェクト直下のgradle.properties")

これでproxy環境下でもビルドが通るようになる．


### AndroidStudio3.1以降のやり方

3.0 -> 3.1にアップデートされてから，従来の方法ではうまくいかなくなった．

**解決方法は[こちら](https://docs.gradle.org/current/userguide/build_environment.html)，次の記述．** 

> Setting up a consistent environment for your build is as simple as placing these settings into a gradle.properties file. 
> The configuration is applied in following order (if an option is configured in multiple locations the last one wins):

> 1. ```gradle.properties``` in project root directory.

> 2. ```gradle.properties``` in ```GRADLE_USER_HOME``` directory.

> 3. system properties, e.g. when ```-Dgradle.user.home``` is set on the command line.   


上記より，gradleは3つを順に読み込んでいき，読み込まれた変数は更新されていく（従来のやり方では```1.gradle.properties```で値を代入していた）．

そこで，1の後に呼ばれる```2.gradle.propeties```の方にproxyのパラメータを追加する．
![ルート直下のgradle.properties](https://github.com/Veget-ables/proxy_setting_AndroidStudio3.1/blob/master/screenshot/root_gradle_properties.png "ルート直下のgradle.properties")

↑ このファイルを好きなエディタで編集 ↓

![ルート直下のgradle.properties2](https://github.com/Veget-ables/proxy_setting_AndroidStudio3.1/blob/master/screenshot/root_gradle_properties2.png "ルート直下のgradle.properties2")

ファイルを更新したらAndroidStudioからリビルドする．するとビルドが通る．

## Extra
### proxyの設定を戻す
proxy環境下以外の場合は，上の設定を残したままだとエラーになるので，proxyの設定を消す必要がある．前節で```2.gradle.properties```を更新するとAndroidStudio上にgradle.properties(Global Properties)が現れる（元からあった？）ので，そこでproxyの記述をコメントアウトすればproxyがないところでもビルドが通る．

![ルート直下のgradle.properties3](https://github.com/Veget-ables/proxy_setting_AndroidStudio3.1/blob/master/screenshot/root_gradle_properties3.png "ルート直下のgradle.properties3")

### AndroidStudioを最新にバージョンアップ
```Help > Check for Updates``` でワークスペースを維持したまま最新版を入手できる．

