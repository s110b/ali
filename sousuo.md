```php
<?
set_time_limit(0);
error_reporting(E_ALL || ~E_NOTICE);
$now=time();
function getarr($fname)
{
    $fd=fopen($fname,"r");
    $seed=0;
    while ($str=trim(fgets($fd)))
    {
        $arr=explode(" - ",$str);
        $key=$arr[0];
        $value=$arr[1];
        $listarr[$key]=$value;
        $seed++;
        //            if ($seed>2000) break;
    }
    return ($listarr);
}
function putskip($now,$key)
{
    $skip=time()-$now;
    echo "$key  - $skip\n";
}
$garr=getarr("gcount");
$marr=getarr("mcount");
$mlarr=getarr("nmlist");
$glarr=getarr("glist");
putskip($now,"START");
require 'conn.php';
$seed=0;$seed2=0;
mysql_selectdb("caoz_data");
foreach ($glarr as $gid=>$value)
{
    $seed++;
    $seed2++;
    $v=$garr[$gid];
    if ($v<5 or $v>1000000) continue;
    $listarr=explode(",",$value);
    foreach ($listarr as $key2=>$mid)
    {
        if ($marr[$mid]==1 or $marr[$mid]>1000) continue;
        $gidlist=explode(",",$mlarr[$mid]);
        foreach ($gidlist as $key3=>$togid)
        {
            if (trim($togid)=="") continue;
            if ($gid==$togid) continue;
            if ($garr[$togid]>1000000) continue;
            if ($garr[$togid]<2) continue;
            $sumlist[$gid][$togid]++;
            $tmpright=1/(sqrt($marr[$mid]+1)*sqrt($garr[$togid]+1));
            $tmpright2=1/(50+$garr[$togid]);
            $rlist[$gid][$togid]+=$tmpright;
            $nrlist[$gid][$togid]+=$tmpright2;
        }
    }
    $value=$rlist[$gid];
    $sql="delete from group_relate where fromgid=$gid";
    mysql_query($sql);

    $tmpseed=0;
    arsort($value);
    foreach ($value as $togid=>$rights)
    {
        $tmpseed++;
        $rights=$rlist[$gid][$togid];
        $rights2=$nrlist[$gid][$togid];
        if ($tmpseed==1)
        {
            $zright=$rights; //归一化
            $zright2=$rights2;
        }
        $tmpsum=$sumlist[$gid][$togid];
        if ($tmpseed>30) break;
        if ($tmpsum<2) continue;
        $rights=$rights/$zright*10000;
        $nrights=$rights2/$zright2*10000;
        $sql="insert into group_relate (fromgid,togid,sums,rights,nrights) values ('$gid','$togid','$tmpsum','$rights','$nrights')";
        mysql_query($sql);
        //echo "$sql\n";
        //echo mysql_error();
        //echo "$gid $togid $rights $tmpsum \n";
    }
    unset($rlist[$gid]);
    unset($nrlist[$gid]);
    unset($sumlist[$gid]);
    if ($seed==1000) { putskip($now,$seed2); $seed=0; }

}
?>
```