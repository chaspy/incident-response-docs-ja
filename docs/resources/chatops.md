---
cover: assets/img/covers/chatops.png
description: Throughout this documentation references are made to various chat commands, all starting with an exclamation (e.g. 'ic page'). We have bots running in our Slack rooms which watch for these commands and execute various actions for us when they're detected. This page gives an overview of the commands we've referenced in this documentation, and what they do behind the scenes.
---

このドキュメント全体を通して、"!"で始まる様々なチャットコマンドを参照しています（`!ic page`）。
これらのコマンドはSlack上のBotで実行し、私たちが問題を検出した時にこのコマンドを監視しています。
このページでは、このドキュメントで利用したコマンドの概要と、そのコマンドが実行する処理について紹介します。

!!! question "これらのBotはオープンソースですか？"
    私たちが利用しているチャットプラグインとBotは、現在は残念がらこれらのBotはオープンソース化されていません。
    しかし将来、それらをオープンソース化して、皆さんが利用できるようにしたいと考えています。
    それまでの間は、このページにある説明が、独自のツールで同じ機能を再現するのに役立つのを願います。

## インシデント対応

`!ic` コマンドは、して体の様々なオンコールスケジュールで、PagerDuty APIをポーリングします。
現在のオンコールユーザーの名前と連絡先をキャッシュして、APIリクエストに問題が発生しても、問題ありません。


### `!ic`

このコマンドは現在のオンコールのインシデントコマンダーと、彼らの電話番号とユーザーをページする方法を通知する方法を表示します。

![Incident Commander List](../assets/img/chatops/ic.png)

### `!ic page`

このコマンドはインシデント対応プロセスを手動でトリガーします。
スケジュール情報をキャッシュし、現在のオンコールを担当するインシデントコマンダー（プライマリとバックアップ、トレーニングでシャドウイングする人）をページします。
また何らかの理由でページできない場合はSlackループに通知し、ユーザーに連絡先を表示して手動で電話をかけられるようにします。
さらに新しいPagerDutyインシデントを作成して、ユーザーを関連付けます。

This is the command we use to manually trigger our incident response process.
It uses the cached schedule information and will page all of the current
Incident Commanders on-call (the primary, the backup, and any trainees who are
shadowing). It will also notify into the room if it is unable to page them for
any reason, and direct users to manually call the person using their contact
information. Additionally, it will create a new PagerDuty incident and link
users to it.

![Paging Incident Commanders](../assets/img/chatops/ic_page.png)

何らかの理由でインシデントコマンダーを自動で呼べなかった場合は、Botは失敗を検知します。
関連するメンバーの電話番号を表示して、手動で連絡できるようにします。
テスト用のBotの出力結果を示します。
API経由でPagerDutyで呼び出せなかった場合をシミュレートしています。

![Testing for Failure](../assets/img/chatops/test_for_failure.png)

### `!ic responders`

これは `!ic` コマンドと同じように機能しますが、インシデントコマンダーだけでなく、チーム全員に対する情報を表示します。
それぞれのチームの、現在オンコール担当である人たちが一覧表示されます。
これは一時的に誰がインシデントの通話に参加するかを知るのに便利です。

![Listing Responders](../assets/img/chatops/ic_responders.png)

### `!ic page responders`

これは `!ic page` コマンドと同じように機能しますが、インシデントコマンダーだけでなく対応者一覧に対してページします。
通常は関連するチームだけがページされるのでめったに利用されることはありません。
しかし全員が総力を挙げて対応が必要になる場合や、現在のオンコール全員をページするのに必要です。

![Paging Responders](../assets/img/chatops/ic_page_responders.png)

### `!ic who <user>`

場合によっては、特定の誰かを指定して通話に参加させる必要があります。
このコマンドは特定のユーザーの連絡先と、ページする方法を表示します。

![Identifying Users](../assets/img/chatops/ic_who_rich.png)

### `!ic page <user>`

このコマンドは特定のユーザーをページします。

![Paging a User](../assets/img/chatops/ic_page_rich.png)

## ステータス

`!status`コマンドは、私たちの内部監視システムを参照して、システムによって報告された現在の状態を判断します。
これはアラートツールが問題を問題の自動で通知するために利用するステータスです。

### `!status`

現在のシステムステータスの概要が分かります。
またステータスを確認できない場合は、問題が発生している可能性があるため、アラートが表示されます。
通常はステータスは `NORMAL` になっているはずです。

![Displaying Status](../assets/img/chatops/status.png)

### `!status stalk`

これは上記のコマンドと同じですが、停止するまで（`!status unstalk`）の間、30秒ごとに状態をポーリングします。
前回のチェックからステータスが変更された場合のみ、チャットルームにステータスを報告します。
インシデント発生中にこれを実行することで、モニタリングシステムを手動で確認しなくても、システムが悪い状態か回復したかを簡単に確認できます。

![Stalking Status](../assets/img/chatops/status_stalk.png)