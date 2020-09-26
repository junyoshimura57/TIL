## 課題内容  
[課題内容](https://esa-pages.io/p/sharing/15996/posts/9/918e6efd34dbff546236.html#%E4%B8%8A%E8%A8%98%E3%81%AE%E7%B5%90%E6%9E%9C%E3%82%92%E5%88%A9%E7%94%A8%E3%81%97%E3%81%A6%E3%82%B3%E3%83%B3%E3%82%BD%E3%83%BC%E3%83%AB%E4%B8%8A%E3%81%AB%E4%BB%A5%E4%B8%8B%E3%81%AE%E3%82%88%E3%81%86%E3%81%AA%E3%83%95%E3%82%A9%E3%83%BC%E3%83%9E%E3%83%83%E3%83%88%E3%81%A7puts%E3%81%97%E3%81%A6%E3%81%8F%E3%81%A0%E3%81%95%E3%81%84%E3%80%82)

### 問題1  
■問題  
従業員番号が499999の従業員の給料全てを取得してください  

■自分の解答  
Salary.where(emp_no: 499999)

■解説  
もし1行目のsalaryの数字を取得したい場合は、`Salary.where(emp_no: 499999).first.salary`のように書く必要がある。[ActiveRecord の find と where の違い](https://qiita.com/7coco/items/b08c842aece4b7f28f55)  

### 問題2  
■問題  
従業員番号が499999の従業員の給料全てを取得してください  

■自分の解答  
①Salary.where('emp_no = ? and from_date <= ? and to_date >= ?', 499999, '2001-01-01', '2001-01-01')  
②Salary.where(emp_no: 499999).where("from_date <= '2001-01-01'").where("to_date >= '2001-01-01'")  
※他にやり方がある気がする...

■解説  
①whereメソッドで不等号を使用する場合は、プレースホルダーを使用する必要がある。[https://pikawaka.com/rails/where#whereメソッドの様々な検索方法・複雑な指定方法](https://pikawaka.com/rails/where)  
②日時は文字列なので`'`で囲んであげる必要があり、その影響で全体は`"`を使用する？？(特に文献が見つからず...)  
なんでプレースホルダ使用しないでできたんだろう？？  

### 問題3  
■問題  
150000以上の給料をもらったことがある従業員の一覧を取得してください  

■自己解答  
①Employee.joins(:salaries).includes(:salaries).where('salary > 150000')  
②Employee.joins(:salaries).eager_load(:salaries).where('salary > 150000')  
③Employee.eager_load(:salaries).where('salary > ?', 150000)  

■解説  
【間違えていた考え】  
`モデル名.joins(:関連名)`内部結合をすることができる。(joinsの指定は関連名というところに注意)  
ただjoinメソットは、モデルのレコードしか取らないので関連先のデータは取得されない。
`Employee.joins(:salaries).where('salary > ?', 150000)`で最初書いたが、1人が複数回150000以上もらったケースもカウントされてしまうためNG。  

【正解になった考え】  
①、②`join`を`inclides`と`eager_load`と合わせて使用すると内部結合だが、重複が削除されるため上手く表示されたっぽい。  
[Rails における内部結合、外部結合まとめ](https://qiita.com/yuyasat/items/c2ad37b5a24a58ee3d30#1-actresses%E3%81%A8movies%E3%81%AE%E5%86%85%E9%83%A8%E7%B5%90%E5%90%88)  
③`eager_load`を使用して左外部結合で抽出。[ActiveRecord ～ 複数テーブルにまたがる検索（preload, eager_load, include, joins）](https://qiita.com/leon-joel/items/f26556c9e56833983856)  