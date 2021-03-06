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

# 使用 Node.js 開發 {{site.data.keyword.watson}} 應用程式

為了示範 REST API 的使用，每個 {{site.data.keyword.ibmwatson}} 服務都會在 {{site.data.keyword.watson}} GitHub 專案中提供使用 Node.js 的範例 Web 型應用程式。
{: shortdesc}

## 開始之前

- 在 {{site.data.keyword.cloud_notm}} 上建立帳戶，或使用現有帳戶。[免費註冊 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://console.{DomainName}/registration/?target=/catalog/%3fcategory=watson){: new_window}。您的帳戶必須具有至少 1 個應用程式及 1 個服務的空間。
- [Node.js 運行環境 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://nodejs.org/#download){: new_window}，包括 [npm](https://www.npmjs.com/){: new_window} 套件管理程式。請確定在安裝之後，在 `PATH` 環境變數上包含指令。
- [Cloud Foundry ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://github.com/cloudfoundry/cli#downloads){: new_window} 指令行用戶端。如果您先前已安裝它，請確定您的版本是最新版本。

## 取得原始碼

請造訪 [{{site.data.keyword.watson}} GitHub ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://github.com/watson-developer-cloud){: new_window} 專案，以查看可供 {{site.data.keyword.watson}} 使用的範例 Node.js 應用程式。然後，使用 GitHub，在本端複製儲存庫。

## 在本端安裝
如果您要修改應用程式，或使用它作為自行建置應用程式的基礎，請在本端安裝它。然後您便可以將應用程式的修改版本部署到 {{site.data.keyword.cloud_notm}}。

### 設定 {{site.data.keyword.watson}} 服務

1.  在指令行上，移至您複製應用程式的本端專案目錄。
1.  登入 {{site.data.keyword.cloud_notm}}：

    ```bash
    cf login -a api https://api.ng.bluemix.net
    ```
    {: pre}

1.  尋找服務及方案的名稱。如果範例應用程式的 `manifest.yml` 檔包含 `declared-services` 區段，請注意 `label` 和 `plan`。否則，請使用 `cf marketplace` 指令來取得服務及方案的名稱：

    ```bash
    cf marketplace
    ```
    {: pre}

1.  在 {{site.data.keyword.cloud_notm}} 建立服務實例：`cf create-service {service-name} {service-plan} {service-instance-name}`。針對 service-name 及 service-plan，請使用來自 manifest 檔或 `cf marketplace` 指令的資訊。

    例如，發出下列指令，以建立 {{site.data.keyword.personalityinsightsshort}} 服務的服務實例：

    ```bash
    cf create-service personality_insights standard my-personality-insights-service
    ```
    {: pre}

### 配置應用程式環境

1.  將 `.env.example` 檔複製到新的 `.env` 檔。
1.  以 `cf create-service-key {service_instance} {service_key}` 格式建立服務金鑰。例如：

    ```bash
    cf create-service-key my-personality-insights-service myKey
    ```
    {: pre}

1.  使用 `cf service-key {service_instance} {service_key}` 指令，從服務金鑰擷取認證。例如：

    ```bash
    cf service-key my-conversation-service myKey
    ```
    {: pre}

    這個指令的輸出是 JSON 物件，如下列範例所示：

    ```json
    {
      "url": "https://gateway.watsonplatform.net/personality-insights/api",
      "username": "583b2e88-c80d-4ec0-93c1-98239f805146",
      "password": "RuytRliRvoFN"
    }
    ```
    {: codeblock}

1.  將 `password` 和 `username` 值（不含引號）從 JSON 貼到 `.env` 檔案中的相關變數。例如：

    ```json
    PERSONALITY_INSIGHTS_USERNAME=583b2e88-c80d-4ec0-93c1-98239f805146
    PERSONALITY_INSIGHTS_PASSWORD=RuytRliRvoFN
    ```
    {: codeblock}

### 安裝及啟動應用程式

1.  將展示應用程式套件安裝至本端 Node.js 運行環境：

    ```bash
    npm install
    ```
    {: pre}

1.  啟動應用程式：

    ```bash
    npm start
    ```
    {: pre}

1.  將您的瀏覽器指向 http://localhost:3000 以試用應用程式。埠指定在 `app.js` 檔案中。

## 部署至 {{site.data.keyword.cloud_notm}}

您可以使用 Cloud Foundry，將應用程式的本端版本部署至 {{site.data.keyword.cloud_notm}}。

1.  在專案根目錄中，開啟 manifest.yml 檔案：
    1.  在 `manifest.yml` 檔案的 `applications` 區段中，將名稱值變更為您應用程式版本的唯一名稱。
    1.  在 `services` 區段中，指定您為應用程式所建立之 {{site.data.keyword.watson}} 服務實例的名稱。如果您不記得服務名稱，請使用 `cf services` 指令來列出您的服務。

        下列範例顯示已修改的 `manifest.yml` 檔：

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

1.  登入 {{site.data.keyword.cloud_notm}}：

    ```bash
    cf login -a api https://api.ng.bluemix.net
    ```
    {: pre}

1.  將應用程式推送至 {{site.data.keyword.cloud_notm}}，並指定來自 `manifest.yml` 檔案的應用程式名稱。例如：

    ```bash
    cf push personality-insights-app-test1
    ```
    {: pre}

    將瀏覽器指向指令輸出中指定的 URL。
