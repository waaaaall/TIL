# issetとemptyとis_nullの表  
|値|if($val)|isset|empty|is_null|
|---|---|---|---|---|
|$var=1|true|true|false|false|
|$var=""|false|true|true|false|
|$var="0"|false|true|true|false|
|$var=0|false|true|true|false|
|$var=null|false|false|true|true|
|$var|false|false|true|true|
|$var=array()|false|true|true|false|
|$var=array(1)|true|true|false|false|
  
# 引用  
`http://d.hatena.ne.jp/sngmr/20080209/1202520832`
