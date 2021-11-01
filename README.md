# logrotate-for-docker-containers
logrotate-for-docker-containers は、logrotateを利用し、Docker コンテナ ログファイル のクリーンアップを行い、ログの肥大化を防ぐための設定ファイルです。    
クラウド環境でもそうですが、特にエッジコンピューティング環境においては、リソース制約に耐えるため、こうしたログファイルのクリーンアップが重要になります。    

## 仕様
logrotate-for-docker-containers は、ポッドのログの出力先である /var/lib/docker/containers/*/ にある *.log に対して、一定のファイルサイズを超えた場合に、logrotateが実行されます。削除対象となるログファイルは直ちに削除はされず、 *.log.1 というようにリネームされ、ファイルで設定されている数までローテーションが行われたあと、削除されます。

## 動作環境
logrotate-for-docker-containers は、以下の動作環境を前提としています。

- OS: Linux OS
- CPU: ARM/AMD/Intel

## 設定
1. 前提
    前提として、logrotateがインストールされていることを確認してください。

2. ファイルの配置
    logrotate-for-docker-containers ファイルを以下のディレクトリに配置してください。
    ``` 
    /etc/logrotate.d 
    ```

3. 動作確認
    以下のコマンドを実行して、logrotateの実行ファイルとして認識されているか確認してください。
    ```
    logrotate-for-docker-containers 単体で稼働するかをテストするコマンド
    sudo logrotate -dv /etc/logrotate.d/logrotate-for-docker-containers  2>&1 | less

    出力例
    rotating pattern: /var/lib/docker/containers/*/*.log  104857600 bytes (3 rotations)
    empty log files are rotated, old logs are removed
    switching euid to 0 and egid to 114
    considering log /var/lib/docker/containers/xxxxxxxxx/xxxxx-json.log
    Creating new state
    Now: 2021-10-28 10:00
    Last rotated at 2021-11-01 10:00
    log does not need rotating (log size is below the 'size' threshold)

    logrotate が logrotate-for-docker-containers を呼び出し、実行しているかをテストするコマンド
    sudo logrotate -dv /etc/logrotate.conf  2>&1 | less

    ```

## 設定詳細
logrotate-for-docker-containers は、以下の設定がされています。
```
/var/lib/docker/containers/*/*.log  # 実行の対象となるログファイル {
        rotate 3                    # ローテーションする回数
        size 100M                   # logrotateの実行対象とするファイルサイズ 
        copytruncate                # 実行の対象となるログファイルをコピーし、その対象となるファイルを切り詰める処理を行う
}
```