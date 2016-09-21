# :cyclone:一、黑名单过滤
```PHP
function is_spam($text, $file, $split = ':', $regex = false){ 
    $handle = fopen($file, 'rb'); 
    $contents = fread($handle, filesize($file)); 
    fclose($handle); 
    $lines = explode("n", $contents); 
    $arr = array(); 
    foreach($lines as $line){ 
        list($word, $count) = explode($split, $line); 
        if($regex) 
            $arr[$word] = $count; 
        else 
            $arr[preg_quote($word)] = $count; 
    } 
    preg_match_all("~".implode('|', array_keys($arr))."~", $text, $matches); 
    $temp = array(); 
    foreach($matches[0] as $match){ 
        if(!in_array($match, $temp)){ 
            $temp[$match] = $temp[$match] + 1; 
            if($temp[$match] >= $arr[$word]) 
                return true; 
        } 
    } 
    return false; 
} 

$file = 'spam.txt'; 
$str = 'This string has cat, dog word'; 
if(is_spam($str, $file)) 
    echo 'this is spam'; 
else 
    echo 'this is not spam';

ab:3
dog:3
cat:2
monkey:2
```

# :cyclone:二、随机颜色生成器
```PHP
function randomColor() { 
    $str = '#'; 
    for($i = 0 ; $i < 6 ; $i++) { 
        $randNum = rand(0 , 15); 
        switch ($randNum) { 
            case 10: $randNum = 'A'; break; 
            case 11: $randNum = 'B'; break; 
            case 12: $randNum = 'C'; break; 
            case 13: $randNum = 'D'; break; 
            case 14: $randNum = 'E'; break; 
            case 15: $randNum = 'F'; break; 
        } 
        $str .= $randNum; 
    } 
    return $str; 
} 
$color = randomColor();
```

