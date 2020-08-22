# Ryoさんのoutputについて  
## 個人的な全体感想  
1.関連を持つテーブルが3つ作成されているだけでなく、自己結合も使用されていたので凄いと感じました。  
2.閲覧者という概念が入っていることが通常のtodoアプリと異なり面白いなと思いました。  
3.viewportの設定されていることや編集者、閲覧者でコントローラー、ビューが分けられているのがいいなと思いました。  


## 個人的に気になった箇所
1.カテゴリーのリスト選択にbootstrapが効いていないかも？  
①対象ページ  
todo新規作成ページ  

②予想理由  
classが設定されていない?（検証ツールより）  

③予想改善方法  
```
# app/views/todos/_form.html.slimの一部抜粋

  .form-group
    = f.label :category_id
    = f.collection_select :category_id, Category.all, :id, :name, :include_blank => true
```
上記の末尾に`class: 'form-control'`を入れる？  
（ただ自分も f.collection_select書き方がの理解が微妙）


2.登録日時の表示形式を変更した方がいいかも？  
①対象ページ  
todo一覧のページ  

②予想改善方法  
自分は以下サイトを参考にlメソッドを使用しました。  
[【初心者向け・動画付き】Railsで日時をフォーマットするときはstrftimeよりも、lメソッドを使おう](https://qiita.com/jnchito/items/831654253fb8a958ec25)