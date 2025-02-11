---
lab:
  title: Azure Machine Learning デザイナーを使用して、モデルをトレーニングする
---

# Azure Machine Learning デザイナーを使用して、モデルをトレーニングする

Azure Machine Learning デザイナーには、ワークフローを定義できるドラッグ アンド ドロップ インターフェイスが用意されています。 モデルのトレーニング、複数のアルゴリズムのテストと比較を簡単に行えるワークフローを作成できます。

この演習では、デザイナーを使用して、2 つの分類アルゴリズムをすばやくトレーニングして比較します。

## 開始する前に

管理レベルのアクセス権を持つ [Azure サブスクリプション](https://azure.microsoft.com/free?azure-portal=true)が必要です。

## Azure Machine Learning ワークスペースをプロビジョニングする

Azure Machine Learning ''ワークスペース'' では、モデルのトレーニングと管理に必要なすべてのリソースと資産を管理するための中心的な場所が提供されます。** Azure Machine Learning ワークスペースは、スタジオ、Python SDK、Azure CLI を使用して操作できます。

Azure CLI を使用してワークスペースと必要なリソースをプロビジョニングする、シェル スクリプトを使用します。 次に、Azure Machine Learning スタジオのデザイナーを使用して、モデルのトレーニングと比較を行います。

### ワークスペースとコンピューティング クラスターを作成する

Azure Machine Learning ワークスペースとコンピューティング クラスターを作成するには、Azure CLI を使用します。 実行するために必要なすべてのコマンドがシェル スクリプトにグループ化されます。

1. ブラウザーで、Azure portal (`https://portal.azure.com/`) を開き、Microsoft アカウントでサインインします。
1. ページ上部の検索ボックスの右側にある \[>_] (*Cloud Shell*) ボタンを選びます。 これにより、ポータルの下部に Cloud Shell ペインが開きます。
1. メッセージが表示されたら、 **[Bash]** を選択します。 Cloud Shell を初めて開いたときに、使用するシェルの種類 (*Bash* または *PowerShell*) を選択するように求められます。
1. 正しいサブスクリプションが指定されていることと、**[ストレージ アカウントは不要]** が選択されていることを確認します。 **適用**を選択します。
1. ターミナルで、次のコマンドを入力して、このリポジトリをクローンします。

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > コピーしたコードを Cloud Shell に貼り付けるには、`SHIFT + INSERT` を使用します。

1. リポジトリがクローンされたら、次のコマンドを入力してこのラボ用のフォルダーに移動し、そこに含まれている setup.sh スクリプトを実行します。

    ```azurecli
    cd azure-ml-labs/Labs/05
    ./setup.sh
    ```

    > 拡張機能がインストールされていないことを示す (エラー) メッセージは無視します。

1. スクリプトが完了するまで待ちます。通常、約 5 分から 10 分かかります。

## 新しいパイプラインを構成する

ワークスペースと必要なコンピューティング クラスターを作成したら、Azure Machine Learning スタジオを開き、デザイナーでトレーニング パイプラインを作成できます。

1. Azure portal で、**mlw-dp100-...** という名前の Azure Machine Learning ワークスペースに移動します。
1. Azure Machine Learning ワークスペースを選択し、その **[概要]** ページで **[スタジオの起動]** を選択します。 ブラウザーで別のタブが開き、Azure Machine Learning スタジオが開きます。
1. スタジオに表示されるすべてのポップアップを閉じます。
1. Azure Machine Learning スタジオ内で、 **[コンピューティング]** ページに移動し、前のセクションで作成したコンピューティング クラスターが存在することを確認します。 クラスターはアイドル状態で、実行されているノードは 0 個のはずです。
1. **[デザイナー]** ページに移動します。
1. ページの上部にある **[カスタム]** タブを選びます。
1. カスタム コンポーネントを使用して新しい空のパイプラインを作成します。
1. 右側の鉛筆アイコンを選択して、既定のパイプライン名 (**Pipeline-Created-on-* date***) を `Train-Diabetes-Classifier` に変更します。


## 新しいパイプラインを作成する

モデルをトレーニングするには、データが必要です。 データストアに格納されている任意のデータを使用することも、パブリックにアクセス可能な URL を使用することもできます。

1. 左側のメニューで **[データ]** タブを選択します。
1. **[diabetes-folder]** コンポーネントをキャンバスにドラッグ アンド ドロップします。

    これでデータが用意できたので、ワークスペース内に既に存在する (セットアップ中に作成された) カスタム コンポーネントを使用してパイプラインの作成を続けることができます。

1. 左側のメニューで、 **[コンポーネント]** タブを選択します。
1. **Remove Empty Rows** コンポーネントをキャンバスの **[diabetes-folder]** の下にドラッグ アンド ドロップします。
1. データの出力を新しいコンポーネントの入力に接続します。
1. **[数値列の正規化]** コンポーネントをキャンバスの **[空の行の削除]** の下にドラッグ アンド ドロップします。
1. 前のコンポーネントの出力を新しいコンポーネントの入力に接続します。
1. **[デシジョン ツリー分類子モデルのトレーニング]** コンポーネントをキャンバスの **[数値列の正規化]** の下にドラッグ アンド ドロップします。
1. 前のコンポーネントの出力を新しいコンポーネントの入力に接続します。
1. **[構成と送信]** を選択し、**[パイプライン ジョブのセットアップ]** ページで新しい実験を作成し、`diabetes-designer-pipeline` という名前を付け、**[次へ]** を選択します。
1. **[入力と出力]** では何も変更せずに **[次へ]** を選択します。
1. **[ランタイムの設定]** で **[コンピューティング クラスター]** を選択し、**[Azure ML コンピューティング クラスターを選択する]** で *aml-cluster* を選択します。
1. **[確認および送信]** を選択し、**[送信]** を選択してパイプラインの実行を開始します。
1. **[パイプライン]** ページに移動し、**Train-Diabetes-Classifier** パイプラインを選択すると、実行の状態を確認できます。
1. すべてのコンポーネントが正常に完了するまで待ちます。

    ジョブを送信すると、コンピューティング クラスターが初期化されます。 コンピューティング クラスターはそれまでアイドル状態であったため、クラスターのノード数が 0 を超えるようにサイズ変更されるまでに時間がかかる場合があります。 クラスターがサイズ変更されると、パイプラインの実行が自動的に開始されます。

各コンポーネントの実行を追跡できます。 パイプラインが失敗すると、失敗したコンポーネントと失敗した理由を調べることができます。 エラー メッセージは、ジョブの概要の **[出力 + ログ]** タブに表示されます。

## 比較する 2 つ目のモデルをトレーニングする

アルゴリズムを比較して、どちらのパフォーマンスが優れているかを評価するために、1 つのパイプライン内で 2 つのモデルをトレーニングして、比較することができます。

1. **デザイナー**に戻り、**Train-Diabetes-Classifier** パイプラインのドラフトを選択します。
1. 他のトレーニング コンポーネントの横にあるキャンバスに **[ロジスティック回帰分類子モデルのトレーニング]** コンポーネントを追加します。
1. **[数値列の正規化]** コンポーネントの出力を、新しいトレーニング コンポーネントの入力に接続します。
1. 上部にある **[構成と送信]** を選択します。
1. **[基本]** ページで、`designer-compare-classification` という名前の新しい実験を作成して実行します。
1. **[確認および送信]** を選択し、**[送信]** を選択してパイプラインの実行を開始します。
1. **[パイプライン]** ページに移動し、**designer-compare-classification** 実験で **Train-Diabetes-Classifier** パイプラインを選択すると、実行の状態を確認できます。
1. すべてのコンポーネントが正常に完了するまで待ちます。  
1. **[ジョブの概要]** を選択し、**[メトリック]** タブを選択して、両方のトレーニング コンポーネントの結果を確認します。
1. どちらのモデルのパフォーマンスが優れているかを試して判断してください。

## Azure リソースを削除する

Azure Machine Learning を調べ終わったら、不要な Azure のコストを避けるために作成したリソースを削除する必要があります。

1. [Azure Machine Learning スタジオ] タブを閉じて、Azure portal に戻ります。
1. Azure portal の **[ホーム]** ページで、**[リソース グループ]** を選択します。
1. **[rg-dp100-...]** リソース グループを選択します。
1. リソース グループの **[概要]** ページの上部で、**[リソース グループの削除]** を選択します。
1. リソース グループ名を入力して、削除することを確認し、**[削除]** を選択します。
