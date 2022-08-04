# ETM
1.目前版本，模型吃的Data(吃資料的程式碼在ETM/data.py)似乎只吃data_espy_tweets.py生成的dataset(matrix and terms)
2.使用自定義word embedding的程式碼在ETM/data.py/class read_embedding_matrix
3.因為抓Theta值是自行改寫的，目前有一個bug "mat1 and mat2 shapes cannot be multiplied" 維度不同的狀況。
原因是在 data_nyt.py 和 data_no_test.py 這兩個生成資料預處理的程式中，有一部份程式碼是:假如 測試集vocab(test)和驗證集vocab(val) 裡面有出現 在訓練集vocab(train) 沒有出現過的字詞，就會把那個字詞刪掉，以至於在 data_no_test.py 中生成的vocab，刪除後的數量與 data_nyt.py 生成且刪除後的數量不一樣，因為data_no_test.py這個程式主要就是故意生成沒有測試集跟驗證集的資料，才能拿到所有的document theta。

執行流程
執行前，先用scripts底下的data_no_test.py以及data_nyt.py預處理文件，生成資料集。data_nyt.py 的資料 for 訓練模型;data_no_test.py的資料for評估模型。(注意資料路徑，修改檔名)生成的資料集會在script/min_df_100 or script/no_test_min_df_100底下

訓練ETM前，將script/min_df_100 or script/no_test_min_df_100底下的資料，全部複製到ETM/data/20ng，並注意main.py裡的data路徑是不是data/20ng(沒改就沒問題)

在生成資料集時，因為停用字、字詞還原和字詞頻率少於或高於參數會刪掉字詞，處理到最後可能會有完全沒有字詞的document/patent的brief，程式會刪掉空白的document/patent的brief，所以有時你的原始數據跟出來的結果數量不同。(scripts底下removed_doc.txt會記錄第幾個document被刪掉)

訓練執行  ($train_embeddings 1 是由模型自行訓練Embedding)
```
python main.py --mode train --dataset 20ng --data_path data/20ng --num_topics 50 --train_embeddings 1 --epochs 1000
```
訓練後的模型都存放在result資料夾裡

評估模型獲得每個document的theta值 執行
```
python main.py --mode eval --dataset 20ng --data_path data/20ng --num_topics 50 --train_embeddings 1 --tc 1 --td 1 --load_from MODEL_FILENAME
```
($num_topic 設定跟模型一樣的Topic數量)

要獲得每document的theta值，執行get_doc_topic_csv.ipynb，程式會將你的原檔跟評估模型後存下來的theta值做比對，並生成csv檢視。(輸入的資料須有 資料原檔、被刪除的document數字、評估模型後的theta值)
