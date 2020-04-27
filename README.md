## 因为服务器使用的是windows，不支持xunsearch。故只能使用pscws4纯php的中文分词类实现。php框架是laravel，数据库是mysql8.x <br />

# 步骤

## 1、composer安装pscws4扩展。进入项目根目录，执行 composer require wxkxklmyt/pscws4

## 2、mysql数据库对需要搜索的字段增加全文索引。alter table your_table add FULLTEXT KEY `name` (`name`) with parser ngram; 这里注意，必须要加上 with parser ngram 条件的自然语言模式，否则无法进行中文匹配。另外还有一个坑，mariaDB不支持此参数，故又把数据库从mariaDB切换回mysql8.

## 3、用法很简单，先把输入的字符串拆分成分词，再用mysql的全文索引查找出对应的记录。
$pscws = new PSCWS4('utf8');<br />
$pscws->set_dict('/path/to/etc/dict.xdb');<br />
$pscws->set_rule('/path/to/etc/rules.ini');<br />
$pscws->send_text($text);<br />
while ($some = $pscws->get_result()){<br />
   foreach ($some as $word){<br />
      array_push($splitWordArr, $word['word'];<br />
   }<br />
}<br /><br />

$splitWord = implode(' ', $splitWordArr);<br /><br />

select * from your_table where match(`name`) against ($splitWord);<br />
