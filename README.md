<?php
/*!
* (C) vietdesigner.net
*
*/

header("Content-Type: text/plain"); // display as plain text

$url = "http://stream.anluong.info/xemlivetv/hbo_xmio.php"; // site url
$content = file_get_contents($url); // get page content html

preg_match('/responseText1 ="(.*?)"/is', $content, $match); // regular expression match

if (isset($match[1])) { // a match was found
    $playlist = explode("/", $match[1]); // split url by slash
    $playlist = end($playlist); // get filename + query (playlist.m3u8?t=...&e=...)

    $urlStream = str_replace($playlist, "", $match[1]); // remove filename + query
    $urlStream = substr($urlStream, -1) == "/" ? $urlStream : $urlStream."/"; // forced add trailing slash onto the end of url

    $contentStream = file_get_contents($match[1]); // get content file m3u8
    $contentStream = str_replace("chunklist_", $urlStream."chunklist_", $contentStream); // add full url

    // force file download
    $filename = "vds.m3u8";// downloaded filename
    header("Content-Description: File Transfer");
    header("Content-Type: application/octet-stream");
    header("Content-Disposition: attachment; filename=".$filename);
    header("Content-Transfer-Encoding: binary");
    header("Connection: Keep-Alive");
    header("Expires: 0");
    header("Cache-Control: must-revalidate, post-check=0, pre-check=0");
    header("Pragma: public");
    header("Content-Length: ".strlen($contentStream));

    echo $contentStream; // print to screen
} else echo "URL stream not found"; // a match was not found
?>
