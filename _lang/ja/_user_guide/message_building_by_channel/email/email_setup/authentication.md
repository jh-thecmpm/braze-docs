---
nav_title: 電子メール認証
article_title: 電子メール認証
page_order: 2
page_type: reference
description: "このリファレンスでは、電子メール認証（電子メールの送信元に関する検証可能な情報を電子メールに付与することを目的とした技術のコレクション）について説明する。"
channel: email

---

# 電子メール認証

> 電子メール認証は、電子メールにその発信元に関する検証可能な情報を装備する技術の集合体である。<br><br>適切な認証は、インターネット・サービス・プロバイダー（ISP）があなたを望ましい電子メールの送信者として認識し、メールを直ちに配信するために極めて重要である。認証がなければ、あなたのアウトリーチは詐欺とみなされる。 

## 認証方法

### センダー・ポリシー・フレームワーク（SPF）

この方法は、あなたのBrazeメール送信IPアドレスが、あなたに代わってメールを送信する権限があることを確認する。SPFは基本的な認証であり、DNS設定でテキストレコードを公開することで実現される。受信サーバーはDNSレコードをチェックし、それが本物かどうかを判断する。この方法は、電子メールの送信者を検証するためのものである。

SPFレコードは、Brazeがお客様のIPとドメインを設定する際に設定されます。

### ドメインキー識別メール（DKIM）

この方法は、Brazeのメール送信ドメインがあなたの代わりにメールを送信する権限があることを確認する。この方法は、送信者の真正性を検証し、メッセージの完全性が保たれていることを検証するために設計されている。また、個々の暗号デジタル署名を使用するため、ISPは配信するメールがあなたが送信したものと同じであることを確認できる。

Brazeはあなたの秘密の秘密鍵でメールに署名する。ISPは、カスタムDNSレコードに保存されているあなたの公開鍵と照合して署名を検証する。2つとしてまったく同じ署名はなく、あなたの公開鍵だけが、あなたの秘密鍵署名を正しく検証することができる。

DKIMレコードは、Brazeがお客様のIPとドメインを設定する際に設定される。

### ドメインベースのメッセージ認証、報告、適合性（DMARC）

[Domain-based Message Authentication, Reporting & Conformance (DMARC)](https://dmarc.org/)は、メール送信者がメールの正当性を証明するための電子メール認証プロトコルであり、メールボックス受信者の信頼性を高め、メールの受け入れを促す。DMARCは、電子メールの送信者が、Sender Policy Framework（SPF）またはDomain Keys Identified Mail（DKIM）を使って認証されなかった電子メールの処理方法を指定することを可能にする。これは、SPFとDKIMの両方のチェックがパスしていることを確認することで達成される。 

送信者は、メールボックス・プロバイダに、署名や認証のチェックに失敗したメールをどのように処理すべきか指示することができる。失敗は、他の人があなたやあなたのメールを真似しようとしていることを示している可能性がある。送信者は、メールボックス・プロバイダにメールの拒否や隔離を指示したり、チェックに失敗したメールについて自動レポートを送ることもできる。そうすることで、メールボックス・プロバイダは、スパマーをよりよく特定し、悪意のあるメールが受信トレイに侵入するのを防ぐことができる。同時に、誤検知を最小限に抑え、市場の透明性を高めるために、より優れた認証レポートを提供することができる。

#### その仕組み

DMARCを導入するには、DNS（Domain Naming System）にDMARCレコードを公開する必要がある。これは、SPFとDKIMのステータスをチェックした後、メールドメインのポリシーを公に表現するTXTレコードである。DMARCは、SPFまたはDKIMのいずれか、あるいは両方がパスした場合に認証する。これはDMARCアライメントと呼ばれる。

DMARCレコードはまた、DMARCレコードに記載されている報告用電子メールアドレスにXMLレポートを送り返すよう、電子メールサーバーに指示する。これらのレポートは、お客様のEメールがエコシステム内をどのように移動しているかについての洞察を提供し、お客様のEメールドメインを使用してEメールコミュニケーションを送信しようとしているすべてのものを特定することを可能にする。

あなたのDMARCレコードにあるポリシーは、SPFとDKIMをパスしていないが、あなたのドメインからのものであると主張するメールに対して何をすべきかを、参加する受信者メールサーバーに指示する。Brazeでは、ルートドメインにDMARCポリシーを設定し、すべてのサブドメインに適用することを推奨している。つまり、今後、現在のサブドメインや新しいサブドメインで追加設定が不要になるということだ。設定できるポリシーは3種類ある：

| 方針 | インパクト |
| --- | --- |
| なし | メールボックス・プロバイダに、失敗したメッセージに対して何もしないように指示する。 |
| 検疫 | メールボックス・プロバイダに、失敗したメッセージをスパムフォルダに送るように指示する。 |
| 却下する | メールボックス・プロバイダに、失敗したメッセージはスパムフォルダに入り、ブロックされるべきであると伝える。 |
{: .reset-td-br-1 .reset-td-br-2 .reset-td-br-3}

#### ドメインのDMARC認証を確認する方法

ドメインのDMARC認証をチェックするには、2つのオプションがある：

- **オプション 1:**[MXToolboxの](https://mxtoolbox.com/dmarc.aspx)ようなサードパーティのDMARCチェッカーに親ドメインやサブドメインを入力することで、DMARCポリシーがあるかどうか、またそのポリシーが何に設定されているかを監査することができる。
    - **MXToolbox**：DMARCをルートドメインに設定した場合、MXToolboxにそれを入力する。サブドメインにDMARCを設定する場合は、MXToolboxにサブドメインを入力する。MXToolboxは、ルックアップを実行する際、"上や下を見る "ことはしない。つまり、ルートドメインでDMARCを設定し、サブドメインを入力した場合、MXToolboxはルートドメインでDMARCが設定されていることを知らないため、失敗を表示する。
- **オプション 2:**メールボックスでドメインまたはサブドメインからのメールを開き、元のメッセージを見つけて、DMARCがこのメールで認証をパスしているかどうかをチェックする。

例えば、Gmailを使っている場合は、以下の手順に従う：

1. Eメールメッセージの**More** <i class="fa-solid fa-ellipsis"></i> をクリックする。
2. **Show originalを**選択する。
3. **DMARCの**ステータスが "PASS "かどうかを確認する。

![DMARCの値が "PASS "であるメール。]({% image_buster /assets/img_archive/dmarc_example.png %})

