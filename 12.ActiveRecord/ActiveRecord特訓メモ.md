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

■自分の解答  
①Employee.joins(:salaries).includes(:salaries).where('salary >= 150000')  
②Employee.joins(:salaries).eager_load(:salaries).where('salary >= 150000')  
③Employee.eager_load(:salaries).where('salary >= ?', 150000)  
④Employee.joins(:salaries).where('salary > ?', 150000).distinct  
⑤Employee.joins(:salaries).where('salary > ?', 150000).group('emp_no')  

■解説  
【間違えていた考え】  
`モデル名.joins(:関連名)`内部結合をすることができる。(joinsの指定は関連名というところに注意)  
ただjoinメソットは、モデルのレコードしか取らないので関連先のデータは取得されない。
`Employee.joins(:salaries).where('salary > ?', 150000)`で最初書いたが、1人が複数回150000以上もらったケースもカウントされてしまうためNG。  

【正解になった考え】  
①、②`join`を`inclides`と`eager_load`と合わせて使用すると内部結合だが、重複が削除されるため上手く表示されたっぽい。  
[Rails における内部結合、外部結合まとめ](https://qiita.com/yuyasat/items/c2ad37b5a24a58ee3d30#1-actresses%E3%81%A8movies%E3%81%AE%E5%86%85%E9%83%A8%E7%B5%90%E5%90%88)  
③`eager_load`を使用して左外部結合で抽出。[ActiveRecord ～ 複数テーブルにまたがる検索（preload, eager_load, include, joins）](https://qiita.com/leon-joel/items/f26556c9e56833983856)  
※ただこれはたまたま正解した模様。通常は外部結合での重複されるはず。

### 問題4  
■問題  
150000以上の給料をもらったことがある女性従業員の一覧を取得してください  

■自分の解答  
①Employee.eager_load(:salaries).where('salary >= ?', 150000).where(gender: 'F')  
②Employee.eager_load(:salaries).where("salary >= ? and gender = ?",150000,'F')  

■解説  
①whereをメソッドチェーンで繋ぐとand検索となる。  
②1つのwhere内でプレースホルダを使用。`Employee.eager_load(:salaries).where("salary > ? and gender: 'F'",150000)`のように文字列での検索とハッシュの検索を混ぜて記載するとエラーとなった。(そういうもの？)  

### 問題5  
■問題  
どんな肩書きがあるか一覧で取得してきてください  

■自分の解答  
①Title.select('title').distinct  
②Title.distinct.select('title')

■解説  
`Title.select('title')`だけだと重複も抜かれてしまったため、`distinct`メソッドを追加した。  

### 問題6  
■問題  
2000-1-29以降に肩書きが「Technique Leader」になった従業員を取得してください  

■自分の解答  
Employee.eager_load(:titles).where(titles: {title: 'Technique Leader'}).where('from_date >= ?', '2000-01-29')  

■解説  
`where(title: 'Technique Leader')`のように書くと`WHERE employees.title = 'Technique Leader'`のSQLが発行されてしまうので、関連先の条件を指定したい場合は`where(結合先のテーブル名: { カラム名: 値 })`の形式で書く必要がある。  
`where('from_date >= ?', '2000-01-29')`の部分はシンボルでなく、文字列で検索を指定しているので上記対応は不要と思われる。  

### 問題7  
■問題  
部署番号がd001である部署のマネージャー歴代一覧を取得してきてください  

■自分の解答  
Employee.joins(:dept_managers).where(dept_manager: {dept_no: 'd001'})  

■解説  
以下の考え方で実施。  
employeesテーブルとdept_managerテーブルを内部結合を行い、dept_noがd001という条件を設定。  

### 問題8  
■問題  
歴代マネージャーにおける男女比を出してください  

■自分の解答  
Employee.joins(:dept_managers).group(:gender).count  

■解説  
中間テーブルと内部結合をして、グループ化して集合関数(count)を使用する。  
[【Rails】groupメソッドの使い方を図解形式で仕組みを徹底解説！](https://pikawaka.com/rails/group)  

### 問題9  
■問題  
部署番号がd004の部署における1999-1-1時点のマネージャーを取得してください  

■自分の解答  
Employee.joins(:dept_managers).where(dept_manager: {dept_no: 'd004'}).where('to_date >= ?', '1999-1-1').order(:from_date).first

■解説  
①dept_managersと内部結合。  
②dept_noがd004かつto_dateが1999-1-1以降に絞る。  
③昇順に並べ替えた一番上のデータを取得する。

※「'to_date >= ?', '1999-1-1'」の`to_date`の名前が他のテーブルカラム名と被った場合はどうするんだろう...  
→範囲オブジェクトを使えば大丈夫そう！[【Rails】範囲オブジェクト(Range)を使ったActiveRecordのwhere比較、範囲検索のコードの書き方](https://simple-minds-think-alike.hatenablog.com/entry/active-record-where-with-range)  

### 問題10 
■問題  
従業員番号が10001, 10002, 10003の従業員が今までに稼いだ給料の合計を従業員ごとに集計してください  

■自分の解答  
Employee.joins(:salaries).where(emp_no: [10001,10002,10003]).group(:emp_no).sum(:salary)

■解説  
①`where(emp_no: [10001,10002,10003])`にてIN句を使用してemp_noを3つに絞る。  
②emp_noでグループ化する。  
③sumメソッドでグループごとにsalaryごとの合計をだす。  

### 問題11 
■問題  
上記に加えtotal_salaryという仮のフィールドを作ってemployeeの情報とがっちゃんこしてください  

■自分の解答  
Employee.find_by_sql("SELECT `employees`.*, SUM(salary) AS total_salary FROM `employees` INNER JOIN `salaries` ON `salaries`.`emp_no` = `employees`.`emp_no` WHERE `employees`.`emp_no` IN (10001, 10002, 10003) GROUP BY `employees`.`emp_no`")  

■解説  
ActiveRecordを使用してだと、SELECT文に`SUM(salary) AS total_salary`としても`{10001=>1281612, 10002=>413127, 10003=>301212}`のようにハッシュが返って来てしまったため、find_by_sqlメソッドを使用して生SQLで記載。

### 問題12 
■問題  
上記の結果を利用してコンソール上に以下のようなフォーマットでputsしてください。  

■自分の解答  
①employees = Employee.find_by_sql("SELECT `employees`.*, SUM(salary) AS total_salary FROM `employees` INNER JOIN `salaries` ON `salaries`.`emp_no` = `employees`.`emp_no` WHERE `employees`.`emp_no` IN (10001, 10002, 10003) GROUP BY `employees`.`emp_no`")  

②employees.each do |employee|
    p "emp_no: #{employee.emp_no}"
    p "full_name: #{employee.first_name} #{employee.last_name}"
    p "total_salary: #{employee.total_salary}"
  end

■解説  
問題11の結果をeach文で展開をしてそれぞれputs。
