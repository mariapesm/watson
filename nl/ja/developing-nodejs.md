---

copyright:
  years: 2015, 2018
lastupdated: "2018-05-02"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:tip: .tip}
{:pre: .pre}
{:codeblock: .codeblock}
{:screen: .screen}
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}

# Node.js での {{site.data.keyword.watson}} アプリケーションの開発

REST API の使用法をご確認いただけるよう、{{site.data.keyword.ibmwatson}} サービスごとに、サンプルの Node.js の Web ベース・アプリケーションを {{site.data.keyword.watson}} GitHub プロジェクト内に用意しています。
{: shortdesc}

## 始める前に

- {{site.data.keyword.cloud_notm}} でアカウントを作成するか、既存のアカウントを使用します。[こちらから無料でお申し込みいただけます ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://console.{DomainName}/registration/?target=/catalog/%3fcategory=watson){: new_window}。そのアカウントに、少なくとも 1 つのアプリと 1 つのサービスのスペースが必要です。
- [Node.js ランタイム![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://nodejs.org/#download){: new_window} ([npm](https://www.npmjs.com/){: new_window} パッケージ・マネージャーも含む)。インストール後に、必ず `PATH` 環境変数にコマンドを含めてください。
- [Cloud Foundry ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/cloudfoundry/cli#downloads){: new_window} コマンド・ライン・クライアント。以前にインストールしてある場合は、バージョンが最新であることを確認してください。

## ソース・コードの取得

[{{site.data.keyword.watson}} Github ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://github.com/watson-developer-cloud){: new_window} プロジェクトにアクセスして、{{site.data.keyword.watson}} で使用できるサンプル Node.js アプリケーションを確認してください。GitHub を使用してリポジトリーをローカル環境に複製してください。

## ローカル環境へのインストール
アプリを変更する場合や、アプリを独自のアプリを作成するための基礎として使用する場合は、そのアプリをローカル環境にインストールします。その後、変更を加えたバージョンのアプリを {{site.data.keyword.cloud_notm}} にデプロイできます。

### {{site.data.keyword.watson}} サービスのセットアップ

1.  コマンド・ラインで、複製したアプリのローカル・プロジェクト・ディレクトリーに移動します。
1.  {{site.data.keyword.cloud_notm}} にログインします。

    ```bash
    cf login -a api https://api.ng.bluemix.net
    ```
    {: pre}

1.  サービスとプランの名前を見つけます。サンプル・アプリの `manifest.yml` ファイルに `declared-services` セクションが含まれている場合は、`label` と `plan` を確認してください。そうでない場合は、`cf marketplace` コマンドを使用して、サービスとプランの名前を取得します。

    ```bash
    cf marketplace
    ```
    {: pre}

1.  次のように {{site.data.keyword.cloud_notm}} でサービスのインスタンスを作成します。`cf create-service {service-name} {service-plan} {service-instance-name}`。service-name と service-plan では、マニフェスト・ファイルか `cf marketplace` コマンドから取得した情報を使用します。

    例えば、{{site.data.keyword.personalityinsightsshort}} サービスのサービス・インスタンスを作成する場合は、以下のコマンドを実行します。

    ```bash
    cf create-service personality_insights standard my-personality-insights-service
    ```
    {: pre}

### アプリ環境の構成

1.  `.env.example` ファイルを新しい `.env` ファイルにコピーします。
1.  `cf create-service-key {service_instance} {service_key}` という形式でサービス・キーを作成します。以下に例を示します。

    ```bash
    cf create-service-key my-personality-insights-service myKey
    ```
    {: pre}

1.  コマンド `cf service-key {service_instance} {service_key}` を使用して、サービス・キーから資格情報を取得します。以下に例を示します。

    ```bash
    cf service-key my-conversation-service myKey
    ```
    {: pre}

    このコマンドの出力は、以下の例のような JSON オブジェクトです。

    ```json
    {
      "url": "https://gateway.watsonplatform.net/personality-insights/api",
      "username": "583b2e88-c80d-4ec0-93c1-98239f805146",
      "password": "RuytRliRvoFN"
    }
    ```
    {: codeblock}

1.  JSON の `password` と `username` の値 (引用符は除く) を `.env` ファイルの該当する変数に貼り付けます。以下に例を示します。

    ```json
    PERSONALITY_INSIGHTS_USERNAME=583b2e88-c80d-4ec0-93c1-98239f805146
    PERSONALITY_INSIGHTS_PASSWORD=RuytRliRvoFN
    ```
    {: codeblock}

### アプリケーションのインストールと開始

1.  デモ・アプリ・パッケージをローカル Node.js ランタイム環境にインストールします。

    ```bash
    npm install
    ```
    {: pre}

1.  アプリケーションを開始します。

    ```bash
    npm start
    ```
    {: pre}

1.  ブラウザーで http://localhost:3000 を指定して、アプリを試用してみます。ポートは `app.js` ファイルで指定されています。

## {{site.data.keyword.cloud_notm}} へのデプロイ

Cloud Foundry を使用して、ローカル・バージョンのアプリを {{site.data.keyword.cloud_notm}} にデプロイできます。

1.  プロジェクトのルート・ディレクトリーで、manifest.yml ファイルを開きます。
    1.  `manifest.yml` ファイルの `applications` セクションで、名前の値を、アプリのそのバージョンに固有の名前に変更します。
    1.  `services` セクションで、アプリ用に作成した {{site.data.keyword.watson}} サービス・インスタンスの名前を指定します。サービス名を覚えていない場合は、`cf services` コマンドを使用してサービスのリストを表示してください。

        変更を加えた `manifest.yml` ファイルの例を以下に示します。

        ```yml
        ---
        declared-services:
          my-personality-insights-service:
            label: personality_insights
            plan: standard
        applications:
        - name: personality-insights-app-test1
          command: npm start
          path: .
          memory: 256M
          instances: 1
          services:
          - my-personality-insights-service
          env:
           NPM_CONFIG_PRODUCTION: false
        ```
        {: codeblock}

1.  {{site.data.keyword.cloud_notm}} にログインします。

    ```bash
    cf login -a api https://api.ng.bluemix.net
    ```
    {: pre}

1.  アプリを {{site.data.keyword.cloud_notm}} にプッシュします。`manifest.yml` ファイルにあるアプリの名前を指定してください。例えば、以下のようにします。

    ```bash
    cf push personality-insights-app-test1
    ```
    {: pre}

    コマンド出力に表示されている URL をブラウザーで指定します。