# :cyclone:三、从网络下载文件
```PHP
set_time_limit(0); 
// Supports all file types 
// URL Here: 
$url = 'http://somsite.com/some_video.flv'; 
$pi = pathinfo($url); 
$ext = $pi['extension']; 
$name = $pi['filename']; 

// create a new cURL resource 
$ch = curl_init(); 

// set URL and other appropriate options 
curl_setopt($ch, CURLOPT_URL, $url); 
curl_setopt($ch, CURLOPT_HEADER, false); 
curl_setopt($ch, CURLOPT_BINARYTRANSFER, true); 
curl_setopt($ch, CURLOPT_AUTOREFERER, true); 
curl_setopt($ch, CURLOPT_FOLLOWLOCATION, true); 
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true); 

// grab URL and pass it to the browser 
$opt = curl_exec($ch); 

// close cURL resource, and free up system resources 
curl_close($ch); 

$saveFile = $name.'.'.$ext; 
if(preg_match("/[^0-9a-z._-]/i", $saveFile)) 
    $saveFile = md5(microtime(true)).'.'.$ext; 

$handle = fopen($saveFile, 'wb'); 
fwrite($handle, $opt); 
fclose($handle);
```
# :cyclone:四、Alexa/Google Page Rank
```PHP
function page_rank($page, $type = 'alexa'){ 
    switch($type){ 
        case 'alexa': 
            $url = 'http://alexa.com/siteinfo/'; 
            $handle = fopen($url.$page, 'r'); 
        break; 
        case 'google': 
            $url = 'http://google.com/search?client=navclient-auto&ch=6-1484155081&features=Rank&q=info:'; 
            $handle = fopen($url.'http://'.$page, 'r'); 
        break; 
    } 
    $content = stream_get_contents($handle); 
    fclose($handle); 
    $content = preg_replace("~(n|t|ss+)~",'', $content); 
    switch($type){ 
        case 'alexa': 
            if(preg_match('~<div class="data (down|up)"><img.+?>(.+?) </div>~im',$content,$matches)){ 
                return $matches[2]; 
            }else{ 
                return FALSE; 
            } 
        break; 
        case 'google': 
            $rank = explode(':',$content); 
            if($rank[2] != '') 
                return $rank[2]; 
            else 
                return FALSE; 
        break; 
        default: 
            return FALSE; 
        break; 
    } 
} 
// Alexa Page Rank: 
echo 'Alexa Rank: '.page_rank('techug.com'); 
echo '
'; 
// Google Page Rank 
echo 'Google Rank: '.page_rank('techug.com', 'google');
```
# :cyclone:五、强制下载文件
```PHP
$filename = $_GET['file']; //Get the fileid from the URL 
// Query the file ID 
$query = sprintf("SELECT * FROM tableName WHERE id = '%s'",mysql_real_escape_string($filename)); 
$sql = mysql_query($query); 
if(mysql_num_rows($sql) > 0){ 
    $row = mysql_fetch_array($sql); 
    // Set some headers 
    header("Pragma: public"); 
    header("Expires: 0"); 
    header("Cache-Control: must-revalidate, post-check=0, pre-check=0"); 
    header("Content-Type: application/force-download"); 
    header("Content-Type: application/octet-stream"); 
    header("Content-Type: application/download"); 
    header("Content-Disposition: attachment; filename=".basename($row['FileName']).";"); 
    header("Content-Transfer-Encoding: binary"); 
    header("Content-Length: ".filesize($row['FileName'])); 

    @readfile($row['FileName']); 
    exit(0); 
}else{ 
    header("Location: /"); 
    exit; 
}
```
# :cyclone:六、通过Email显示用户的Gravatar头像
```PHP
 $gravatar_link = 'http://www.gravatar.com/avatar/' . md5($comment_author_email) . '?s=32';
  echo '<img src="' . $gravatar_link . '" />';
```
# :cyclone:七、通过cURL获取RSS订阅数
```PHP
$ch = curl_init();
curl_setopt($ch,CURLOPT_URL,'https://feedburner.google.com/api/awareness/1.0/GetFeedData?id=7qkrmib4r9rscbplq5qgadiiq4');
curl_setopt($ch,CURLOPT_RETURNTRANSFER,1);
curl_setopt($ch,CURLOPT_CONNECTTIMEOUT,2);
$content = curl_exec($ch);
$subscribers = get_match('/circulation="(.*)"/isU',$content);
curl_close($ch);
```
# :cyclone:八、时间差异计算函数
```PHP
function ago($time)
{
   $periods = array("second", "minute", "hour", "day", "week", "month", "year", "decade");
   $lengths = array("60","60","24","7","4.35","12","10");

   $now = time();

       $difference     = $now - $time;
       $tense         = "ago";

   for($j = 0; $difference >= $lengths[$j] && $j < count($lengths)-1; $j++) {
       $difference /= $lengths[$j];
   }

   $difference = round($difference);

   if($difference != 1) {
       $periods[$j].= "s";
   }

   return "$difference $periods[$j] 'ago' ";
}
```
# :cyclone:九、裁剪图片
```PHP
$filename= "test.jpg";
list($w, $h, $type, $attr) = getimagesize($filename);
$src_im = imagecreatefromjpeg($filename);

$src_x = '0';   // begin x
$src_y = '0';   // begin y
$src_w = '100'; // width
$src_h = '100'; // height
$dst_x = '0';   // destination x
$dst_y = '0';   // destination y

$dst_im = imagecreatetruecolor($src_w, $src_h);
$white = imagecolorallocate($dst_im, 255, 255, 255);
imagefill($dst_im, 0, 0, $white);

imagecopy($dst_im, $src_im, $dst_x, $dst_y, $src_x, $src_y, $src_w, $src_h);

header("Content-type: image/png");
imagepng($dst_im);
imagedestroy($dst_im);
```
# :cyclone:十、检查网站是否宕机
```PHP
function Visit($url){
       $agent = "Mozilla/4.0 (compatible; MSIE 5.01; Windows NT 5.0)";$ch=curl_init();
       curl_setopt ($ch, CURLOPT_URL,$url );
       curl_setopt($ch, CURLOPT_USERAGENT, $agent);
       curl_setopt ($ch, CURLOPT_RETURNTRANSFER, 1);
       curl_setopt ($ch,CURLOPT_VERBOSE,false);
       curl_setopt($ch, CURLOPT_TIMEOUT, 5);
       curl_setopt($ch,CURLOPT_SSL_VERIFYPEER, FALSE);
       curl_setopt($ch,CURLOPT_SSLVERSION,3);
       curl_setopt($ch,CURLOPT_SSL_VERIFYHOST, FALSE);
       $page=curl_exec($ch);
       //echo curl_error($ch);
       $httpcode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
       curl_close($ch);
       if($httpcode>=200 && $httpcode<300) return true;
       else return false;
}
if (Visit("http://www.google.com"))
       echo "Website OK"."n";
else
       echo "Website DOWN";
```
# :cyclone:十一、二维数组排序算法
```PHP
/**
 * 二维数组排序算法
 * @author: 凌翔 <553299576@qq.com>
 * @DateTime 2016-05-06T20:03:38+0800
 * @param    [array]                   $arr   [待排序的数组]
 * @param    [string]                  $field [排序字段]
 * @param    [string]                  $sort  [正序SORT_ASC,倒顺SORT_DESC]
 * @return   [array]                          [排序完成数组]
 */
function array_sort($arr,$field,$sort){
    $f = array();
    foreach ($arr as $v) {
        $f[] = $v[$field];
    }

    array_multisort($f, $sort, $arr);
    return $arr;
}
```
# :cyclone:十二、递归树算法树
```PHP
/**
 * 递归数算法
 * @param array $items 传入一维数组
 * @param string $id_name id名称
 * @param string $pid_name 父级id名称
 * @param string $children_name 子级名称
 * @return array
 */
function generate_tree($items,$id_name='id',$pid_name='parent_id',$children_name='children'){
	$items_new = array();
	foreach ($items as $item) {
		$items_new[$item[$id_name]] = $item ;
	}
	$items = $items_new ;unset($items_new);
	$tree = array();
	foreach($items as $item){
		if(isset($items[$item[$pid_name]])){
			$items[$item[$pid_name]][$children_name][] = &$items[$item[$id_name]];
		}else{
			$tree[] = &$items[$item[$id_name]];
		}
	}
	return $tree;
}
```
# :cyclone:十三 、生成唯一字符串
```PHP
function createSn() {
	$code = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
	$rand = $code[rand(0,25)]
	.strtoupper(dechex(date('m')))
	.date('d').substr(time(),-5)
	.substr(microtime(),2,5)
	.sprintf('%02d',rand(0,99));
	for(
			$a = md5( $rand, true ),
			$s = '0123456789ABCDEFGHIJKLMNOPQRSTUV',
			$d = '',
			$f = 0;
	$f < 8;
	$g = ord( $a[ $f ] ),
	$d .= $s[ ( $g ^ ord( $a[ $f + 8 ] ) ) - $g & 0x1F ],
	$f++
	);
	return $d;
}
```
